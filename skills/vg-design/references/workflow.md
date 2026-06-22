# Workflow

## 1. Implementation Discovery (Before Designing)

Always inspect the codebase before applying vg-design defaults. Check in order:

1. `package.json` — installed icon libraries, Motion, GSAP, design system packages, Tailwind version
2. Existing components — read a representative page/component to understand current conventions
3. CSS tokens / theme files — extract existing color, radius, shadow, font, and spacing tokens
4. Project conventions — naming patterns, file structure, client/server component boundaries

**If the project already uses Inter, beige, Lucide, or a specific design system:** that constraint outranks vg-design aesthetic preferences per the precedence model. Work within the existing system. Document exceptions rather than silently applying defaults.

## 2. Brief Inference

Read before any code:

1. **Page kind** — landing (SaaS / consumer / agency / event), portfolio, dashboard, redesign (preserve vs overhaul)
2. **Vibe words** — "minimalist", "Linear-style", "Awwwards", "premium consumer", "Apple-y", "brutalist", "editorial"
3. **Reference signals** — URLs, screenshots, products named, brands compared to
4. **Audience** — technical buyers vs design-conscious consumers vs procurement panel
5. **Existing brand assets** — logo, color, type, photography (mandatory for redesigns)

**Output a one-line Design Read:**
> "Reading this as: \<page kind\> for \<audience\>, with a \<vibe\> language, leaning toward \<design system or aesthetic family\>."

If ambiguous, ask exactly one question. If you can confidently infer, proceed silently.

## 3. Precedence Model

When sources conflict, follow this hierarchy. Lower tiers defer to upper tiers.

| Tier | Authority | Overrides |
|---|---|---|
| 1 | **Accessibility & Performance** | Everything below. WCAG AA, `prefers-reduced-motion`, Core Web Vitals. |
| 2 | **Project Constraints** | Existing design system, brand tokens, redesign preservation rules. |
| 3 | **Emil's Motion Standard** | All easing, duration, spring, and animation decisions. |
| 4 | **Layout & Typography Rules** | Hero discipline, section cadence, eyebrow restraint, card rules, font selection. |
| 5 | **Aesthetic Preferences** | Premium patterns, double-bezel, variance engine, stylistic choices. |

**Known conflict resolutions (Tier 3 overrides Tier 4):**
- `transition: all` is banned even when dial says "CSS transitions." Specify exact properties.
- `ease-out` CSS keyword → use `cubic-bezier(0.23, 1, 0.32, 1)` for UI movement. Use `linear` for constant/scrubbed motion. `ease` acceptable only for deliberate color-only micro-transitions under 200ms.
- Duration ceiling: UI animations max 300ms regardless of MOTION_INTENSITY dial.
- Aesthetic preferences (double-bezel, glass, large radii) defer to accessibility contrast rules.
- Premium-consumer palette bans override aesthetic preference for the beige+brass family.

## 4. The Three Dials

### Dial Definitions

**DESIGN_VARIANCE:**
- **1-3 (Predictable):** Symmetrical CSS Grid (12-col, equal fr-units), equal paddings, centered alignment
- **4-7 (Offset):** `margin-top: -2rem` overlaps, varied image aspect ratios, left-aligned headers over center data, asymmetric section spacing
- **8-10 (Asymmetric):** Masonry, `grid-template-columns: 2fr 1fr 1fr`, massive empty zones, pinned scroll structures
- **Mobile override:** Levels 4-10 collapse to `w-full`, `px-4`, `py-8` below 768px. No rotations, no overlaps, no negative margins.

**MOTION_INTENSITY:**
- **1-3 (Static):** No automatic animations. CSS `:hover` and `:active` only
- **4-7 (Fluid CSS):** Emil's custom cubic-beziers on transitions. Animation-delay cascades for load-ins. `whileInView` reveals
- **8-10 (Advanced):** GSAP ScrollTrigger, immersive scroll sections (horizontal pan, sticky-stack, pinned sequences), spring physics, parallax. Never `window.addEventListener('scroll')`

**VISUAL_DENSITY:**
- **1-3 (Art Gallery):** Section padding `py-32` to `py-48`. Large text, generous image sizing. Max 2 content units per section
- **4-7 (Daily App):** Standard `py-16` to `py-24`. Multiple content units, balanced image-to-text ratio
- **8-10 (Cockpit):** Tight paddings. 1px rules separate data. Mandatory: `font-mono` for numbers. Cards omitted unless structural

