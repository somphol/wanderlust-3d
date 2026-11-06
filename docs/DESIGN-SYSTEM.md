# Design system

The visual language is “editorial travel journal meets modern atlas”: warm paper, deep emerald ink,
terracotta sunset accents, large serif display type and generous white space. Photography carries the
colour; the UI stays quiet around it.

## Palette

| Token | Value | Use |
| --- | --- | --- |
| `--ink` | `#17251f` | primary text |
| `--ink-soft` | `#4d5c53` | body secondary |
| `--ink-muted` | `#74827a` | labels, metadata |
| `--paper` | `#f7f2e8` | page background |
| `--paper-deep` | `#f0e8d9` | section wash, footer |
| `--paper-card` | `#fffdf8` | cards, surfaces |
| `--sand` | `#e7d9c2` | CTA buttons, warm highlights |
| `--sand-deep` | `#d8c4a5` | scrollbar, borders |
| `--emerald` | `#0d3b2e` | primary brand / dark sections |
| `--emerald-soft` | `#165942` | hover |
| `--emerald-wash` | `#dfece5` | subtle fills |
| `--sunset` | `#e8794f` | accent, focus ring, progress |
| `--sunset-soft` | `#f8d9c8` | season note |
| `--gold` | `#c7a15a` | reserved accent |
| `--danger` | `#a63f31` | destructive actions |
| `--accent` | JS-set | region accent |
| `--accent-soft` | JS-set | region wash |

Region accents:

| Region | Accent | Soft |
| --- | --- | --- |
| Japan | `#c34f5c` | `#f8e4e3` |
| Italy | `#b8653f` | `#f7e8dc` |
| Costa Rica | `#1f8a63` | `#def1e8` |

## Typography

| Role | Stack | Notes |
| --- | --- | --- |
| Display | `"Iowan Old Style", "Palatino Linotype", "Book Antiqua", Palatino, Georgia, serif` | headlines, numbers, quote |
| Body | `"Avenir Next", Avenir, "Segoe UI", "Helvetica Neue", system-ui, sans-serif` | UI, paragraphs |
| Mono | `"SFMono-Regular", ui-monospace, Menlo, Consolas, monospace` | reserved |

No web fonts are downloaded. The serif fallback is deliberate: Iowan on macOS, Palatino/Georgia on
Windows, Georgia on Linux. It keeps the editorial character without a font request.

### Scale

| Element | Size | Line height |
| --- | --- | --- |
| Hero h1 | `clamp(3.4rem, 8.8vw, 7.6rem)` | `0.89` |
| Section h2 | `clamp(2.35rem, 4.6vw, 4.3rem)` | `0.99` |
| Card title | `clamp(1.65rem, 2.2vw, 2.05rem)` | `1` |
| Modal title | `clamp(2.5rem, 5vw, 4.6rem)` | `0.92` |
| Body | `1rem` | `1.65` |
| Small label | `0.62–0.72rem` uppercase, `0.11–0.2em` tracking | `1.3` |

Display italics are used once per headline to carry the emotional word — “wider”, “stories”,
“disappear”. Do not set whole headings in italic.

## Spacing and layout

| Token | Value |
| --- | --- |
| `--container` | `1280px` |
| `--gutter` | `clamp(1.15rem, 4vw, 2.75rem)` |
| `--header-h` | `82px` desktop / `70px` mobile |
| Section padding | `clamp(4.5rem, 9vw, 8rem)` |
| Card gap | `clamp(1rem, 2vw, 1.65rem)` |

Grid behaviour:

- Explorer: 3 columns ≥ 1080 px, 2 columns 761–1080 px, 1 column ≤ 760 px.
- Routes: 3 columns ≥ 1080 px, 1 column below 761 px.
- Footer: brand + 3 link columns, collapsing to 2 then 1.
- Region tabs: 4 horizontal cards on desktop; a 2 × 2 grid on mobile.
- Interest chips: wrapped on desktop; one horizontally scrollable row on mobile.

