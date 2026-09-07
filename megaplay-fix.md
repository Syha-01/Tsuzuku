# MegaPlay stopped returning a stream

**6 September 2026** · anikototv.to → megaplay.buzz

Notes for anyone else scraping AniKoto or MegaPlay. If your app started failing
with "no servers available" while the site still plays fine in a browser,
nothing is blocked — the endpoint changed shape.

---

## Summary

`/stream/getSources` stopped returning the stream URL and started returning an
encrypted blob instead. Any extractor reading `sources.file` got `null`, which
surfaces as a missing server rather than a changed format.

**The fix is not decryption.** MegaPlay's own web client had already moved to a
second endpoint, `/stream/getSourcesNew`, which still returns the file in
plaintext. Asking for that first restores playback.

---

## The symptom

Episodes 1–9 and 11 of a twelve-episode series played. Episodes 10 and 12 said
no servers were available — while the same episodes played fine on the site.

That split is the most useful thing in the whole incident, because it rules out
the obvious explanations. Not the network, not DNS, not an ISP block, not the
site being down — those take out a whole series, not two episodes of it.

## The chain

The video never comes from the site you browse. Four hops sit between a tap and
a picture, each able to fail on its own:

```
anikototv.to    /ajax/server/list      → which servers exist for this episode
anikototv.to    /ajax/server?get=id    → a link id becomes an embed URL
megaplay.buzz   /stream/<path>         → the embed page, carrying a numeric data-id
megaplay.buzz   /stream/getSources     → the data-id becomes a stream URL
<cdn host>      master.m3u8            → the playlist, then segments
```

Checking the site in a browser tests hop one, which was never the problem.

## Root cause

Hop four changed shape. It used to answer:

```json
{ "sources": { "file": "https://…/master.m3u8" }, "tracks": [ … ] }
```

It now answers:

```json
{ "tracks": [ … ], "t": 1, "intro": {…}, "outro": {…},
  "server": 4, "enc": "<base64url blob>" }
```

`sources` is gone, replaced by `enc`.

## Why only some episodes broke

The site lists three server names per episode, but they are not three sources:

| Label | Resolves to | Backend |
| --- | --- | --- |
| Vidstream-2 | `megaplay.buzz/stream/s-2/<id>/sub` | `server: 4` |
| HD-1 | the same URL with `?s=tcdn` | `server: 4` — same stream |
| VidPlay-1 | `vidtube.site/stream/…` | `server: 6` — genuinely separate |

Vidstream and HD are one stream listed twice; both embeds return the same
`data-id`. Only VidPlay is a different chain, and it never stopped returning
plaintext.

So every episode that still played was quietly running on VidPlay. Episodes 10
and 12 simply were not listed with a VidPlay entry, leaving them with two links
onto the one broken backend.

> When a failure splits along an axis that looks arbitrary, enumerate what each
> case actually resolves to rather than trusting the labels. Three server names
> hiding two backends is why "try another server" appeared to work for some
> episodes and not others.

## The fix: a second endpoint

MegaPlay's own player kept working throughout, which means it was getting a URL
somehow. It was calling a different endpoint:

| Endpoint | Returns |
| --- | --- |
| `/stream/getSources` | `sources: null` plus `enc` |
| `/stream/getSourcesNew` | `sources.file` in plaintext, no `enc` |

Ask for the new one first and keep the old one as a fallback:

```js
async function getSources(base, embed, id) {
    const headers = {
        'User-Agent': UA,
        Referer: embed,
        'X-Requested-With': 'XMLHttpRequest',
    };
    try {
        const rNew = await fetch(`${base}/stream/getSourcesNew?id=${id}`, { headers });
        const jNew = JSON.parse(await rNew.text() || 'null');
        if (jNew && (jNew.sources || jNew.enc)) return jNew;
    } catch { /* fall through */ }

    const r = await fetch(`${base}/stream/getSources?id=${id}`, { headers });
    try { return JSON.parse(await r.text() || 'null'); } catch { return null; }
}
```

The guard matters: a 404 body fails `JSON.parse` and falls through cleanly, and
a response with neither field is treated as no answer rather than a valid empty
one.

Safe for other hosts. VidPlay's `getSourcesNew` returns byte-identical content
to its `getSources` — same file, same tracks — so it returns on the first call
and costs no extra round trip.

