# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page static wedding invitation website (Dr. Abhishek S & Dr. Reshma R), deployed to Netlify at `abhishma.netlify.app`. No build step, package manager, or framework — it's plain HTML/CSS/JS.

The current design direction is **"Kasavu Nights"** — a dark, editorial, cinematic Kerala treatment (deep maroon ground, kasavu gold, full-bleed photography). It was a deliberate redesign away from the earlier light "Kasavu & Ivory" look.

Note: this directory (`Abhishma`) is a subfolder inside a larger git repo rooted at the parent `Projects` directory, which holds many unrelated sibling projects. Only work within this folder unless directed otherwise.

## Files

- `index.html` — all markup + one inline `<script>` IIFE with all behaviour. Links `styles.css` and Google Fonts.
- `styles.css` — the entire hand-written design system (tokens → base → type → layout → motifs → components → sections → reduced-motion). **This is the source of truth for all styling.**
- `assets/` — `images/hero.jpg` (hero + couple portrait), `images/whatsapp.jpg` (OG/social share image), `favicon.png`, `gallery/photo1–6.jpg`. These are real files now (pulled from the live site); the site 404s cosmetically without them.
- `tailwind.min.css` — **legacy / unused.** The old design was built on this purged Tailwind file; the current design does not link it. Left in the folder for reference only — do not add references to it, and it is excluded from the deploy zip.
- Deploy artifact: `../abhishma-deploy.zip` (index.html + styles.css + assets only). Regenerate with `Compress-Archive -Path index.html, styles.css, assets -DestinationPath ..\abhishma-deploy.zip -Force`.

## Working with this codebase

- No dev server/bundler/linter/test suite. Preview by opening `index.html` or serving the folder (`python -m http.server`). Browsers cache `styles.css` hard — hard-reload (Ctrl+Shift+R) after CSS edits.
- **Style in `styles.css` with the design tokens** (`--ground`, `--gold`, `--cream`, `--maroon`, `--green`, `--foil`, the `--serif`/`--body`/`--script` font vars, etc.). Don't reintroduce utility-class styling.
- **All JS is progressive enhancement**, each `init*()` wrapped in `safe(fn, label)` so one failure never blanks the page. Modules: `initEntrance`, `initProgress`, `initNav`, `initParallax`, `initReveals`, `initCountdown`, `initCalendarLinks`, `initGallery`, `initWishForm`, `initHeartbeat`. Wrap any new enhancement in `safe(...)`.
- **Scroll reveals** use `[data-r]` (+ optional `data-delay`) toggled to `.in` by an IntersectionObserver, guarded by `html.js` so no-JS shows everything. **Never put `[data-r]` on above-the-fold content** — the hero learned this the hard way; the hero content instead uses a pure-CSS `heroRise` load animation so it always appears regardless of the observer.
- **Section order** (intentional): hero → invitation → countdown (early hook) → couple → heartbeat divider → events → gallery → wishes → compliments (near the end) → footer.
- **Ceremonial entrance** (`#entrance`, `initEntrance`): a "സ്വാഗതം / Welcome" overlay that plays once per visit (`sessionStorage` key `ar-entered`), auto-dismisses after ~2.6s or on tap/scroll/key, and is removed from the DOM after. Managed entirely by JS + `html.js` so no-JS visitors never get a stuck overlay.
- **Gallery** (`initGallery`): a native horizontal scroll-snap **film-strip** (works with no JS), enhanced with a lightbox, dot indicators, and **autoplay** — advances one photo every 4s by scrolling *only the strip* (never the page), pauses on hover/hold/manual-swipe and resumes ~0.7s after release, and pauses when off-screen or the tab is hidden. Manual scroll is always free.
- **Add to Calendar**: each `.add-to-calendar` link has a pre-encoded `.ics` `data:` URI in `href` (+ `download` + `data-cal-*` attributes) for three events. `initCalendarLinks()` overrides these on Android/ChromeOS only, opening Google Calendar's web endpoint instead. When editing an event's date/time/venue, update **both** the visible text and the matching `.ics` data URI / `data-cal-*` attributes.
- **Wishes form**: posts to Netlify Forms (`data-netlify="true"` on a hidden static form for build-time detection; the visible `#wish-form` submits via `fetch("/")` with URL-encoded body). Works only on Netlify. Submissions land in the Netlify dashboard under Forms → `wedding-wishes`.
- **Signature motifs**: the ECG heartbeat-into-heart divider (`initHeartbeat`, "two hearts one rhythm" — nod to both being doctors), CSS-only falling jasmine `.petal`s in the hero + entrance, the woven `.kasavu-rule` and `.corners` gold brackets, the gold-foil (`--foil`) buttons/accents, and the top scroll-progress bar. All decorative glyphs are the inline `<symbol>` line-icons (`#ic-…`, class `.ico`) — never emoji.
- External deps: Google Fonts (Cormorant Garamond / Inter / Pinyon Script / Noto Serif Malayalam), Cloudflare Web Analytics beacon. No local node_modules.

## Content rule

Treat all copy, image filenames, and links (maps, `.ics` data, Netlify form) as fixed — only change them when explicitly asked. See the memory note `abhishma-no-touch-content`.

## Deploy

Deploys to the existing `abhishma.netlify.app` (its URL is hard-coded in canonical/OG/Twitter/share-link — deploying elsewhere breaks the share preview unless those are updated). Simplest path: drag `../abhishma-deploy.zip` onto the site's Deploys tab in the Netlify dashboard. A drag-drop deploy replaces the whole site, so the zip must contain everything (it does).
