---
name: vg-design
description: "Production-grade frontend design engineering with Emil Kowalski's animation philosophy, anti-slop UI patterns, premium visual design, and full-output enforcement. Use when designing, building, reviewing, or refactoring frontend interfaces: landing pages, portfolios, dashboards, product UI, forms, animations, design systems, UI audits, redesigns, or any frontend task requiring taste-driven output."
version: 1.2.0
---

# vg-design

## Decision Framework

Every build follows this chain. Rules compound — later steps never override earlier ones.

```
Brief → Design Read (one line)
     → Content structure (audience, job, objections, proof — see content-strategy.md)
     → Set Dials: VARIANCE / MOTION / DENSITY (baseline 8/6/4)
     → Layout priorities (hero shape, section cadence, bento/zigzag/cards)
     → Component choices (system pick, double-bezel threshold, pattern selection)
     → Motion layer (Emil's framework: should it animate? → purpose? → easing? → duration?)
     → Verify: critical preflight → visual QA (browser screenshots, responsive, contrast)
```

## Precedence Model

When sources conflict: **accessibility/performance** > **project constraints** > **Emil's motion standard** > **layout/typography rules** > **aesthetic preferences**.

Known conflict resolutions:
- `transition: all` is banned even when MOTION_INTENSITY says "CSS transitions." Specify exact properties.
- `ease-out` CSS keyword → use `cubic-bezier(0.23, 1, 0.32, 1)` for UI movement. Use `linear` for constant/scrubbed motion. `ease` is acceptable only for deliberate color-only micro-transitions (< 200ms).
- Duration ceiling: UI animations max 300ms regardless of MOTION_INTENSITY dial.

## The Three Dials

| Dial | 1 | 5 | 10 |
|---|---|---|---|
| **DESIGN_VARIANCE** | Perfect Symmetry | Moderate offset | Artsy Chaos |
| **MOTION_INTENSITY** | Static | Fluid CSS transitions | Cinematic / Physics |
| **VISUAL_DENSITY** | Art Gallery / Airy | Daily App | Cockpit / Packed |

Infer from brief. See [references/workflow.md](references/workflow.md) for the full inference table and use-case presets.

## Implementation Discovery (Before Building)

Always inspect before applying defaults:
1. Check `package.json` for installed libraries (icons, motion, design system packages)
2. Read existing components, CSS tokens, theme files, and conventions
3. Confirm the project's actual icon library, font stack, and design system
4. Match existing patterns unless the brief calls for a break

## Absolute Bans — Tier 0

These are vg-design defaults. They defer to the precedence model: if the project uses Inter, beige, Lucide, or a design system with pure tokens, that constraint outranks the aesthetic ban. Document the exception.

**Typography:** Inter as default, em-dashes (`—`), Fraunces/Instrument_Serif default
**Layout:** Identical 3-column card grids, nested cards, eyebrow on every section, numbered section markers, centered hero when VARIANCE > 4 and not editorial
**Visual:** Pure black/white (as vg-design default), AI-purple gradients, gradient text, div-based fake screenshots, cream/beige body bg, beige+brass palette, glassmorphism default, 32px+ radius on cards, side-stripe borders, ghost-card pattern, hand-drawn SVGs, decorative dots
**Motion:** `transition: all`, `ease-in` on UI, animating layout properties, `window.addEventListener('scroll')`
**Code:** `// TODO`, `// ...`, placeholder-as-label, custom mouse cursors
**Copy:** Scroll cues, weather strips, version labels on marketing, "Quietly in use at", em-dashes

**Escape hatches:** When an existing project, brand, or design system mandates a Tier-0 item, follow Tier 2 of the precedence model. Document the exception. Do not fight a design system that ships with Inter or uses a specific palette.

See [references/ai-tells.md](references/ai-tells.md) for the full tiered catalog (Tiers 0/1/2).

## Emil's Easing Curves

```css
--ease-out: cubic-bezier(0.23, 1, 0.32, 1);       /* directional UI movement, popovers, toasts */
--ease-in-out: cubic-bezier(0.77, 0, 0.175, 1);    /* on-screen movement, morphing */
--ease-drawer: cubic-bezier(0.32, 0.72, 0, 1);     /* iOS-like drawer, drag-to-dismiss */
```

Use `linear` for constant/scrubbed motion (marquees, scroll-driven). Use `ease` only for deliberate color-only micro-transitions under 200ms (hover bg changes). Never use `ease-in` on UI elements. Never use `transition: all`.

## Duration Caps

Button press: 100-160ms | Tooltips/popovers: 125-200ms | Dropdowns/selects: 150-250ms | Modals/drawers: 200-300ms for UI, up to 500ms only for large gesture-driven sheets | UI max: 300ms

**Default stack:** React/Next.js (RSC default), Tailwind v4, Motion (`motion/react`), next/font. Icons: Phosphor > HugeIcons > Radix > Tabler (one family per project).

## Critical Preflight (Run Every Time)

- [ ] Design Read declared. Content structure derived. Dials set.
- [ ] No undocumented Tier-0 AI tells. Project/brand/design-system exceptions noted.
- [ ] Hero fits viewport: headline ≤ 2 lines, CTA visible, no trust strip inside.
- [ ] One accent color locked. One corner-radius system. One page theme.
- [ ] No `transition: all`. All UI transitions use Emil's custom cubic-bezier.
- [ ] No elements animate from `scale(0)`. Scale-on-active used only where motion/product UI gates allow it.
- [ ] Popovers use origin-aware `transform-origin`.
- [ ] Value prop clear in first viewport? CTA intent obvious and unduplicated?
- [ ] Button contrast ≥ 4.5:1. No wrapped CTA labels at desktop.
- [ ] Content hierarchy matches business priority (most important = most prominent)?
- [ ] Reduced motion gated. Touch hover gated. `min-h-[100dvh]`, never `h-screen`.
- [ ] No placeholder patterns. Every code block complete and runnable.

Full deep audit: [references/preflight.md](references/preflight.md)

## References

- Workflow: [workflow.md](references/workflow.md), [content-strategy.md](references/content-strategy.md), [preflight.md](references/preflight.md), [visual-qa.md](references/visual-qa.md)
- Design: [tokens.md](references/tokens.md), [typography.md](references/typography.md), [color.md](references/color.md), [layout.md](references/layout.md), [assets.md](references/assets.md)
- Implementation: [motion.md](references/motion.md), [code.md](references/code.md), [product-ui.md](references/product-ui.md), [performance.md](references/performance.md), [progressive-enhancement.md](references/progressive-enhancement.md)
- Guardrails: [ai-tells.md](references/ai-tells.md), [EXAMPLES.md](EXAMPLES.md), [DISCLAIMER.md](DISCLAIMER.md)