## The CDN trap

Getting a URL back is not the same as getting a playable one. The legacy path
points at a host that refuses video:

| Host | video `master.m3u8` | subtitle `.vtt` |
| --- | --- | --- |
| `cdn.imgnex.top` | **403** | 200 |
| `ncdn.imgnex.top` | 200 | 200 |

Video is blocked on the bare host while subtitles still serve, so the rewrite is
video-only:

```js
file = file.replace(/^https?:\/\/cdn\.imgnex\.top\//i, 'https://ncdn.imgnex.top/');
```

Rewriting subtitle URLs too would be harmless here but wrong in principle —
they are not blocked, and the narrower rule is the one that stays correct when
only one of the two changes.

`getSourcesNew` returns a different host again, so on the working path this
rewrite never fires.

## Wiring it in

One line, and the ordering in it is the whole design:

```js
const file = j?.sources?.file
    ?? j?.sources?.[0]?.file
    ?? (typeof j?.enc === 'string' ? decrypt(j.enc)?.file ?? null : null);
if (!file) return [];
```

Plaintext is tried first, deliberately:

- hosts that never changed keep their existing path and cost nothing;
- the fallback runs only where it is actually needed;
- and if a host reverts to plaintext, it starts working again with no code change.

Everything downstream — splitting the master playlist into a quality ladder,
subtitle tracks, hardsub flags — keys off the resulting `file` and needs no
knowledge of how it was obtained.

## A failure mode worth naming: the load-time crash

If you do add a decryption fallback, watch what you pull in with it. A crypto
library still wrapped in a UMD header — the pattern probing for a global to
attach itself to, ending in `})(this)` or `})(_root)` — will break under Hermes
and under any ES-module transpile, where top-level `this` is `undefined`.

Reaching for a property on it throws *while the module is being imported*, which
means:

- the failure happens before any exported function can run;
- every importer of that module fails with it;
- and the visible symptom is not "decryption failed" but "decryption never happened".

A module that throws at import time produces the same silence as a module that
was never called. If your diagnostics cannot tell those apart, they will send
you looking in the wrong half of the chain.

The fix is not to carry UMD wrappers into a bundler-managed module at all. A
focused implementation of just the primitive you need has no global scope to
reach for and nothing to fail on load.

## Diagnostics that tell the truth

Two things worth building in, both learned the hard way here.

**Separate the causes of an empty result.** "The server had nothing" and "the
extraction failed on what it had" want opposite fixes. A changed format is not a
dead server. If both print the same message you cannot tell which half of the
chain to look at.

**Give a replacement message different wording.** The first version of that
fallback string was byte-identical to the message it replaced, which meant an
old build and a new build printed the same text — so the diagnostic could not
even establish which code was running.

**Don't stop at the first success.** A test that returns as soon as one server
answers tells you playback works today and nothing about whether the rest of the
chain still would. Probe every server, and exercise any fallback explicitly — a
fallback nobody has run is a guess.

## Operational notes

- **Don't hardcode CDN hostnames.** They come out of the response and they
  rotate — this incident alone saw `cdn.kryntal.top`, `cdn.imgnex.top`,
  `megap.shiora.site` and `megap.norami.top`. Only the embed host patterns are
  worth matching, and loosely.
- **Referer matters at the CDN.** It 403s without the one the player sends, so
  probe it exactly as playback will.
- **A well-formed manifest is not a playing video.** Fetch a segment and check
  you got media back. A playlist can return 200 and contain nothing but dead
  URLs — we hit one that was 143 segments of ad-CDN links, every one a 403.

## Method, generalised

What made this tractable was not knowing anything about the cipher:

1. **Reproduce the exact chain outside the app.** Curl each hop in order with
   the same headers. Caching, retries and racing hide which hop actually failed.
2. **Find the discriminator.** Something works and something doesn't. Enumerate
   what each case resolves to until the difference is concrete.
3. **Compare against the working client.** The site's own player kept playing,
   so it was doing something different. That difference was the fix, and it was
   cheaper than the cryptography.
4. **Verify to the bytes.** Fetch a segment and confirm it is media, not an
   error page.

> Considerable effort went into the encryption before anyone checked whether the
> site had simply moved endpoints. It had. Look for the path the working client
> takes before reverse-engineering the one it abandoned.
