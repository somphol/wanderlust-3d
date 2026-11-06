# Data schema and content guide

`data.js` is the single source of truth for the atlas. It is a plain object assigned to
`window.WANDERLUST_DATA`; there is no database, CMS or build transform.

The file ends with a deep-freeze pass. Add new records before that pass and keep the existing shape.

## Top level

```js
{
  meta: { name, version, updated, currency, disclaimer },
  regions: [Region, Region, Region],
  destinations: [Destination × 15],
  routes: [Route, Route, Route]
}
```

### `meta`

| Field | Type | Notes |
| --- | --- | --- |
| `name` | string | Display name |
| `version` | string | Semantic-ish content version |
| `updated` | string | `YYYY-MM-DD` |
| `currency` | string | Display currency; currently `USD` |
| `disclaimer` | string | Planning-note disclaimer shown in docs, not UI |

### `Region`

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| `id` | string | yes | lowercase kebab-case, stable, used in URLs and CSS |
| `name` | string | yes | English name |
| `localName` | string | yes | Endonym or local label |
| `tagline` | string | yes | Editorial one-liner |
| `tabNote` | string | recommended | Short tab subtitle, e.g. `Culture · Food · Nature` |
| `short` | string | yes | Reserved for summaries |
| `description` | string | yes | 2–3 sentences for the region brief |
| `image` | string | yes | Unsplash CDN URL, no query string |
| `imageAlt` | string | yes | Descriptive alt text |
| `accent` | hex | yes | Region accent, used by JS |
| `accentSoft` | hex | yes | Wash for the region brief and tags |
| `ink` | hex | yes | Reserved for region-specific text |
| `bestTime` | string | yes | Human-readable window |
| `dailyBudget` | number | yes | Regional average in USD |
| `language` | string | yes | Primary language(s) |
| `currency` | string | yes | Local currency code |
| `stats` | array | yes | Three `{label, value}` objects for the brief |

### `Destination`

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| `id` | string | yes | Unique, stable, lowercase kebab-case |
| `regionId` | string | yes | Must match a `Region.id` |
| `name` | string | yes | Destination name |
| `country` | string | yes | Country label |
| `tagline` | string | yes | 4–8 words, evocative but concrete |
| `image` | string | yes | Primary Unsplash URL |
| `imageAlt` | string | yes | What is actually in the photo |
| `gallery` | array | yes | Exactly 3 `{ image, alt }` objects |
| `bestTime` | string | yes | Includes seasons and separators |
| `bestMonths` | array | yes | 3-letter month abbreviations, used by the 12-month strip |
| `dailyBudget` | number | yes | USD; must equal `budgetBreakdown` sum |
| `idealNights` | number | yes | Default trip nights, 1–30 |
| `budgetLevel` | string | yes | `budget` ≤ $125, `mid` $126–199, `luxury` ≥ $200 |
| `language` | string | yes | Local language(s) |
| `currency` | string | yes | Local currency code |
| `vibes` | array | yes | Interest tags drawn from the canonical list |
| `lat`, `lng` | number | yes | Decimal degrees; displayed in the modal |
| `overview` | string | yes | 3–5 sentences, present tense, specific |
| `attractions` | array | yes | Exactly 3 `{name, type, description}` |
| `cuisine` | array | yes | Exactly 4 `{name, note}` |
| `culture` | string | yes | Etiquette / local context, 2–3 sentences |
| `seasonNote` | string | yes | Weather/season trade-offs, 2–3 sentences |
| `itineraryTip` | string | yes | How to structure a stay |
| `audiences` | array | yes | 2–3 traveler types |
| `budgetBreakdown` | object | yes | `{stay, food, transport, activities}` in USD |

Canonical `vibes` values (order used by the chip bar):

```text
Culture · History · Foodie · Nature · Beaches · Mountains · Adventure
Wildlife · City · Wellness · Romance · Nightlife
```

The chip list is generated from the data intersection with `TAG_ORDER` in `script.js`. A new tag
outside that list will not render; add it to `TAG_ORDER` too.

### `Route`

| Field | Type | Notes |
| --- | --- | --- |
| `id` | string | stable kebab-case |
| `name` | string | editorial route name |
| `days` | number | Suggested total, display only |
| `regionId` | string | region badge |
| `image` | string | card image |
| `imageAlt` | string | alt text |
| `summary` | string | 2–3 sentences |
| `destinationIds` | array | Must reference existing destination IDs |
| `pace` | string | `Slow`, `Balanced`, `Active` |
| `budgetFrom` | number | Indicative per-person total, display only |

## Image rules

- Use only `https://images.unsplash.com/photo-<id>` as the base URL. Do **not** append a query string
  in `data.js`; the renderer adds `auto=format&fit=crop&w=…&q=…`.
- Every gallery item needs its own `alt`, describing the scene, not the destination name alone.
- Verify a candidate photo visually before using it. A 200 response only proves the asset exists.
- Run `npm run verify:images` before publishing.
- Do not commit downloaded copies of Unsplash photos; the project uses hotlinks to keep the repo
  small and the licence trail clear.

### How the original images were curated

1. Collect candidate Unsplash CDN IDs.
2. Download 480 px thumbnails and build an HTML contact sheet.
3. Screenshot the sheet, inspect it, and reject anything that does not match the destination or the
   region's visual mood.
4. Re-test every final URL with a HEAD request.
5. Map 24 unique images: 15 primaries + region heroes, routes and gallery support. See
   [CREDITS.md](../CREDITS.md) for the inventory.

## Copy standards

- Write for a curious adult, not a search engine. Present tense, concrete nouns, no brochure hype.
- Avoid unverifiable superlatives (“best in the world”). Prefer specific, checkable claims.
- Each overview should answer: what does this place feel like, and why would I choose it?
- `culture` is practical etiquette, not a history essay.
- `seasonNote` must name trade-offs (crowds, rain, closures), not just “best time”.
- `itineraryTip` should be actionable in one sentence or two.
- Budgets are indicative mid-range daily averages per person, excluding flights.
- Keep attraction `type` short: `Temple · 1–2 hrs`.

## Adding a destination

1. Copy an existing object with a similar budget tier.
2. Give it a new, unique `id`.
3. Set `regionId`; confirm that region would then have exactly five places. If not, remove or move
   another destination in the same edit.
4. Pick a verified image and two supporting gallery images.
5. Set `bestMonths` to 3-letter abbreviations.
6. Make `budgetBreakdown` sum to `dailyBudget` (integers, USD).
7. Add exactly three attractions and four cuisine items.
8. Run:

```sh
npm run verify
npm run verify:images
```

9. Regenerate screenshots only if the first visible cards changed.

## Adding a region

1. Add a `Region` object with a unique id, accent pair and three stats.
2. Add exactly five destinations with that `regionId`.
3. Add the region's hero image and optional signature route.
4. If the region uses a new vibe, add it to `TAG_ORDER` in `script.js`.
5. The tab, filter, accent and brief are automatic.
6. Run both checks.

## Validation contract

`scripts/verify.mjs` enforces:

- 3 regions, 15 destinations, 5 per region
- unique destination IDs
- exactly 3 gallery images per destination
- budget breakdown sums to `dailyBudget` (±1 for rounding)
- all primary images use the Unsplash CDN
- the rendered catalogue, filters, modal, trip planner and persistence behave as documented
- no console/page errors, no horizontal overflow at 390 px, images/buttons/links have names

It does **not** verify factual accuracy, photographer names, or that a photo depicts the named place.
Those remain editorial responsibilities.