### Dial Inference Table

| Signal | VARIANCE | MOTION | DENSITY |
|---|---|---|---|
| "minimalist / clean / Linear-style" | 5-6 | 3-4 | 2-3 |
| "premium consumer / Apple-y" | 7-8 | 5-7 | 3-4 |
| "playful / Awwwards / agency" | 9-10 | 8-10 | 3-4 |
| "landing page / portfolio (default)" | 7-9 | 6-8 | 3-5 |
| "trust-first / public-sector" | 3-4 | 2-3 | 4-5 |
| "redesign - preserve" | match existing | +1 | match existing |
| "redesign - overhaul" | +2 | +2 | match existing |

### How Dials Gate Decisions

| Decision | Gate |
|---|---|
| Use centered hero? | Only when VARIANCE ≤ 4, or editorial/manifesto brief |
| Section padding | DENSITY 1-3 → py-32/48, 4-7 → py-16/24, 8-10 → py-8/12 |
| Card grids vs spacing | DENSITY ≤ 7 → cards acceptable. DENSITY ≥ 8 → cards banned, use dividers |
| Scroll-driven animation | MOTION ≥ 8 only |
| Stagger reveals | MOTION ≥ 5 only |
| Magnetic hover | MOTION ≥ 7 only |
| Double-bezel cards | VARIANCE ≥ 6 AND DENSITY ≤ 5 AND premium/marketing surface. Max 3 per page |
| Immersive scroll section (sticky-stack, horizontal pan) | MOTION ≥ 8 AND VARIANCE ≥ 8 AND NOT product UI. Rare — max 1 per page, never on dashboards/forms/tables |
| Glassmorphism | Only when brief explicitly calls for it. Never as default |
| Marquee | Max 1 per page. MOTION ≥ 5 |

## 5. Design System Map

When the brief matches a real system, use the official package. One system per project.

| Brief reads as… | Reach for |
|---|---|
| Microsoft / enterprise SaaS | `@fluentui/react-components` |
| Google-ish / Material | `@material/web` + M3 tokens |
| IBM-style B2B | `@carbon/react` |
| GitHub-style devtool | `@primer/css` or `@primer/react-brand` |
| Public-sector UK | `govuk-frontend` |
| US public-sector | `uswds` |
| Modern accessible React | `@radix-ui/themes` |
| Modern SaaS (own components) | shadcn/ui |
| Tailwind-based indie SaaS | Tailwind v4 utilities |

When the brief is an aesthetic with no official system, build with Tailwind + Motion + native CSS. Label borrowed inspiration honestly.

## 6. Full-Output Enforcement

Never produce placeholders. Every code block is complete, runnable, unabridged.

**Banned:** `// ...`, `// TODO`, `// rest of code`, `/* ... */`, bare `...`, "you can extend this later."

When approaching token limits: write at full quality to a clean breakpoint, then end with `[PAUSED - X of Y complete. Send "continue" to resume.]`

## 7. Reference Analysis

When a user provides inspiration (URLs, screenshots, brand names), extract concrete traits. Do not copy vibe words.

**Extract:**
- Layout structure (hero shape, section cadence, grid pattern)
- Color strategy (restrained/committed/full/drenched)
- Type hierarchy (sizes, weights, pairings, letter-spacing)
- Motion vocabulary (which elements animate, how fast, what easing)
- Content density (words per section, image-to-text ratio)
- Interactive patterns (hover states, transitions, navigation style)

**Do not copy:**
- Exact colors (extract the strategy, not the hex)
- Brand-specific fonts (extract the class: geometric sans, humanist serif, mono)
- Vibe words as design directives ("it feels expensive" → what makes it expensive?)

## 8. Redesign Protocol

1. **Detect the mode:** greenfield (no existing), preserve (modernise brand intact), overhaul (new visual, same content)
2. **Audit first:** brand tokens, IA, content blocks, patterns to preserve/retire, SEO baseline, inferred dials
3. **Preserve:** IA, copy voice, a11y wins, analytics events, URL slugs, primary nav labels
4. **Modernise in order:** typography → spacing → color → motion → hero → full block replacement
5. **Never change silently:** URL structure, nav labels, form fields, logo, legal copy
