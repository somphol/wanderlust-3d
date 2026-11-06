# Architecture

## Overview

Wanderlust 3D is a static single-page application. There is no server, build step or framework. The
browser loads four core files in order:

```text
index.html
  ├── style.css          design system + all component styles
  ├── data.js            frozen catalogue (regions, destinations, routes)
  └── script.js          render + behaviour (defer, executes after parsing)
```

The only runtime network dependencies are:

1. `images.unsplash.com` for photography
2. `cdn.tailwindcss.com` for the brief-mandated Tailwind Play CDN
3. Google Chrome's local rendering (no fonts are downloaded)

If the Tailwind CDN is blocked, `style.css` still renders the complete interface.

## Rendering flow

```text
DOMContentLoaded
  └── init()
      ├── cacheElements()
      ├── loadTrip()             ← localStorage["wanderlust.trip.v1"]
      ├── renderRegionTabs()     ← one pass; later updates toggle classes
      ├── renderTagFilters()     ← one pass; later updates toggle aria-pressed
      ├── renderRoutes()
      ├── render()
      │   ├── updateRegionTabs()
      │   ├── applyAccent()      ← writes --accent / --accent-soft
      │   ├── renderRegionBrief()
      │   ├── updateTagFilters()
      │   ├── filteredDestinations()
      │   ├── renderGrid()
      │   ├── updateResultCount()
      │   ├── syncRouteButtons()
      │   └── updateTripBadge()
      ├── bindEvents()
      ├── setupReveals()
      └── setHeroSlide(0)
```

Every filter interaction re-runs only `render()`; region tabs and chips are not rebuilt, so focus and
scroll position survive. The destination grid is replaced with innerHTML; the trip drawer is
re-rendered from `state.trip` whenever it changes.

## Data model

`data.js` assigns `window.WANDERLUST_DATA` and deep-freezes the arrays and objects. The renderer never
mutates catalogue objects. A destination references its region with `regionId`; there is no nested
region→destination structure, which keeps filtering flat and makes a destination easy to move later.

The full field list is in [DATA-SCHEMA.md](DATA-SCHEMA.md).

## Filtering

The filter pipeline is intentionally simple and deterministic:

```js
state = { region: 'all', query: '', tags: Set, budget: 'all', sort: 'curated' }

matches(dest):
  region  → dest.regionId === state.region (or all)
  budget  → dest.budgetLevel === state.budget (or all)
  tags    → dest.vibes ∩ state.tags is non-empty (OR semantics)
  query   → case-insensitive substring across name, country, tagline, overview,
            bestTime, language, region name, vibes, attractions, cuisine, audiences
```

Then sorting:

| Value | Behaviour |
| --- | --- |
| `curated` | dataset order (region → editorial order) |
| `budget` | `dailyBudget` ascending, then name |
| `name` | `localeCompare` |
| `nights` | `idealNights` ascending, then budget |

`currentList` stores the filtered IDs. The modal uses that list for previous/next navigation, so
opening a destination from a filtered grid keeps the filtered context.

## Destination modal and deep links

Opening a destination renders a complete modal fragment from `modalHTML(dest)` and focuses the close
button. The URL becomes `#/destination/<id>` via `history.replaceState`. Closing restores focus to the
element that opened the modal and clears the hash.

- **Escape** closes the modal.
- **Tab / Shift+Tab** are trapped inside the panel.
- **Previous / next** use the filtered list, wrapping at both ends.
- **Gallery thumbnails** replace the hero image and its `srcset`.
- **Deep link** on load is handled 120 ms after init to allow the first paint.

The drawer follows the same focus pattern and locks body scroll via `body.no-scroll`. A single
`syncScrollLock()` decides the lock from `modalOpen || drawerOpen`.

## Trip planner

The only persisted state:

```json
{
  "version": 1,
  "savedAt": "2026-11-06T04:00:00.000Z",
  "items": {
    "kyoto": { "nights": 3, "order": 1, "addedAt": 1793936613166 }
  }
}
```

- Key: `wanderlust.trip.v1`
- `order` preserves insertion order for rendering.
- `nights` is clamped to 1–30.
- Estimated cost = Σ `nights × dailyBudget`.
- Adding a place from a card/modal uses `idealNights`; the drawer can change it.
- Adding a route skips existing places.
- Copy and download build the same plain-text itinerary.
- Clearing requires two clicks and resets after four seconds.

If `localStorage` throws (private mode/quota), the app keeps working in memory and tells the user once.

## Visual system

Design tokens are CSS custom properties at the top of `style.css`. Region accents are applied at
runtime:

```js
--accent      // region colour, e.g. #c34f5c for Japan
--accent-soft // matching wash
```

Components read tokens rather than hard-coded colours. The type pairing is system-only: an Iowan/
Palatino/Georgia display serif and an Avenir/Segoe/system sans. This avoids font requests and keeps
the editorial feel consistent across platforms.

The hero is a three-slide crossfade with a slow Ken Burns transform. Autoplay uses an 8-second
interval plus a CSS progress bar. It stops when the document is hidden and is disabled under
`prefers-reduced-motion`.

## Accessibility

- One `<h1>`; sections use `<h2>` and cards use `<h3>`.
- Skip link to `#explore`.
- `role="tablist"` with `aria-selected` and arrow-key navigation for region tabs.
- Filter chips are toggle buttons with `aria-pressed`.
- Result count is `aria-live="polite"`.
- Modal and drawer are `role="dialog" aria-modal="true"`.
- Focus trap, Escape handling, focus restoration.
- Decorative images use `alt=""`; content images have descriptive alt text.
- `:focus-visible` outlines use the sunset colour.
- Colour contrast is designed for AA against the paper/emerald surfaces.
- `prefers-reduced-motion` removes transforms, autoplay and smooth scrolling.

## Performance

- First hero image is eager and high priority; other hero slides and cards lazy-load.
- `srcset` supplies 480/800/1200 px card variants and 800/1200/1600 px modal variants.
- Every image has a fixed aspect ratio container to prevent layout shift.
- No web fonts, no icon library, no JS framework.
- The whole app is roughly 100 KB of HTML/CSS/JS before images (gzip much smaller).
- The Tailwind CDN is the largest optional script; remove it for the leanest build.

## Security and privacy

- No analytics, cookies, fingerprinting, accounts or third-party tracking.
- The only storage is the itinerary in `localStorage`.
- User-entered search text is never injected into HTML; it is only compared against lowercase text.
- Catalogue strings are HTML-escaped by `esc()` before interpolation.
- External links use `rel="noopener"`.
- GitHub Pages cannot set CSP/HSTS headers; a host with header control is required if those are
  mandated.

## Extension points

- **New destination:** add an object to `destinations`, verify the region now has five, add an image
  and run `npm run verify` plus `npm run verify:images`.
- **New region:** add to `regions`, add exactly five destinations, add a route if desired, and choose
  an accent. The tab and filter are automatic.
- **New filter:** add a field to the data, extend `matches()`, mirror the control into the filter panel
  and update `resetFilters()`.
- **New route:** add to `routes` with `destinationIds` and a display budget. No engine change needed.
- **Theme:** override the `:root` tokens. The region accents are set inline by JS, so override
  `applyAccent()` or provide a region palette.
