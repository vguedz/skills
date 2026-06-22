# AI Tells — Forbidden Patterns

Hard bans. Match-and-refuse. If you're about to write any of these, rewrite.

## Visual & CSS

- Neon/outer glows by default. Use inner borders or tinted shadows.
- Pure black (`#000000`). Off-black always.
- Oversaturated accents. Desaturate to blend with neutrals.
- Excessive gradient text (`background-clip: text` + gradient). Use solid color.
- Custom mouse cursors. Accessibility-hostile, perf-hostile.
- `repeating-linear-gradient(...)` stripe backgrounds. Pure decoration.
- Hand-drawn / sketchy SVG illustrations (`feTurbulence`, `feDisplacementMap`, "doodle", "wavy"). Ship real assets or nothing.
- Div-based fake screenshots (fake task lists, dashboards, terminals from styled divs). #1 LLM-design tell. Use real screenshots, generated images, or skip.
- `border-radius: 32px+` on cards/sections/inputs. Cards top out at 12-16px. Full-pill for tags/buttons only.
- Side-stripe borders (`border-left`/`border-right` > 1px as colored accent). Rewrite with full borders, bg tints, or nothing.
- Glassmorphism as default. Rare and purposeful, or nothing.
- Filled progress-bar tracks (`bg-zinc-200` with partial fill) on landing pages. Dashboard clutter.

## Typography

- Inter as default (see typography.md for override path).
- Em-dashes (`—`) anywhere (zero tolerance — see typography.md).
- `<br>`-broken-and-italicized headlines as default design move.

## Layout & Spacing

- The hero-metric template (big number + small label + supporting stats + gradient accent).
- Identical card grids (same-sized cards with icon + heading + text, repeated endlessly).
- Tiny uppercase tracked eyebrow above every section. Max 1 per 3 sections.
- Numbered section markers as default scaffolding (`01 / About`, `02 / Process`, `06 · how it works`).
- 3-column equal feature cards.
- Overlapping card rotations on mobile (< 768px). Touch-target conflicts.
- Split-header: "left big headline + right small explainer paragraph" as section header.
- Centered hero when `DESIGN_VARIANCE > 4` (unless editorial/manifesto).

## Content & Data

- Generic names ("John Doe", "Sarah Chan"). Use creative, realistic, locale-appropriate names.
- Generic avatars (SVG "egg", Lucide user icons). Use photo placeholders or skip.
- Fake-perfect numbers (`99.99%`, `50%`). Use organic data (`47.2%`).
- Startup-slop brand names ("Acme", "Nexus", "SmartFlow"). Invent premium-sounding names.
- Filler verbs ("Elevate", "Seamless", "Unleash"). Concrete verbs only.
- Fake-precise numbers (`92%`, `4.1x`, `48k`) not from real data. Mock-data label required.

## Copy Tells

- "Quietly in use at" / "Quietly trusted by" social-proof headers. Use plain language.
- "From the field" / "Field notes" / "On our desks" poetic labels. Use functional labels.
- Weather/locale strips ("LIS 14:23 · 18°C"). Banned unless brief is place-focused.
- Version labels in hero (V0.6, BETA, INVITE-ONLY). Only for actual launch briefs.
- Version footers on marketing pages (`v1.4.2`, `Build 0048`).
- Micro-meta-sentences under eyebrows ("Each of these is a feature we ship today..."). Clutter.
- Generic step labels ("Stage 1 / Stage 2"). Verb-noun directly.
- Pills/labels overlaid on images ("Plate · Brand", "Field notes - journal").
- Photo-credit captions as decoration ("Field study no. 12 · Ines Caetano"). Only for real credits.
- Scroll cues ("↓ scroll", "Scroll to explore", "Scroll to walk through it").
- Decorative colored status dots on nav/list/badge. Only for real semantic state.
- Decoration text strip at hero bottom (`BRAND. MOTION. SPATIAL.`).
- The em-dash in any visible text (zero tolerance).
- "We respect the French ones"-style mock-humble industry-references in body copy.

## Structural

- Logo wall = logos only. No industry/category labels below each logo. Use Simple Icons CDN or generated SVG marks, not plain text wordmarks.
- `border-t` + `border-b` on every row of long lists/spec tables. Use card-per-spec, grouped chunks, or accordion.
- Long spec sheets with hairline under every row. Banned. Alternative UI components required.
- The middle-dot (`·`) is rationed. Max 1 per line in metadata strips.
- Section-numbering eyebrows (`00 / INDEX`, `001 · Capabilities`).
- `01 / 4`-style pagination on images or bento tiles.
- Micro-meta-sentences below section headings.
- "Index of Work, 2018 - 2026"-style range labels as eyebrows.
- Vertical rotated text ("INDEX" rotated 90°). Agency cliché.
- Crosshair/hairline grid lines as decoration.

## Technology

- `transition: all` — always specify exact properties.
- `window.addEventListener("scroll", ...)` — use `useScroll()` / `ScrollTrigger` / `IntersectionObserver`.
- Animating layout properties (`top`, `left`, `width`, `height`).
- Custom `window.scrollY` in React state — re-renders every frame.
- `requestAnimationFrame` touching React state — use motion values.
- Hand-rolled SVG icons — use Phosphor / HugeIcons / Radix / Tabler.
- Mixing GSAP/Three.js with Motion in the same component tree.
- Lucide as default icons — acceptable only on explicit request or existing dependency.
