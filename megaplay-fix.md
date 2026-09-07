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

**The fix is an endpoint, not a cipher.** MegaPlay's own web client had already
moved to `/stream/getSourcesNew`, which still returns the file in plaintext.
Asking for that first restores playback.

## Root cause

`/stream/getSources` used to answer:

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

So every episode that still played was quietly running on VidPlay. Episodes
listed without a VidPlay entry were left with two links onto the one broken
backend, and those are the ones that died.

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

Keep reading plaintext first and treat anything else as the fallback:

```js
const j = await getSources(base, embed, id);   // new endpoint, then legacy
const file = j?.sources?.file ?? j?.sources?.[0]?.file ?? null;
if (!file) return [];
```

That ordering is deliberate:

- hosts that never changed keep their existing path and cost nothing;
- the fallback runs only where it is actually needed;
- and if a host reverts to plaintext, it starts working again with no code change.

Everything downstream — splitting the master playlist into a quality ladder,
subtitle tracks, hardsub flags — keys off the resulting `file` and needs no
knowledge of which endpoint produced it.

## Operational notes

- **Don't hardcode CDN hostnames.** They come out of the response and they
  rotate — this incident alone saw `cdn.kryntal.top`, `cdn.imgnex.top`,
  `megap.shiora.site` and `megap.norami.top`, the last two within hours of each
  other. Only the embed host patterns are worth matching, and loosely.
- **Referer matters at the CDN.** It 403s without the one the player sends, so
  probe it exactly as playback will.
- **A well-formed manifest is not a playing video.** Fetch a segment and check
  you got media back. A playlist can return 200 and contain nothing but dead
  URLs — one we hit was 143 segments of ad-CDN links, every one a 403.
- **An empty result and a changed format look identical.** Reading `sources.file`
  and getting `null` reports as "this server has nothing", which is what sent
  this particular hunt into the wrong half of the chain for a while.

---

## The legacy path

None of the above needs the `enc` payload decoded — `getSourcesNew` makes that
unnecessary, and it is the route worth taking.

If you do need the legacy endpoint decoded for something, the key is not
published here. Open an issue on this repo and I'll share it directly.
