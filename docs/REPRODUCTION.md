# Reproduction and handoff

This document exists so the build can be understood, audited or recreated after the original session
is gone. Read [AGENTS.md](../AGENTS.md) first for the one-minute operational summary; this is the long
version.

## Original brief (condensed)

Build a production-ready tourism and travel exploration web application named **“Wanderlust 3D: Global
Destination Explorer”** with vanilla HTML5, CSS3, ES6 and Tailwind CSS / modern CSS variables,
deployable to GitHub Pages.

Required:

- 3 featured regions/countries, each with exactly 5 curated destinations (15 total)
- destination name, tagline, royalty-free high-resolution Unsplash image, best time, daily budget,
  language, vibe tags, overview, 3 must-see attractions and local cuisine
- full-screen hero with a dynamic background slider, tagline and search
- region tabs/cards, responsive destination grid
- immersive destination modal with gallery, itinerary tips, cultural notes and weather/season advice
- “Add to Trip” wishlist persisted in `localStorage`
- real-time search by keyword, interest tag and budget
- glassmorphism, warm/emerald/sunset palette, hover transitions, fully responsive
- zero external build dependencies; cleanly separated `index.html`, `style.css`, `script.js`
- professional release documentation, state file, credits, skill backup and GitHub publication

The session also required: avoid repeated Keychain prompts, document everything another agent needs,
record elapsed time and token use, credit OpenCode and the model, and turn the process into a reusable
skill published to GitHub.

## Environment used

| Item | Value |
| --- | --- |
| Date | 6 November 2026 (America/Los_Angeles) |
| OS | macOS (darwin) |
| Node | v26.13.0 |
| npm | 11.20.0 |
| Browser | Google Chrome (headless via `puppeteer-core`) |
| GitHub CLI | not installed; GitHub REST API + credential helper used |
| Project path | `~/Projects/wanderlust-3d` |
| Session ID | `ses_e5122d766ffeIjJBL2QxOOo3WO` |
| Model | DeepSeek V4.1 Flash (`opencode-go/deepseek-v4.1-flash`) |
| Harness | OpenCode 2.2.5 |

## Build order

1. **Orient.** Inspected `~/Projects`, existing skill patterns, GitHub auth and the OpenCode session
   database (for token accounting). Confirmed the stored GitHub credential works, so no new Keychain
   prompt was needed for repeated pushes.
2. **Content architecture.** Chose Japan, Italy and Costa Rica, then five destinations per region with
   complementary interests (culture/city/nature/food/beach). Defined the full data shape before
   writing UI so filters, modal and budgets had a stable contract.
3. **Image curation.** Collected candidate Unsplash CDN IDs, downloaded 480 px thumbnails, built an
   HTML contact sheet, screenshotted it, and visually rejected mismatches. Final set: 24 unique images
   (15 primaries + region heroes, routes and gallery support). Every URL was then HEAD-tested.
4. **Data.** Wrote `data.js`: 3 regions, 15 destinations, 3 routes, budgets, months, attractions,
   cuisine, culture, season notes and itinerary tips. Budget parts were authored to sum to
   `dailyBudget`.
5. **Shell and design.** Built `index.html` with semantic sections, an inline SVG sprite and a
   static hero. Wrote the full token-based design system in `style.css`.
6. **Behaviour.** Wrote `script.js`: rendering, filtering, search, modal, gallery, deep links, trip
   persistence, routes, toasts, focus traps, hero autoplay, reveals and image fallbacks.
7. **Verification.** Built a self-contained static server and an 81-check Puppeteer suite; fixed
   responsive issues found at 390 px (figure margins, region tabs, filter selects, floating button
   overlap, modal ordering).
8. **Release assets.** Added a 1200×630 social card, README screenshots, 404, manifest, robots,
   sitemap, Pages workflow.
9. **Documentation.** Wrote README, AGENTS, architecture, data schema, design system, verification,
   deployment, reproduction, contributing, changelog, security, credits and license.
10. **Publish.** Created the GitHub repository, pushed, enabled Pages, verified the live URL, then
    published the skill to the OpenCode catalog.
11. **Provenance.** Wrote `state.json` and `STATE.md` from the OpenCode session record after the
    deployment.

## Key decisions and why

### Three regions, not a generic catalogue

Japan, Italy and Costa Rica give three distinct visual and emotional registers: ritual/neon, art/coast,
and rainforest/Pacific. Five destinations each is enough to feel curated and small enough to research
properly. The Costa Rica lineup favours Monteverde, Manuel Antonio, Tamarindo, Tortuguero and
Corcovado. Arenal was considered but a matching volcano photograph was not among the visually verified
set; rather than mislabel a mountain photo, the atlas uses Corcovado as the wild finale. Add Arenal
later with a verified image.

