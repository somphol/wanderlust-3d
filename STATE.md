# Build state — Wanderlust 3D

A human-readable snapshot of how this project was produced. The machine-readable copy is
[`state.json`](state.json).

## At a glance

| | |
| --- | --- |
| Project | **Wanderlust 3D: Global Destination Explorer** |
| Version | 1.0.0 |
| Session start | 2026-11-06 03:18:11 UTC |
| Packaged | 2026-11-06 04:20:10 UTC |
| Deployed | 2026-11-06 04:17:37 UTC |
| Elapsed | **3,719 s — 1h 1m 59s** |
| Harness | **OpenCode 2.2.5** |
| Model | **DeepSeek V4.1 Flash** (`opencode-go/deepseek-v4.1-flash`) |
| Cost reported | **$0.00** |
| Live | <https://somphol.github.io/wanderlust-3d/> |
| Repository | <https://github.com/somphol/wanderlust-3d> |
| Pages run | <https://github.com/somphol/wanderlust-3d/actions/runs/42161593615> |

## What was built

- 3 regions: **Japan, Italy, Costa Rica**
- 15 destinations, exactly 5 per region
- 24 visually verified Unsplash photographs
- 3 signature routes
- 12 interest tags
- A three-slide cinematic hero with search
- Live region, interest, budget and sort filters
- Full destination modals with galleries, budget bars and a 12-month best-time strip
- A persistent `localStorage` itinerary with nights, totals, copy and download
- Hash deep links (`#/destination/kyoto`)
- 81 headless checks, a 24-image network audit and 9 generated release images
- README, AGENTS handoff, architecture, data-schema, design-system, verification, deployment and
  reproduction docs
- GitHub Actions Pages deployment
- A reusable `wanderlust-3d-studio` OpenCode skill, published to the skills catalog

## Token usage

| Metric | Value |
| --- | ---: |
| Input tokens | 291,927 |
| Output tokens | 143,978 |
| Reasoning tokens | 73,810 |
| **Total (input + output + reasoning)** | **509,715** |
| Cache read | 38,536,704 |
| Cache write | 1,336 |
| Total including cache | 39,047,755 |

Cache reads are repeated context usage, not new generation. They are reported separately so the
headline total stays comparable to a normal token count.

> **Snapshot note:** the figures were read from the OpenCode session record at
> 2026-11-06 04:20:10 UTC, before the final state-file commit, push and closing response. The live
> session totals will be marginally higher. The authoritative query is in
> [`docs/REPRODUCTION.md`](docs/REPRODUCTION.md#7-record-state).

## Verification

| Layer | Result |
| --- | --- |
| Headless release gate | **81/81 passed** |
| Unsplash image audit | **24/24 reachable** |
| Console errors | 0 |
| Page errors | 0 |
| Horizontal overflow at 390 / 768 / 1440 px | none |
| Live smoke test | 200, 15 cards, hero image loaded, 0 errors |
| Pages workflow | success |
| Skill check | OK — 24 files, 0.27 MB |
| Scaffold test | new project copied and 81/81 passed |

Commands:

```sh
npm run verify
npm run verify:images
npm run screenshots
```

## Time breakdown (approximate)

| Phase | Share |
| --- | --- |
| Orientation, auth, session lookup | ~5 min |
| Content architecture and 15 destination guides | ~15 min |
| Image curation and verification | ~10 min |
| HTML/CSS/JS implementation | ~18 min |
| Verification, responsive fixes, screenshots | ~7 min |
| Documentation and release assets | ~10 min |
| GitHub publication, Pages, skill, state | ~5 min |

The wall-clock total includes tool execution, headless browser runs and image downloads, not just
generation time.

## Credits

Designed, written and built with **OpenCode** and **DeepSeek V4.1 Flash**. Photography by Unsplash
contributors under the Unsplash License. System fonts; inline SVG icons.

Full details: [CREDITS.md](CREDITS.md).

## Continuity

- Local project: `~/Projects/wanderlust-3d`
- Skill source: `~/.config/opencode/skills/wanderlust-3d-studio`
- Skill catalog: <https://somphol.github.io/opencode-skills/>
- Skill file: <https://somphol.github.io/opencode-skills/wanderlust-3d-studio/SKILL.md>
- Reproduce or extend: [docs/REPRODUCTION.md](docs/REPRODUCTION.md)
- Operate the repo: [AGENTS.md](AGENTS.md)
