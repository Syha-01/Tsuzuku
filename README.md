# Tsuzuku

Release host for **Tsuzuku** — an Android anime app on the AniKoto source, with
the scraper embedded on the phone. No account needed and no server between you
and the episodes: nothing to go down between your phone and the source. The
social side — rankings, watch parties, friends, backup — is there when you
want it and off until you do.

## Direct download

| Build | Version | Released | Size | |
| --- | --- | --- | --- | --- |
| **Mobile (arm64)** — phone app (Android). Rankings, watch parties with chat, friends, offline downloads, MyAnimeList and AniList sync, picture controls, picture-in-picture, subtitles translated on the phone, sub and dub. | v5.0.0 | 16 Oct 2026 | ~57 MB | [![Download APK](https://img.shields.io/badge/Download-APK-2ea44f?style=for-the-badge&logo=android&logoColor=white)](https://github.com/Syha-01/Tsuzuku/releases/download/v5.0.0/tsuzuku-v5.0.0-arm64.apk) |
| **Universal** — the same app, for older 32-bit phones as well. | v5.0.0 | 16 Oct 2026 | ~85 MB | [![Download Universal](https://img.shields.io/badge/Download-Universal-555555?style=for-the-badge&logo=android&logoColor=white)](https://github.com/Syha-01/Tsuzuku/releases/download/v5.0.0/tsuzuku-v5.0.0-universal.apk) |

Every build is also listed on the [**Releases**](https://github.com/Syha-01/Tsuzuku/releases) page.

### New in 5.0.0

Tsuzuku goes social. **Rankings** count every episode you finish: climb eight
ranks, top the weekly boards, take on a new challenge each week and collect
fourteen secret titles. **Watch parties** keep everyone in sync with the host,
with chat and reactions that show even in full screen. **Follow friends** to
see their hours and what they are watching, **back up your list** across
phones, and tune the picture with **brightness, contrast, colour and
sharpness**. Full notes on the
[release page](https://github.com/Syha-01/Tsuzuku/releases/tag/v5.0.0).

## Screens

<p align="center">
  <img src="screenshots/03-player.webp" width="640" alt="The player in landscape with its controls showing">
</p>

<p align="center"><b>The player</b> — landscape whichever way the rest of the app is set. Quality,
audio track and subtitles sit in the controls rather than behind a settings gear, beside a
<code>+85s</code> button for clearing an opening. Everything else is a gesture.</p>

<table>
<tr>
<td align="center" width="25%"><img src="screenshots/01-home.webp" width="180" alt="Home"><br><b>Home</b><br><sub>Spotlight, Continue Watching and a ranked grid</sub></td>
<td align="center" width="25%"><img src="screenshots/02-series-detail.webp" width="180" alt="Series detail"><br><b>Series detail</b><br><sub>Rating, resume, and a countdown to the next episode</sub></td>
<td align="center" width="25%"><img src="screenshots/04-download-picker.webp" width="180" alt="Downloading"><br><b>Downloading</b><br><sub>Pick a batch; only the sizes it comes in</sub></td>
<td align="center" width="25%"><img src="screenshots/05-add-to-list.webp" width="180" alt="Tracking"><br><b>Tracking</b><br><sub>Watching, Later, Done or Dropped</sub></td>
</tr>
<tr>
<td align="center" width="25%"><img src="screenshots/06-saved.webp" width="180" alt="Saved"><br><b>Saved</b><br><sub>Split by status, with progress against the episode count</sub></td>
<td align="center" width="25%"><img src="screenshots/07-schedule.webp" width="180" alt="Schedule"><br><b>Schedule</b><br><sub>Estimated air times in your timezone</sub></td>
<td align="center" width="25%"><img src="screenshots/08-search-browse.webp" width="180" alt="Search &amp; browse"><br><b>Search &amp; browse</b><br><sub>Eleven listings and forty-seven genres</sub></td>
<td align="center" width="25%"><img src="screenshots/09-downloads.webp" width="180" alt="Downloads"><br><b>Downloads</b><br><sub>Saved episodes, playable with no internet</sub></td>
</tr>
</table>

<details>
<summary><b>Settings</b> — eight sections, each one folds open on its own</summary>

<br>

<table>
<tr>
<td align="center" width="25%"><img src="screenshots/10-settings-playback.webp" width="180" alt="Playback"><br><b>Playback</b><br><sub>Subtitle and autoplay defaults</sub></td>
<td align="center" width="25%"><img src="screenshots/11-settings-display.webp" width="180" alt="Display"><br><b>Display</b><br><sub>Orientation, and where Saved opens</sub></td>
<td align="center" width="25%"><img src="screenshots/12-settings-subtitles.webp" width="180" alt="Subtitles"><br><b>Subtitles</b><br><sub>Size, colour and background</sub></td>
<td align="center" width="25%"><img src="screenshots/13-settings-downloads.webp" width="180" alt="Downloads"><br><b>Downloads</b><br><sub>Default quality, segments at once, storage used</sub></td>
</tr>
<tr>
<td align="center" width="25%"><img src="screenshots/14-settings-catalogue.webp" width="180" alt="Catalogue"><br><b>Catalogue</b><br><sub>Scraper version, and clearing its cache</sub></td>
<td align="center" width="25%"><img src="screenshots/15-settings-connection.webp" width="180" alt="Connection"><br><b>Connection</b><br><sub>Names the first step of the chain that fails</sub></td>
<td align="center" width="25%"><img src="screenshots/16-settings-app.webp" width="180" alt="App"><br><b>App</b><br><sub>Over-the-air updates, clearing history</sub></td>
<td align="center" width="25%"><img src="screenshots/17-settings-whats-new.webp" width="180" alt="What's new"><br><b>What's new</b><br><sub>Release notes, newest first</sub></td>
</tr>
</table>

</details>

## What it does

- **Rankings** — join with a name, or your AniList or MyAnimeList account, and every episode you finish counts towards eight ranks, boards for today, this week and this month, a weekly challenge, and fourteen secret titles to collect. Your picture on the boards is any AniList character or a photo of your own.
- **Watch parties** — start one from a series page or the player and everyone plays, pauses and skips with the host. Chat with quick emoji and reactions, shown in full screen too. Share a link or a code, or make it public so anyone can join from Home.
- **Friends** — follow people from the boards to see their hours beside yours and what they are watching right now, if they share it. Share a list of shows with a link.
- **Picture controls** — brightness, contrast, colour and sharpness in the player, or one tap on Vivid.
- **Backup** — saved shows, progress and settings kept under your name and brought back on a new phone.
- **Edit Home** — the lists and genres you want, in your order.
- **Episodes saved to the phone** — pick a batch, or hold one episode to grab just that one. They play with no connection at all, subtitles included, and the progress counts the same as if you had streamed it. Downloads keep running in the background and pick up where they stopped if the app is killed.
- **Subtitles translated on the phone** — pick Spanish or Japanese in the player's subtitle menu and the track is translated on the device itself. The language pack comes down once, after which it needs no connection. An episode can be saved with the translated track baked in, so it reads the same offline.
- **Episodes written out to a folder** — saved episodes go to Downloads, an SD card or anywhere else on the phone, one at a time or all at once, with the subtitles inside the video (MKV) so they show in any player, on the phone or a computer.
- **Quality switching that keeps playing** — pick a rendition mid-episode and the picture changes while the timeline doesn't. The choice is remembered across episodes.
- **Sub and dub as separate lists**, switchable mid-watch.
- **Subtitles rendered by the app** — size, colour and background are yours to set, and cues that drift against the video are pulled back into line.
- **Player gestures** — double tap either side to seek and repeat taps stack into one jump, hold anywhere for 2×, drag on the left for brightness and on the right for volume. `+85s` clears a cold open plus the OP in one press.
- **MyAnimeList and AniList** — sign in to bring your list with you and keep what you watch in step with it.
- **Picture-in-picture** — go home mid-episode and it keeps playing in a corner, subtitles included.
- **Continue watching, a saved list with progress, genre browsing and the airing schedule.**
- **Updates over the air** — fixes arrive on their own; Settings → *Check for update* pulls one on demand.

## Which device

The main APK is built for 64-bit ARM phones — virtually every Android phone
from 2016 onward. Older 32-bit phones take the universal build, the same
version. Intel-based devices and Android Studio emulators are not
covered.

> Sideloaded APKs: when installing, allow "Install unknown apps" for your
> browser or file manager. Android's "this file may harm your device" prompt is
> normal for any APK downloaded outside the Play Store.

### What goes online

Only what you choose. Joining the rankings sends the show, episode number and
length of each episode you finish. Sharing what you watch, the backup and
problem reports are switches in **Settings → Online**, all off to begin with.
An anonymous install count — a random id, nothing about you — can be switched
off there too.

### App won't load, or "can't reach the source"?

Some networks and internet providers block streaming content. If the app can't
reach the source on your connection, a VPN usually settles it.

## For developers

**[MegaPlay stopped returning a stream](megaplay-fix.md)** — September 2026.
`/stream/getSources` swapped the stream URL for an encrypted blob, so anything
reading `sources.file` got nothing and reported it as a missing server. The fix
is an endpoint, not a cipher: MegaPlay's own player had already moved to
`/stream/getSourcesNew`, which still answers in plaintext.

Written up for anyone else scraping AniKoto or MegaPlay — what broke, why it
only appeared to affect some episodes, the drop-in replacement for
`getSources`, and the CDN host that 403s video while still serving subtitles.
There is [an offline copy](megaplay-fix.html) too.