### A separate `data.js`

The brief asked for clean separation of `index.html`, `style.css` and `script.js`. A fourth file,
`data.js`, keeps content out of behaviour and makes the catalogue editable without touching rendering.
The deployed site remains four static files and has no build step. The dataset is frozen at load.

### Custom CSS with CSS variables, Tailwind CDN included

The brief offered “Tailwind CSS / modern CSS variables” and also asked for Tailwind via CDN. The
interface is authored entirely in `style.css` with tokens, because that gives tighter editorial control
and a fully styled page if the CDN is blocked. The Play CDN is included to satisfy the letter of the
brief, with preflight disabled so it cannot reset the type system. It logs a development warning; see
known limits.

### 24 verified images, hotlinked

Downloading the photos would bloat the repo and complicate licensing. The app hotlinks
`images.unsplash.com` and uses `srcset`/`sizes` plus `auto=format` so Unsplash serves an optimized
format. A failure hides the image and leaves a gradient, so layout never collapses.

### `localStorage` only

The itinerary is a personal shortlist, not an account. Storing it in the browser avoids auth, privacy
and infrastructure. Schema is versioned and tolerant of a bare legacy map. Private mode is handled
gracefully with an in-memory fallback.

### Hash deep links

`#/destination/<id>` works on GitHub Pages without server rewrites. `replaceState` avoids polluting
the back stack; `popstate` is handled for normal browser navigation.

### Verification as a product feature

The release gate is part of the repository. It starts its own server, clears storage, and tests the
real UI rather than a mock. It catches the regressions that matter for this app: filter counts,
persistence totals, modal content, mobile overflow and missing accessible names.

## Exact reproduction steps

### 1. Scaffold

```sh
mkdir -p ~/Projects/wanderlust-3d
cd ~/Projects/wanderlust-3d
git init
git branch -M main
mkdir -p docs scripts/lib screenshots assets .github/workflows
```

### 2. Build the files

Create, in this order:

1. `data.js` — catalogue and routes
2. `index.html` — shell, sprite, static hero, overlays
3. `style.css` — tokens, components, responsive rules
4. `script.js` — rendering and behaviour
5. `scripts/lib/static-server.mjs`, `scripts/serve.mjs`
6. `scripts/verify.mjs`, `scripts/check-images.mjs`, `scripts/screenshots.mjs`
7. `package.json` with `puppeteer-core` as the only dev dependency
8. release assets and docs

The skill at `~/.config/opencode/skills/wanderlust-3d-studio` contains a working starter copy of the
four core files plus the verification scripts. To scaffold a fresh copy:

```sh
node ~/.config/opencode/skills/wanderlust-3d-studio/scripts/scaffold.mjs ~/Projects/my-atlas
```

### 3. Curate images

- Build a list of candidate `photo-…` IDs.
- Download thumbnails at `?auto=format&fit=crop&w=480&q=60`.
- Generate a contact sheet:

```html
<div style="display:grid;grid-template-columns:repeat(5,1fr);gap:10px">
  <!-- one figure per image, with the ID as the caption -->
</div>
```

- Screenshot it with Chrome and inspect the actual subjects. A 200 status is not evidence that the
  image shows the destination.
- Map the final images to destinations; keep two supporting images per destination for the gallery.
- HEAD every unique URL:

```sh
npm run verify:images
```

### 4. Verify

```sh
npm install
npm run verify
npm run screenshots
```

Expected: `81/81 checks passed`, `24/24 images reachable`.

### 5. Publish the app

```sh
git add -A
git commit -m "feat: release Wanderlust 3D 1.0.0"
git remote add origin https://github.com/somphol/wanderlust-3d.git
git push -u origin main
```

Create the repository through the GitHub API using the existing credential, then enable Pages with
`build_type: workflow`. The first workflow run creates the `github-pages` environment and deploys.
See [DEPLOYMENT.md](DEPLOYMENT.md).

### 6. Publish the skill

The skill source lives at:

```text
~/.config/opencode/skills/wanderlust-3d-studio/
├── SKILL.md
├── README.md
├── ATTRIBUTION.md
├── assets/wanderlust-3d-starter/
├── references/
└── scripts/
```

It is copied into the catalog repository `~/Projects/opencode-skills` by adding an entry to
`skills.json` and running `node sync.mjs`. That repository is the GitHub backup and serves the skill
from <https://somphol.github.io/opencode-skills/>.

