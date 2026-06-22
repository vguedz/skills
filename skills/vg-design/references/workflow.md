# Workflow

## 1. Brief Inference

Read these signals before any code:

1. **Page kind** — landing (SaaS / consumer / agency / event), portfolio, dashboard, redesign (preserve vs overhaul)
2. **Vibe words** — "minimalist", "Linear-style", "Awwwards", "premium consumer", "Apple-y", "brutalist", "editorial", "agency"
3. **Reference signals** — URLs, screenshots, products named, brands being compared to
4. **Audience** — technical buyers vs design-conscious consumers vs procurement panel vs recruiters
5. **Existing brand assets** — logo, color, type, photography (mandatory for redesigns)

**Output a one-line Design Read:**
> "Reading this as: \<page kind\> for \<audience\>, with a \<vibe\> language, leaning toward \<design system or aesthetic family\>."

If ambiguous, ask exactly one question. If you can confidently infer, proceed silently.

## 2. The Three Dials

### Dial Definitions

**DESIGN_VARIANCE:**
- **1-3 (Predictable):** Symmetrical CSS Grid (12-col, equal fr-units), equal paddings, centered alignment
- **4-7 (Offset):** `margin-top: -2rem` overlaps, varied image aspect ratios (4:3 next to 16:9), left-aligned headers over center-aligned data
- **8-10 (Asymmetric):** Masonry, `grid-template-columns: 2fr 1fr 1fr`, massive empty zones (`padding-left: 20vw`)
- **Mobile override:** For 4-10, asymmetric layouts above `md:` MUST collapse to `w-full`, `px-4`, `py-8` below 768px

**MOTION_INTENSITY:**
- **1-3 (Static):** No automatic animations. CSS `:hover` and `:active` states only
- **4-7 (Fluid CSS):** CSS transitions with Emil's custom cubic-beziers. Animation-delay cascades for load-ins. Focus on `transform` and `opacity`
- **8-10 (Advanced Choreography):** Complex scroll-triggered reveals, parallax, GSAP ScrollTrigger, Motion hooks. Never `window.addEventListener('scroll')`

**VISUAL_DENSITY:**
- **1-3 (Art Gallery):** `py-32` to `py-48`. Maximum breathing room
- **4-7 (Daily App):** Standard spacing `py-16` to `py-24`
- **8-10 (Cockpit):** Tight paddings. 1px lines separate data. Mandatory: `font-mono` for all numbers

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

### Use-Case Presets

| Use case | VARIANCE | MOTION | DENSITY |
|---|---|---|---|
| Landing (SaaS, mainstream) | 7 | 6 | 4 |
| Landing (Agency / creative) | 9 | 8 | 3 |
| Landing (Premium consumer) | 7 | 6 | 3 |
| Portfolio (Designer / studio) | 8 | 7 | 3 |
| Portfolio (Developer) | 6 | 5 | 4 |
| Editorial / Blog | 6 | 4 | 3 |
| Public-sector service | 3 | 2 | 5 |

## 3. Design System Map

When the brief matches a real system, use the official package. Never recreate its CSS by hand. One system per project.

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

## 4. Full-Output Enforcement

Never produce placeholders. Every code block is complete, runnable, unabridged.

**Banned:** `// ...`, `// TODO`, `// rest of code`, `/* ... */`, bare `...`, "you can extend this later", "the rest follows the same pattern", "I'll leave that as an exercise."

When approaching token limits: write at full quality to a clean breakpoint, then end with `[PAUSED — X of Y complete. Send "continue" to resume.]`

## 5. Redesign Protocol

If the task is a redesign:

1. **Detect the mode:** greenfield (no existing), preserve (modernise without breaking brand), overhaul (new visual language)
2. **Audit first:** document brand tokens, IA, content blocks, patterns to preserve, patterns to retire, SEO baseline
3. **Preserve** IA, copy voice, accessibility wins, analytics events, URL slugs, primary nav labels
4. **Modernise in order:** typography refresh → spacing & rhythm → color recalibration → motion layer → hero recomposition → full block replacement
5. **Never change silently:** URL structure, nav labels, form fields, logo, legal copy
