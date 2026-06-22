---
name: vg-design
description: >-
  Production-grade frontend design engineering with Emil Kowalski's animation
  philosophy, anti-slop UI patterns, premium visual design, and full-output
  enforcement. Use when designing, building, reviewing, or refactoring frontend
  interfaces — landing pages, portfolios, dashboards, product UI, forms,
  animations, design systems. Triggers on "design", "UI", "UX", "style",
  "polish", "animation", "layout", "component", "review my UI", "audit
  design", "make it premium", "make it pop", "redesign", "craft", or any
  frontend task requiring taste-driven output.
version: 1.1.0
---

# vg-design

## Quick Start

1. **Infer the brief** — page kind, vibe, audience, brand assets. Write one-line Design Read.
2. **Set the three dials** — `DESIGN_VARIANCE`, `MOTION_INTENSITY`, `VISUAL_DENSITY` (baseline 8/6/4).
3. **Build** — consult references/ for each domain. Never produce placeholders.
4. **Verify** — run the pre-flight checklist (references/preflight.md).

**Animation standard:** Emil Kowalski's philosophy is the definitive source for all motion decisions. Where other sources conflict, Emil wins.

## The Three Dials

| Dial | 1 | 5 | 10 |
|---|---|---|---|
| **DESIGN_VARIANCE** | Perfect Symmetry | Moderate offset | Artsy Chaos |
| **MOTION_INTENSITY** | Static | Fluid CSS transitions | Cinematic / Physics |
| **VISUAL_DENSITY** | Art Gallery / Airy | Daily App | Cockpit / Packed |

**Baseline: 8 / 6 / 4.** Infer from brief: minimalist/clean → lower motion/density; premium consumer → high variance, medium motion; Awwwards/agency → high all; trust-first → low all. See [references/workflow.md](references/workflow.md).

## Absolute Bans

Inter as default, em-dashes, pure black/white, Fraunces/Instrument_Serif, cream/beige body bg, premium-consumer beige+brass palette, AI-purple gradients, gradient text, side-stripe borders, ghost-card pattern, glassmorphism default, 32px+ border-radius on cards, identical card grids, 3-column feature cards, nested cards, div-based fake screenshots, hand-drawn SVG illustrations, eyebrow on every section, numbered section markers, scroll cues, weather strips, version labels on marketing, decorative dots, `repeating-linear-gradient` stripes, `window.addEventListener('scroll')`, animating layout properties (top/left/width/height), placeholder-as-label, custom mouse cursors, `transition: all`, `ease-in` on UI elements.

## Emil's Easing Curves

```css
--ease-out: cubic-bezier(0.23, 1, 0.32, 1);       /* UI interactions */
--ease-in-out: cubic-bezier(0.77, 0, 0.175, 1);    /* on-screen movement */
--ease-drawer: cubic-bezier(0.32, 0.72, 0, 1);     /* iOS-like drawer */
```

Never use `ease-in` for UI. Never use `transition: all`. Never use built-in CSS easings — always use custom cubic-bezier. UI animations must stay under 300ms.

## Duration Caps

Button press: 100-160ms | Tooltips/popovers: 125-200ms | Dropdowns/selects: 150-250ms | Modals/drawers: 200-500ms | UI max: 300ms

## Default Stack

React/Next.js (RSC default), Tailwind v4, Motion (`motion/react`), next/font. Icons: Phosphor > HugeIcons > Radix > Tabler (one family per project).

## References

| File | Covers |
|---|---|
| [workflow.md](references/workflow.md) | Brief inference, design read, dials deep, design system map |
| [typography.md](references/typography.md) | Font selection, type scale, pairings, em-dash ban |
| [color.md](references/color.md) | Color strategy, OKLCH, cream/beige ban, premium-consumer palette, dark mode |
| [layout.md](references/layout.md) | Hero discipline, layout rules, spacing, cards |
| [motion.md](references/motion.md) | Emil's complete animation standards (all subsections) |
| [code.md](references/code.md) | React composition, component building patterns, UI state cycles, forms |
| [ai-tells.md](references/ai-tells.md) | Complete forbidden patterns catalog |
| [performance.md](references/performance.md) | GPU-safe animation, Core Web Vitals, a11y, dependency check |
| [preflight.md](references/preflight.md) | Full pre-flight checklist |
| [EXAMPLES.md](../EXAMPLES.md) | Copy-safe code patterns |
