# Verification

The project has three verification layers: a headless browser release gate, a network image audit,
and a short manual review. All three are expected before publishing.

## Prerequisites

- Node.js 20+
- Google Chrome or Chromium (the scripts use `puppeteer-core`, not a bundled browser)
- Network access for the image audit and for the screenshots that load Unsplash photography

```sh
npm install
```

If Chrome is not discovered automatically:

```sh
CHROME_PATH="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" npm run verify
```

## 1. Headless release gate

```sh
npm run verify
```

`scripts/verify.mjs` starts its own static server on a random localhost port, launches Chrome,
clears `localStorage`, and exercises the application end to end. It fails on any uncaught page error,
console error, data-integrity problem or layout overflow.

Current result at release:

```text
81/81 checks passed in ~18s
Release checks passed.
```

What it covers:

| Area | Checks |
| --- | --- |
| Static/SEO | title, one h1, meta description, Open Graph, skip link, valid JSON-LD |
| Data | 3 regions, 15 destinations, 5 per region, unique IDs, 3 gallery images, budget sums, Unsplash host |
| Catalogue | initial 15 cards, 4 region tabs, 12 interest chips, default count |
| Region filter | Japan/Italy/Costa Rica each return 5, all restores 15, first card order |
| Search | Kyoto ranking, clear button, semantic “turtle” search, reset |
| Interests | Beaches OR filter, pressed state, toggle off |
| Budget/sort | luxury tier, budget sort order, reset |
| Modal | Kyoto opens, 3 thumbs, 3 attractions, 4 cuisine cards, gallery swap |
| Trip | add, badge, localStorage payload, added state, Escape/focus return |
| Drawer | open, item, nights, cost, increment/decrement, remove, empty state |
| Routes | Japan route adds 4, persisted, 11 nights, $1,930 total |
| Persistence | reload keeps 4 items; clear requires two clicks |
| Deep link | `#/destination/venice` opens Venice; close clears hash |
| Mobile 390px | no overflow, back-to-top hidden, menu opens/closes, modal/drawer fit |
| Tablet 768px | no overflow, two-column grid |
| Accessibility | every image has alt, every visible button/link has a name |
| Runtime | zero page errors, zero console errors |

### Interpreting a failure

- “expected 1, got 2” in search means the data or the search fixture changed. Search is intentionally
  semantic: “kyoto” also matches Osaka, whose overview mentions Kyoto as a base. Adjust the fixture
  only if the behaviour is correct.
- “budget breakdowns sum” means a destination's four budget parts no longer equal `dailyBudget`.
- “horizontal overflow” means a responsive rule regressed. Inspect the element at the reported width.
- “page errors” prints the stack after the summary; fix the first error before re-running.

## 2. Image reachability audit

```sh
npm run verify:images
```

This parses `data.js`, the static hero URLs in `index.html` and the route images, deduplicates them,
and sends a HEAD request to each unique Unsplash CDN URL with a small crop query.

Current result:

```text
24/24 images reachable.
All image URLs resolved.
```

The audit proves the asset exists; it does not prove the photo depicts the named place. Visual
curation is a separate editorial step (see [DATA-SCHEMA.md](DATA-SCHEMA.md#how-the-original-images-were-curated)).

## 3. Screenshot regeneration

```sh
npm run screenshots
```

Writes nine JPEGs to `screenshots/`:

- `desktop-hero.jpg`
- `desktop-explorer.jpg`
- `desktop-routes.jpg`
- `desktop-modal.jpg`
- `desktop-itinerary.jpg`
- `og-cover.jpg` (1200 × 630 social card)
- `mobile-hero.jpg`
- `mobile-explorer.jpg`
- `mobile-modal.jpg`

The script resets `localStorage`, adds the Japan route for the itinerary shot, hides transient toasts,
blurs focus rings, and waits for images with a 1.6 s cap. Do not edit these files by hand.

## Manual QA checklist

Run through this once on a real browser before a release:

- [ ] Hero auto-advances; dots and arrows change the slide; no flash of an unloaded background.
- [ ] Search from the hero scrolls to the atlas and applies the query.
- [ ] Region tabs, interest chips, budget and sort combine without a blank state.
- [ ] Reset filters restores all 15 and clears the inputs.
- [ ] A card opens the correct modal; the URL hash changes.
- [ ] Gallery thumbnails swap the hero image.
- [ ] Add to itinerary updates the badge and survives a reload.
- [ ] Night stepper updates the item cost and the total.
- [ ] Copy plan puts a readable itinerary on the clipboard (HTTPS or localhost).
- [ ] Download saves `wanderlust-itinerary.txt`.
- [ ] Clear itinerary requires two clicks.
- [ ] Escape closes the modal/drawer and returns focus.
- [ ] Mobile menu opens, navigates and closes.
- [ ] At 390 px there is no horizontal scroll; modal and drawer fill the viewport.
- [ ] With `prefers-reduced-motion`, hero autoplay is off and reveals are instant.
- [ ] Keyboard-only: tab through the header, filters, cards, modal and drawer; focus is always visible.
- [ ] A missing image leaves a gradient placeholder, not a broken icon or collapsed card.

## Browser support

Tested in Chromium (headless, current). The CSS uses modern features supported by current Chrome,
Edge, Firefox and Safari:

- CSS custom properties
- `clamp()`, `min()`, `aspect-ratio`, `svh`/`dvh` units
- `backdrop-filter`
- `:has()` for the card focus ring (progressive enhancement; the fallback is a default focus outline)
- `display: contents` for the mobile modal reorder (current browsers)

No Internet Explorer support is intended.

## Not covered by automation

- Factual accuracy of travel advice, prices, opening hours and visa rules
- Photographer names and per-image attribution
- Visual design taste and photographic matching
- Screen-reader output on every platform (manual audit recommended)
- GitHub Pages propagation time
- Network conditions and CDN rate limiting

## Troubleshooting

| Symptom | Fix |
| --- | --- |
| `Chrome/Chromium not found` | Set `CHROME_PATH` |
| `npm install` fails | Confirm Node 20+ and network access |
| Screenshots time out | Unsplash may be slow; rerun, or raise the 1.6 s image wait |
| A check fails only after editing data | Run `npm run verify` and read the first data failure |
| Tailwind warning in console | Expected; it is a Play CDN development warning, not an error |
