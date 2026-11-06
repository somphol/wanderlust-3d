# Agent notes — Wanderlust 3D

## What it is

A static, zero-build travel atlas for 15 destinations across Japan, Italy and Costa Rica. It produces
a single-page site with a hero slider, live filters, destination modals, three signature routes and a
persistent `localStorage` itinerary. It is not a booking engine, CMS or backend; all content is
editorial data in `data.js`. No runtime package install is required.

## Commands

```sh
python3 -m http.server 8080     # simplest local preview
npm run serve                   # Node static server, PORT=9000 to change
npm install                     # dev-only: puppeteer-core for the release gate
npm run verify                  # 81 headless checks; must pass before release
npm run verify:images           # network: HEAD every unique Unsplash image
npm run screenshots             # regenerate screenshots/*.jpg from the live app
```

`CHROME_PATH=/path/to/chrome` overrides Chrome discovery. Node 20+ is required for the dev scripts.

Deploy: push to `main`; `.github/workflows/pages.yml` deploys the repository root to GitHub Pages.
Pages must be enabled once with **Source: GitHub Actions**.

## Architecture

- `data.js` — frozen source of truth: `meta`, `regions`, `destinations`, `routes`. Image URLs are bare
  Unsplash CDN URLs; the renderer appends `auto=format&fit=crop&w=…&q=…`.
- `script.js` — one IIFE. Rendering order: cache elements → load trip from `localStorage` → render
  region tabs and interest chips → render routes → `render()` (tabs, accent, brief, filters, grid,
  counts) → bind delegated events → reveal observer → hero autoplay → hash deep link.
- `style.css` — all visual behaviour. Design tokens live at the top under `:root`; region accents are
  written to CSS variables by `applyAccent()`. The Tailwind Play CDN is loaded but **not** the source
  of layout.
- `index.html` — semantic shell plus an inline SVG symbol sprite (`#i-*`). Dynamic regions, cards,
  routes, modal content and drawer items are rendered by `script.js`.
- `scripts/verify.mjs` — self-contained static server + Chrome test suite. This is the release gate.
- `.github/workflows/pages.yml` — uploads the repository root as a Pages artifact and deploys.

State lives in four places only: `state.region/query/tags/budget/sort` (in memory), the trip object in
`localStorage` under `wanderlust.trip.v1`, the destination hash `#/destination/<id>`, and the current
hero slide.

## Decisions and constraints

- **Exactly 3 regions × 5 destinations.** IDs are unique and stable; they are used in URLs,
  `localStorage` and CSS data attributes. Do not rename an existing ID casually.
- **Budget breakdown must sum to `dailyBudget`.** `npm run verify` enforces this.
- **Every destination needs 3 gallery images and an `imageAlt`.** All photographs must resolve on
  `images.unsplash.com`; `npm run verify:images` is the audit.
- **No build step.** Do not introduce a bundler, framework or runtime npm dependency. Dev tooling is
  fine in `package.json`; the deployed site must remain the four core files.
- **Tailwind CDN is deliberate but non-essential.** The original brief asked for it. If you remove the
  two `<script>` tags in `index.html`, the site is unchanged except for the development warning.
- **`localStorage` schema is versioned** as `{ version: 1, savedAt, items: { id: { nights, order, addedAt } } }`.
  `loadTrip()` accepts the bare legacy object for tolerance. Keep old keys working.
- **Hash routing is `#/destination/<id>`.** Use `replaceState`, not `pushState`; the modal close path
  clears the hash. `popstate` is handled for browser navigation.
- **Accessibility is part of the contract**: focus trap in modal/drawer, Escape to close, focus
  restored, `aria-live` counts, `aria-pressed` filters, visible focus rings, reduced-motion support.
- **Screenshots are generated** by `scripts/screenshots.mjs`. Do not hand-edit them.
- **`state.json` and `STATE.md` are provenance snapshots.** Update them after a new build; do not
  pretend the token numbers are live.
- **No secrets** belong in this repo. Deployment uses the local credential helper; never commit a token.

## Known issues

- Tailwind Play CDN logs “cdn.tailwindcss.com should not be used in production”. It is a requirement
  artefact, not a functional dependency. Removing it is safe.
- Images are hotlinked. First render needs network; a failed image is hidden and its container keeps a
  gradient background.
- Budgets, seasons and attraction details are editorial approximations, not live inventory.
- The itinerary is per browser profile. There is no account or sync.
- `npm run verify` needs Chrome/Chromium. `npm run screenshots` additionally needs network for images.
- There is no service worker or offline cache; the manifest provides install metadata only.
- GitHub Pages cannot set custom response headers (CSP, HSTS); document that if security requirements
  change.

## Continuity

- Live: <https://somphol.github.io/wanderlust-3d/>
- Repository: <https://github.com/somphol/wanderlust-3d>
- Local source of truth: `~/Projects/wanderlust-3d`
- Skill source: `~/.config/opencode/skills/wanderlust-3d-studio`
- Skill backup/catalog: `~/Projects/opencode-skills` → <https://somphol.github.io/opencode-skills/>
- Build provenance: `state.json` / `STATE.md` (session, model, elapsed time, tokens, verification)
- Session: built 6 November 2026 with OpenCode 2.2.5 and DeepSeek V4.1 Flash
  (`opencode-go/deepseek-v4.1-flash`).
