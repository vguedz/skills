# AI Tells — Forbidden Patterns (Tiered)

These are vg-design defaults for avoiding common AI-looking output. They defer to the precedence model: if a project, brand, or design system intentionally uses one of these patterns, follow the project constraint and document the exception. If there is no explicit constraint, rewrite.

---

## Tier 0 — Critical (Never Ship These)

These are the highest-frequency LLM signature tells. They instantly read as "AI made this" unless a real project constraint justifies them.

### Typography
- Inter as default font (see [typography.md](typography.md) for override path)
- Em-dashes (`—`) anywhere — zero tolerance
- Fraunces or Instrument_Serif as default serif

### Visual
- Cream/beige/sand/paper body background (`oklch(L 0.84-0.97, C < 0.06, H 40-100)`)
- Beige+brass+oxblood+espresso palette for premium-consumer briefs
- AI-purple/blue gradient glows on buttons and hero backgrounds
- Div-based fake screenshots (fake task lists, dashboards, terminals built from styled `<div>`s)
- Gradient text (`background-clip: text` + gradient)
- `border-radius: 32px+` on cards/sections/inputs

### Layout
- Identical 3-column card grids (icon + heading + text, repeated)
- Eyebrow (small uppercase tracked label) above every section header
- Nested cards (card inside card)
- Centered hero when VARIANCE > 4 (unless editorial/manifesto)

### Motion
- `transition: all` — specify exact properties
- `ease-in` on any UI animation

### Code
- Placeholder patterns: `// ...`, `// TODO`, `// rest of code`, bare `...`

### Copy
- Em-dashes in any visible text
- "Quietly in use at" / "Quietly trusted by" social-proof headers
- Scroll cues: "↓ scroll", "Scroll to explore"

---

## Tier 1 — Common (Almost Always Wrong)

These are frequent AI defaults. Override unless the brief explicitly calls for one.

### Visual
- Glassmorphism as default (rare and purposeful, or nothing)
- Side-stripe borders (`border-left`/`border-right` > 1px as colored accent)
- Ghost-card pattern (`border: 1px solid` + wide soft shadow on same element)
- Hand-drawn / sketchy SVG illustrations (`feTurbulence`, `feDisplacementMap`)
- `repeating-linear-gradient(...)` stripe backgrounds
- Pure black (`#000000`) or pure white (`#ffffff`)
- Decorative colored status dots on nav/list/badge
- Filled progress-bar tracks on landing pages

### Layout
- Numbered section markers (`01 / About`, `02 / Process`, `06 · how it works`)
- Split-header: "left big headline + right small explainer paragraph" as section header
- Hero-metric template (big number + label + stats + gradient)
- Decoration text strip at hero bottom (`BRAND. MOTION. SPATIAL.`)

### Typography
- `<br>`-broken-and-italicized headlines as default
- Mixed-family emphasis (random serif word in sans headline)

### Copy & Content
- Generic names ("John Doe", "Sarah Chan", "Jack Su")
- Startup-slop brand names ("Acme", "Nexus", "SmartFlow", "Cloudly")
- Filler verbs ("Elevate", "Seamless", "Unleash", "Next-Gen", "Revolutionize")
- Section-numbering eyebrows
- Generic step labels ("Stage 1 / Stage 2 / Stage 3")
- Version labels in hero (V0.6, BETA, INVITE-ONLY) unless actual launch
- Version footers on marketing pages (`v1.4.2`, `Build 0048`)
- Pills/labels overlaid on images ("Plate · Brand", "Field notes - journal")
- Photo-credit captions as decoration ("Field study no. 12 · Ines Caetano")
- "From the field" / "Field notes" / "On our desks" poetic labels
- Micro-meta-sentences under eyebrows ("Each of these is a feature we ship today...")

### Structural
- Logo wall with industry labels below logos (logo = logo only)
- `border-t` + `border-b` on every row of long lists/spec tables
- Long spec sheets with hairline under every row
- The middle-dot (`·`) used as default separator for everything (max 1 per line)
- `01 / 4`-style pagination on images/bento

---

## Tier 2 — Situational (Avoid Unless Brief Calls For It)

These are tells in most contexts but legitimate when the brief explicitly requests them.

### Visual & Layout
- Weather/locale strips ("LIS 14:23 · 18°C") — only for globally-distributed or place-focused brands
- Vertical rotated text ("INDEX OF WORK" rotated 90°) — agency/portfolio cliché
- Crosshair/hairline grid lines as decoration — only when organizing real content
- Overlapping card rotations on mobile — touch-target hazard

### Copy & Content
- Fake-precise numbers (`92%`, `4.1×`, `48k`) — must come from real data or be labeled as mock
- "Reservation 412 of 800"-style live counters — only for real waitlist data

### Motion & Code
- Marquee repeated on same page — max 1 per page
- Custom mouse cursors — accessibility-hostile, perf-hostile
- Hand-rolled SVG icons — use Phosphor / HugeIcons / Radix / Tabler
- Lucide as default icon library — acceptable only on explicit request or existing dependency
- Mixed icon families (Phosphor + Lucide in same component tree)

### Technology
- `window.addEventListener('scroll', ...)` — use `useScroll()` / `ScrollTrigger` / `IntersectionObserver`
- Animating layout properties (`top`, `left`, `width`, `height`)
- Custom `window.scrollY` in React state
- Mixing GSAP/Three.js with Motion in same component tree