## Surfaces and shape

| Token | Value |
| --- | --- |
| `--radius-sm` | `12px` |
| `--radius` | `20px` |
| `--radius-lg` | `28px` |
| `--radius-xl` | `36px` |

Cards are `#fffdf8` with a 1 px low-contrast border, a 20 px radius and a shadow that appears on hover.
Glass surfaces (header, search bar, filter panel) use `backdrop-filter: blur()` with a translucent
paper fill. The hero and route cards use dark scrims for text contrast.

## Components

### Header

Fixed, transparent over the hero, switching to a blurred paper bar after 36 px of scroll. The brand
is a compass mark plus a two-line wordmark. Desktop nav links have an animated underline. The trip
button is a pill with an orange count badge.

### Hero

Three full-bleed slides crossfade over 1.35 s with a 14 s Ken Burns transform. A left-biased scrim
protects the headline. Search is a floating paper pill with a dark emerald submit. Dots, arrows and a
progress line sit bottom-right; a scroll cue sits bottom-left.

### Destination card

4:3 photograph with region and daily-budget badges, an editorial body, three interest chips, best
season and suggested nights. The entire card is a link; a separate “Add to trip” pill overlays the
bottom-right. Hover lifts the card 6 px and scales the image 1.055×.

### Destination modal

A 1120 px paper panel with a 16:7 photographic hero, gallery thumbnails, a drop-cap overview, numbered
attractions, cuisine grid, culture/season note cards and a sticky sidebar with stats, add button,
budget bars and a 12-month best-time strip. On mobile the sidebar stats come first, the story follows,
then budget and months.

### Trip drawer

A 460 px right-hand paper drawer (full-width on mobile) with thumbnail rows, a compact night stepper,
estimated cost per item, and a sticky footer with places/nights/total plus copy and download. The
empty state is a compass and a single call to action.

### Signature route card

A dark photographic card with bottom-aligned editorial content: region/days/pace pills, title,
summary, destination chips, “From” price and an add-route button. The scrim is strong enough for white
text at every crop.

## Motion

| Motion | Duration | Easing |
| --- | --- | --- |
| Hero slide crossfade | 1.35 s | `cubic-bezier(.22,.61,.36,1)` |
| Hero Ken Burns | 14 s | ease-out |
| Card hover | 0.35 s | `cubic-bezier(.16,1,.3,1)` |
| Modal entry | 0.45 s | `cubic-bezier(.16,1,.3,1)` |
| Drawer slide | 0.42 s | `cubic-bezier(.16,1,.3,1)` |
| Reveal on scroll | 0.8 s | `cubic-bezier(.16,1,.3,1)` |

All non-essential motion is removed under `prefers-reduced-motion: reduce`. The hero autoplay and
progress bar are disabled too.

## Iconography

A single inline SVG sprite in `index.html` defines 24 px stroke icons (`#i-compass`, `#i-search`,
`#i-suitcase`, …). The UI uses `stroke: currentColor`, `stroke-width: 1.7`, round caps and joins. No
icon font or external icon library is loaded. The compass mark is also the favicon and app icon.

## Photography direction

- Warm, natural light; avoid heavy HDR or stock-smile tourism.
- Human presence is welcome but not required; the place is the subject.
- Prefer a strong sense of depth, texture or weather over a flat landmark shot.
- Maintain a mix of wide landscapes, streets and small details across a region.
- Do not apply filters in CSS beyond a slight saturation reduction (`0.92–0.95`); the photographs
  should feel like photographs.

## Accessibility

- Body text contrast: ink on paper ~13:1; soft ink on paper ~7:1; white on emerald ~11:1.
- Focus: 3 px `--sunset` outline with 3 px offset; never remove focus without a replacement.
- Touch targets: 44 px minimum for primary controls; stepper buttons are 24 px inside a larger row
  with generous spacing.
- Headings follow a single h1 → h2 → h3 hierarchy.
- Motion is optional; the site is fully usable with animation disabled.
