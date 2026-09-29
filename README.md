# Pecha Kucha Timer

A tiny, dependency-free web app for **Pecha Kucha** talks (20 slides × 20 seconds):
it embeds your Google Slides so they auto-advance every 20s — GIFs and all — with a
synced countdown ring in the corner. Built for screen-sharing.

**Live:** https://io-ogni.github.io/pecha-kucha-timer/

## Usage

1. Publish your deck: Google Slides → **File → Share → Publish to web → Embed**, copy the link.
2. Open the app, paste the link, set slides × seconds (default 20 × 20), press **Start**.
3. **Share this browser tab** in Zoom/Meet. Slides auto-advance; GIFs animate.
4. If the countdown isn't hitting 0 exactly when a slide flips, tap **Space** once as a slide changes to lock it. `←/→` nudge ±0.25s.
5. **Un-publish the deck when you're done.**

Keyboard: `Space` align · `←/→` nudge · `T` hide timer · `F` fullscreen.

## How it's built

- **Single static HTML file.** Vanilla JS + inline CSS. No framework, no build step, no dependencies, no backend, no analytics. The only external resource loaded is your own Google Slides iframe.
- **Hosting:** GitHub Pages, served from `main` root. Push = live.

### Slides
Your published deck is embedded in an `<iframe>`. The src is built at runtime: `/pub` → `/embed`
(only `/embed` is frame-able), plus forced `start=true&loop=false&delayms=<sec×1000>`. That
`delayms` drives Google's own player to auto-advance every 20s (and it's the live Google
renderer, so GIFs animate). The pasted query is stripped and replaced, so it works regardless
of the publish-dialog settings.

### Timer
A vanilla `requestAnimationFrame` countdown anchored to a single timestamp
(`startAt = performance.now()`). Slide number and remaining seconds are **derived from elapsed
time** (`floor(elapsed / 20)`) rather than incremented, so it's **drift-free**. Drawn as an SVG
ring via `stroke-dashoffset`, with color states (calm → amber at 5s → red + pulse at 3s).

### Sync
Cross-origin means JS can't read or control Google's slideshow clock, so perfect auto-sync is
impossible. The timer starts on your click; one **Space** tap on the first real transition
re-anchors `startAt`; `←/→` nudge. Both run at exactly 20s, so a single alignment holds for the
whole talk.

### Details
- `navigator.wakeLock` keeps the screen awake.
- The iframe is `calc(100% + 46px)` tall so Google's bottom branding bar is clipped off-screen.
- No auto-fullscreen (some browsers flip the canvas vertically in OS fullscreen) — share the tab.

## Privacy

- Runs **entirely in your browser.** The deck link you paste stays on your device — never
  uploaded, stored, or sent anywhere, and **not in this app's code**.
- **Nothing is saved:** no cookies, no localStorage, no analytics. The repo is public but
  contains only code — zero personal links.
- The one cost: to auto-advance at 20s, Google requires the deck to be *published to web*.
  Publish it temporarily and **un-publish after your talk**.

## License

Free to use. Built by [Ioana Ognibeni](https://ioana-ognibeni.eu).
