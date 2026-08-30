# Tsuzuku

Release host for **Tsuzuku** — an Android anime app on the AniKoto source, with
the scraper embedded on the phone. No account, no tracking, and no server in
between: nothing to go down between your phone and the source.

## Direct download

| Build | Version | Released | Size | |
| --- | --- | --- | --- | --- |
| **Mobile (arm64)** — phone app (Android). Quality switching mid-episode, sub and dub, styleable subtitles. | v1.0.1 | 29 Aug 2026 | ~SIZE MB | [![Download APK](https://img.shields.io/badge/Download-APK-2ea44f?style=for-the-badge&logo=android&logoColor=white)](https://github.com/Syha-01/Tsuzuku/releases/latest/download/tsuzuku-v1.0.1-arm64.apk) |

Every build is also listed on the [**Releases**](https://github.com/Syha-01/Tsuzuku/releases) page.

## What it does

- **Quality switching that keeps playing** — pick a rendition mid-episode and the picture changes while the timeline doesn't. The choice is remembered across episodes.
- **Sub and dub as separate lists**, switchable mid-watch.
- **Subtitles rendered by the app** — size, colour and background are yours to set, and cues that drift against the video are pulled back into line.
- **Player gestures** — double tap either side to seek and repeat taps stack into one jump, hold anywhere for 2×, drag on the left for brightness and on the right for volume. `+85s` clears a cold open plus the OP in one press.
- **Continue watching, a saved list with progress, genre browsing and the airing schedule.**
- **Updates over the air** — fixes arrive on their own; Settings → *Check for update* pulls one on demand.

## Which device

The APK is built for 64-bit ARM phones — virtually every Android phone from
2016 onward. There is no universal build: 32-bit phones, Intel-based devices
and Android Studio emulators are not covered.

> Sideloaded APKs: when installing, allow "Install unknown apps" for your
> browser or file manager. Android's "this file may harm your device" prompt is
> normal for any APK downloaded outside the Play Store.

### App won't load, or "can't reach the source"?

Some networks and internet providers block streaming content. If the app can't
reach the source on your connection, a VPN usually settles it.
