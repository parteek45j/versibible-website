# VersiBible — showcase website

A single-page, fully static marketing site for the VersiBible iOS app. It presents the product,
its real screens, and its pricing. **It sells nothing.** There is no backend, no checkout, no
sign-up, no form, no analytics, and no third-party request of any kind.

---

## Run it locally

There is no build step. Any static file server works:

```bash
python3 -m http.server 8790
```

Then open <http://localhost:8790>. Opening `index.html` directly with `file://` also works, but a
server is closer to production.

## Deploy

The whole folder is the site — upload it as-is.

- **Vercel** — `npx vercel deploy --prod` from this folder, or drag the folder onto
  vercel.com/new. No framework preset; output directory is the folder root.
- **Netlify** — drag the folder onto app.netlify.com/drop, or connect the repo with build command
  empty and publish directory set to this folder.
- **GitHub Pages** — commit the folder to a repo and point Pages at the branch root (or `/docs`
  if you move it there).

Nothing needs environment variables, a Node runtime, or a build cache.

## What's in here

```
index.html          The whole site. One scroll: hero → promise → how it works →
                    free features → Plus → philosophy → pricing → footer.
privacy.html        Plain-language privacy page, mirroring the app's Privacy screen.
styles.css          All styles. Brand tokens are CSS variables at the top.
site.webmanifest    Web app manifest (theme + background #FAF7F2).
assets/
  fonts/            Newsreader + Instrument Sans, self-hosted as woff2 (~172 KB total).
  screenshots/      Real screenshots captured from the app in the iOS Simulator.
  favicon.ico, favicon-16/32.png, apple-touch-icon.png, icon-192/512.png
  og-image.png      1200×630 share card.
  wick.png          The full-size app mark (1024×1024), kept for regenerating icons.
```

### Fonts are self-hosted on purpose

The site loads **zero** third-party resources, so the Google Fonts `<link>` was replaced with
local `@font-face` woff2 files converted from the same families the app bundles
(`@expo-google-fonts/newsreader`, `@expo-google-fonts/instrument-sans`). Both are SIL Open Font
License, which permits self-hosting.

## Brand tokens

Transcribed from the app's `src/theme/tokens.ts`. Change them in one place — the `:root` block in
`styles.css`.

| Token | Hex | Used for |
|---|---|---|
| Paper | `#FAF7F2` | Page background |
| Ink | `#1C1917` | Primary text, dark buttons |
| Amber | `#C8781F` | The single accent |
| Flame | `#E8A33D` | The lamp flame, night-band accents |
| Glow | `#F6EBDC` | The wash behind the promise band |
| Night | `#14110F` | Pricing band and footer |
| Night surface | `#1E1A17` | Cards on the night bands |
| Border | `#E7E1D8` | Hairlines |

Type: **Newsreader** (serif) for display and scripture — italic 300 for verse text — and
**Instrument Sans** for everything interface.

## Rules this site follows

- **Showcase only.** Every button either scrolls the page or points at the "Coming to the App
  Store" chip in the pricing band. Nothing takes payment or collects data.
- **No testimonials.** The app ships with `HAS_REAL_TESTIMONIALS = false` in `src/data/content.ts`
  because there are no real quotes yet. There is a commented placeholder in `index.html` just
  above the pricing section — drop real, attributable quotes there when they exist. Do not invent
  any.
- **No invented product claims.** Feature copy describes screens that exist. Where a feature is
  not shipped yet (word-by-word Greek and Hebrew in Deep Study, which the app marks *Coming
  soon*), the site says so.
- **Prices mirror the app.** `$49.99/yr` and `$9.99/mo`, with the free tier and trial fine print
  taken from `src/data/content.ts`.

## Regenerating the screenshots

Screenshots live in `assets/screenshots/` at 414×900 (downscaled from 1206×2622 retina captures).
To refresh them, run the app in the iOS Simulator, complete onboarding once so the demo progress
state seeds, then:

```bash
xcrun simctl io booted screenshot assets/screenshots/today.png
```

Downscale afterwards so each file stays comfortably under 150 KB:

```bash
sips -Z 900 assets/screenshots/*.png
```

The Plus screens (Deep Study, literacy map) need a Plus entitlement to view. The screenshots here
were taken by flipping `subscription` to `plus` in the simulator's AsyncStorage snapshot
(`.../Library/Application Support/com.versibible.app/RCTAsyncLocalStorage_V1/manifest.json`) — no
purchase was made.

## Regenerating the icons and share card

All derived from the app mark at `assets/wick.png` (originally `assets/icon.png` in the app repo):

```bash
sips -s format png -z 192 192 assets/wick.png --out assets/icon-192.png
```

`favicon.ico` and `og-image.png` were generated with Pillow; the source snippet is in this repo's
git history, or regenerate them from `wick.png` with any image tool. Keep `theme_color` and
`background_color` at `#FAF7F2` in `site.webmanifest` and the `<meta name="theme-color">` tag.

## Accessibility notes

- Every screenshot has descriptive alt text naming what is on the screen.
- Images carry explicit `width`/`height` so nothing shifts while loading.
- Scroll reveals are disabled under `prefers-reduced-motion: reduce`, and a `<noscript>` block
  makes all content visible with JavaScript off.
- Focus rings are amber and always visible on keyboard focus.
