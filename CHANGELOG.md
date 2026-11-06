# Changelog

All notable changes to Wanderlust 3D are documented here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and the project uses semantic-ish versions for
content and code releases.

## [1.0.0] — 2026-11-06

### Added

- Three curated regions — Japan, Italy and Costa Rica — with five destinations each.
- Fifteen destination guides with overviews, three attractions, four dishes, cultural notes, season
  guidance, itinerary tips, indicative budgets and suggested nights.
- 24 visually verified Unsplash photographs, including three-image galleries per destination.
- Full-screen three-slide hero with autoplay, dots, arrows, progress and a live search form.
- Region tabs, multi-select interest chips, budget tiers, four sort orders and real-time semantic
  search.
- Immersive destination modal with gallery thumbnails, drop-cap overview, budget bars, 12-month
  best-time strip and previous/next navigation.
- Persistent **My Dream Itinerary** drawer with add/remove, night steppers, estimated totals, copy and
  text download.
- Three signature routes that add a whole itinerary in one click.
- Hash deep links for every destination.
- Responsive layout tested at 1440, 768 and 390 px.
- Accessibility: skip link, focus traps, focus restoration, keyboard tabs, live result counts,
  reduced-motion support and visible focus rings.
- 81-check headless release gate, 24-image network audit and reproducible screenshot generator.
- GitHub Pages deployment workflow.
- 404 page, web manifest, app icon, robots.txt, sitemap.xml and a 1200 × 630 social card.
- Release documentation: README, AGENTS handoff, architecture, data schema, design system,
  verification, deployment, reproduction, contributing, security, credits and licence.
- Build provenance in `state.json` and `STATE.md`.
- Reusable `wanderlust-3d-studio` OpenCode skill published through the skills catalog.

### Known limits

- Tailwind Play CDN development warning (layout is authored in `style.css`).
- Photography is hotlinked and requires network access.
- Budgets and travel notes are editorial planning estimates.
- Itinerary is per browser profile; no accounts or sync.
