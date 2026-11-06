# Contributing

This is a small, opinionated editorial project. Contributions are welcome if they preserve the
curated scale (3 regions × 5 destinations) and the zero-build architecture.

## Before you start

- Read [AGENTS.md](AGENTS.md) for commands and constraints.
- Read [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) for how rendering and state work.
- Read [docs/DATA-SCHEMA.md](docs/DATA-SCHEMA.md) before changing content.

## Code standards

- Vanilla ES6 in an IIFE; no framework, bundler or runtime dependency.
- Keep `index.html`, `style.css`, `data.js` and `script.js` deployable as-is.
- Use CSS custom properties instead of new hard-coded colours.
- Escape catalogue strings before injecting them into HTML (`esc()`).
- Preserve keyboard support and focus management in overlays.
- Respect `prefers-reduced-motion`.
- Do not add analytics, tracking, cookies or third-party network calls without a documented reason.

## Content standards

- Exactly five destinations per region; unique, stable, lowercase kebab-case IDs.
- Every destination has three attractions, four dishes, three gallery images and a real `imageAlt`.
- `budgetBreakdown` must sum to `dailyBudget` in USD.
- `bestMonths` uses three-letter English abbreviations.
- Images must be visually verified Unsplash assets that actually depict the place or its region.
- Write specific, checkable prose. Avoid superlatives, affiliate language and unverifiable claims.
- Budgets are mid-range daily averages per person, excluding flights.
- Keep cultural notes practical and respectful; avoid stereotyping.

## Workflow

```sh
git checkout -b feat/your-change
# edit
npm install
npm run verify
npm run verify:images   # if images changed
npm run screenshots     # if visible UI changed
git add -A
git commit -m "feat: describe the change"
```

Open a pull request with:

- what changed and why
- the verification output
- screenshots for visual changes
- any new image IDs and their licence/verification notes

## Adding a destination

1. Add the object to `data.js` under the right region.
2. Move or remove another destination if the region would exceed five.
3. Add a verified image and two gallery supports.
4. Update `bestMonths`, budgets, attractions and cuisine.
5. Run both checks.
6. If the change alters the first visible cards, regenerate screenshots.

## Reporting issues

Use the GitHub issue tracker. Include:

- the page and viewport width
- steps to reproduce
- expected vs actual
- browser and version
- a screenshot if visual

For security issues, see [SECURITY.md](SECURITY.md).

## Licence

By contributing code or original copy, you agree that it is released under the MIT License. Do not
contribute photography you do not have the right to use.