```sh
node ~/.config/opencode/skills/skill-publish/scripts/check-skill.mjs \
  ~/.config/opencode/skills/wanderlust-3d-studio
cd ~/Projects/opencode-skills
node sync.mjs wanderlust-3d-studio
git add -A && git commit -m "feat: add wanderlust-3d-studio skill"
git push
```

### 7. Record state

Query the OpenCode session record (`~/.local/share/opencode/opencode.db`, table `session_v2`) for
`tokens_input`, `tokens_output`, `tokens_reasoning`, cache reads/writes and cost. Write them to
`state.json` and `STATE.md`; note that the snapshot precedes the final state-file commit and push, so
the live session totals are slightly higher.

Never print credentials while doing this. The session ID is in `state.json`; it is not a secret.

## File-by-file map

| File | Responsibility | Safe to edit? |
| --- | --- | --- |
| `index.html` | shell, SEO, sprite, static hero, overlay shells | yes, keep IDs used by JS |
| `style.css` | tokens + all styling | yes |
| `data.js` | catalogue | yes, then run verify |
| `script.js` | render + behaviour | yes, then run verify |
| `scripts/verify.mjs` | release gate | yes, keep checks meaningful |
| `scripts/screenshots.mjs` | screenshot generation | yes |
| `screenshots/*` | generated release images | no, regenerate |
| `docs/*` | handoff and design docs | yes |
| `state.json` / `STATE.md` | provenance snapshot | update only with real numbers |
| `.github/workflows/pages.yml` | deploy | rarely |

## Data and image inventory

See [CREDITS.md](../CREDITS.md) for the 24-image inventory and [DATA-SCHEMA.md](DATA-SCHEMA.md) for
field-level rules. The 15 destination IDs are:

```text
kyoto, tokyo, hakone, osaka, okinawa
rome, florence, venice, amalfi-coast, dolomites
monteverde, manuel-antonio, tamarindo, tortuguero, corcovado
```

These IDs appear in URLs and `localStorage`; treat them as stable API.

## Verification summary

At release:

- 81/81 headless checks passed
- 24/24 Unsplash images returned 200 with an image content type
- no console errors, no page errors
- no horizontal overflow at 390 px or 768 px
- screenshots regenerated from the real app

The Tailwind Play CDN emits a non-fatal warning:

```text
cdn.tailwindcss.com should not be used in production. To use Tailwind CSS in production, install it
as a PostCSS plugin or use the Tailwind CLI.
```

This is a consequence of the brief's CDN requirement. The page does not depend on Tailwind for layout.

## Known deviations and open items

- **No Arenal destination** in Costa Rica; see the decision log above.
- **No service worker/offline mode.** The app is static but requires network for photography.
- **No booking/payment/account layer.** Out of scope.
- **Tailwind warning.** Removing the two script tags in `index.html` removes it.
- **Photographer names are not embedded in the CDN assets.** CREDITS lists the image IDs and licence;
  per-photographer attribution would require resolving each asset on Unsplash.
- **Budgets and travel notes are editorial.** They are planning estimates, not live inventory.
- **`prefers-reduced-motion` disables hero autoplay**, but the first slide still shows.

## Copy-paste brief for the next agent

> Read `AGENTS.md` and `docs/ARCHITECTURE.md` first. Build or extend a static travel atlas called
> Wanderlust 3D with exactly three regions and five destinations each. Keep the four-file core
> (`index.html`, `style.css`, `data.js`, `script.js`) with no runtime build. Every destination needs a
> verified Unsplash primary image, three gallery images, seasons, a daily budget whose breakdown sums
> to the total, three attractions, four dishes, cultural notes and an itinerary tip. Preserve the
> region tabs, live search/interest/budget filters, destination modal with hash deep links, and the
> `localStorage` itinerary with night steppers and totals. Run `npm run verify` (81 checks) and
> `npm run verify:images` (24 images) before publishing. Regenerate screenshots with
> `npm run screenshots`. Update `AGENTS.md`, `docs/REPRODUCTION.md` and `state.json` after the build.
> Deploy to GitHub Pages via the included workflow.

## Continuity

- Live: <https://somphol.github.io/wanderlust-3d/>
- Repo: <https://github.com/somphol/wanderlust-3d>
- Local: `~/Projects/wanderlust-3d`
- Skill: `~/.config/opencode/skills/wanderlust-3d-studio`
- Catalog: <https://somphol.github.io/opencode-skills/>
- Build state: [state.json](../state.json) / [STATE.md](../STATE.md)
