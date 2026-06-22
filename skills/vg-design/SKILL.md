---
name: vg-design
description: Aggregated design engineering skill. Covers UI polish, animation, component architecture, anti-slop frontend, premium visual design, and full-output enforcement. Use when designing, building, reviewing, or refactoring frontend interfaces — landing pages, portfolios, dashboards, product UI, forms, animations, design systems. Triggers on "design", "UI", "UX", "style", "polish", "animation", "layout", "component", "review my UI", "audit design", "make it premium", or any frontend task requiring taste-driven output.
version: 1.0.0
bundled_from:
  - emil-design-eng
  - vercel-composition-patterns
  - impeccable
  - design-taste-frontend
  - high-end-visual-design
  - full-output-enforcement
---

# vg-design

Aggregated design engineering skill. Production-grade code, committed design choices, exceptional craft. Every rule below is contextual — read the brief, then pull only what fits.

**Animation standard:** Emil Kowalski's design engineering philosophy is the definitive source for all motion, animation, easing, and interaction decisions. Where other bundled sources conflict with Emil's standards, Emil wins.

---

## 1. WORKFLOW: BRIEF → BUILD → VERIFY

### 1.1 Brief Inference (before any code)

Read these signals first:
1. **Page kind** — landing (SaaS / consumer / agency / event), portfolio, dashboard, redesign
2. **Vibe words** — "minimalist", "Linear-style", "Awwwards", "premium consumer", "Apple-y", "brutalist", "editorial", "agency"
3. **Reference signals** — URLs, screenshots, products named, brands competing with
4. **Audience** — technical buyers vs. design-conscious consumers vs. procurement panel
5. **Existing brand assets** — logo, color, type, photography (mandatory for redesigns)

**Output a one-line "Design Read" before generating:**
> "Reading this as: \<page kind\> for \<audience\>, with a \<vibe\> language, leaning toward \<design system or aesthetic family\>."

If the brief is ambiguous, ask exactly **one** question. If you can confidently infer, don't ask — just declare and proceed.

### 1.2 The Three Dials

Set these before any layout/motion/density decision. Every rule below is gated by them.

| Dial | 1 | 5 | 10 |
|---|---|---|---|
| **DESIGN_VARIANCE** | Perfect Symmetry | Moderate offset | Artsy Chaos |
| **MOTION_INTENSITY** | Static | Fluid CSS transitions | Cinematic / Physics |
| **VISUAL_DENSITY** | Art Gallery / Airy | Daily App | Cockpit / Packed |

**Baseline:** `8 / 6 / 4`. Override per the design read:

| Signal | VARIANCE | MOTION | DENSITY |
|---|---|---|---|
| "minimalist / clean / Linear-style" | 5-6 | 3-4 | 2-3 |
| "premium consumer / Apple-y" | 7-8 | 5-7 | 3-4 |
| "playful / Awwwards / agency" | 9-10 | 8-10 | 3-4 |
| "landing page / portfolio (default)" | 7-9 | 6-8 | 3-5 |
| "trust-first / public-sector" | 3-4 | 2-3 | 4-5 |
| "redesign - preserve" | match existing | +1 | match existing |
| "redesign - overhaul" | +2 | +2 | match existing |

#### How the Dials Drive Output

**DESIGN_VARIANCE (1-10):**
- **1-3 (Predictable):** Symmetrical CSS Grid, equal paddings, centered alignment.
- **4-7 (Offset):** `margin-top: -2rem` overlaps, varied image aspect ratios, left-aligned headers over center-aligned data.
- **8-10 (Asymmetric):** Masonry, `grid-template-columns: 2fr 1fr 1fr`, massive empty zones (`padding-left: 20vw`).
- **Mobile override:** For levels 4-10, asymmetric layouts above `md:` MUST collapse to strict single-column below 768px.

**MOTION_INTENSITY (1-10):**
- **1-3 (Static):** No automatic animations. CSS `:hover` and `:active` states only.
- **4-7 (Fluid CSS):** CSS transitions with custom cubic-beziers. `animation-delay` cascades for load-ins. Focus on `transform` and `opacity`.
- **8-10 (Advanced Choreography):** Complex scroll-triggered reveals, parallax, GSAP ScrollTrigger, Motion hooks. **NEVER `window.addEventListener('scroll')`.**

**VISUAL_DENSITY (1-10):**
- **1-3 (Art Gallery):** Lots of white space. `py-32` to `py-48`.
- **4-7 (Daily App):** Standard web app spacing (`py-16` to `py-24`).
- **8-10 (Cockpit):** Tight paddings. 1px lines separate data. Mandatory: `font-mono` for all numbers.

### 1.3 Design System Map

When the brief matches a real system, **use the official package** — never recreate its CSS by hand. One system per project.

| Brief reads as… | Reach for |
|---|---|
| Microsoft / enterprise SaaS | `@fluentui/react-components` |
| Google-ish / Material | `@material/web` + M3 tokens |
| IBM-style B2B | `@carbon/react` |
| GitHub-style devtool | `@primer/css` or `@primer/react-brand` |
| Public-sector UK | `govuk-frontend` |
| US public-sector | `uswds` |
| Modern accessible React | `@radix-ui/themes` |
| Modern SaaS (own the components) | shadcn/ui |
| Tailwind-based indie SaaS | Tailwind v4 utilities |

**Default stack** (no system match): React/Next.js (RSC default), Tailwind v4, Motion (`motion/react`), next/font. Icons: Phosphor > HugeIcons > Radix > Tabler (one family per project, never hand-roll SVG icons).

### 1.4 Full-Output Enforcement

Never produce placeholders. Every code block is complete, runnable, unabridged.

**Banned patterns:** `// ...`, `// TODO`, `// rest of code`, `/* ... */`, bare `...`, "you can extend this later", "the rest follows the same pattern", "I'll leave that as an exercise."

When approaching token limits: write at full quality to a clean breakpoint, then end with `[PAUSED — X of Y complete. Send "continue" to resume.]`

---

## 2. TYPOGRAPHY

### 2.1 Font Selection

- **Inter is discouraged as default.** Reach for Geist, Outfit, Cabinet Grotesk, Satoshi, or a brand-appropriate serif first. Inter acceptable only for neutral/Linear-style or accessibility-first sites.
- **Serif is VERY discouraged as default.** "Creative brief = serif" is the #1 AI typography tell. Serif only when: the brand brief literally names a serif, OR the aesthetic is genuinely editorial/luxury/publication/heritage. Default sans-serif display for everything else.
- **Specifically BANNED as defaults:** Fraunces, Instrument_Serif (the LLM-favorite display serifs).
- **Emphasis rule:** Use italic or bold of the SAME font for word emphasis. Never inject a random serif word into a sans headline.
- **One family in multiple weights** beats pairing similar-but-not-identical fonts.
- **Italic descender clearance (mandatory):** When italic display type contains descender letters (`y g j p q`), use `leading-[1.1]` minimum + `pb-1` reserve. `leading-none` will clip.

### 2.2 Type Scale

- Hero/display heading ceiling: `clamp()` max ≤ 6rem (~96px).
- Display letter-spacing floor: ≥ -0.04em. Default is -0.02 to -0.03em for tight grotesque display.
- Body line length: cap at 65-75ch.
- `text-wrap: balance` on h1-h3; `text-wrap: pretty` on long prose.
- **Default scale:** Display `text-4xl md:text-6xl tracking-tighter leading-none`. Body `text-base text-gray-600 leading-relaxed max-w-[65ch]`.

### 2.3 Font Pairings

`Geist` + `Geist Mono`, `Satoshi` + `JetBrains Mono`, `Cabinet Grotesk` + `Inter Tight`, `GT America` + `IBM Plex Mono`.

### 2.4 Em-Dash Ban (NON-NEGOTIABLE)

**`—` (em-dash) is COMPLETELY banned everywhere.** Headlines, eyebrows, pills, body, quotes, attribution, captions, buttons, alt text. Use hyphen `-`, period, comma, colon, or parentheses instead. Zero em-dashes. Zero en-dashes (`–`) as separators. This is the #1 AI visual tell.

---

## 3. COLOR

### 3.1 Color Strategy (pick before picking colors)

| Level | Description |
|---|---|
| **Restrained** | Tinted neutrals + one accent ≤10%. Product default. |
| **Committed** | One saturated color carries 30-60% of surface. Brand default. |
| **Full palette** | 3-4 named roles, used deliberately. Campaigns; data viz. |
| **Drenched** | The surface IS the color. Brand heroes. |

### 3.2 Core Rules

- **Use OKLCH** for new projects.
- **One accent color per project.** Saturation < 80% by default. Once chosen, lock it — audit every component before shipping.
- **The LILA Rule:** AI-purple/blue glow aesthetic is discouraged as default. No automatic purple button glows, no random neon gradients. Use neutral bases (Zinc/Slate/Stone) with high-contrast singular accents.
- **No pure `#000000` or `#ffffff`.** Off-black and off-white always.
- **Verify contrast.** Body ≥ 4.5:1, large text ≥ 3:1. Placeholder text needs 4.5:1 too. Gray text on colored backgrounds: use a darker shade of the background's hue.
- **One palette per project.** Don't fluctuate between warm and cool grays.

### 3.3 The Cream/Beige Ban (AI Default)

**The warm-neutral band (OKLCH L 0.84-0.97, C < 0.06, hue 40-100) is banned as default body bg.** Token names like `--paper`, `--cream`, `--sand`, `--bone`, `--linen` are tells. Alternatives: saturated brand color as body, true off-white at chroma 0, or darker mid-tone tinted neutral.

### 3.4 Premium-Consumer Palette Ban

For cookware/wellness/artisan/luxury/DTC briefs, the AI-default beige+brass+oxblood+espresso palette (`#f5f1ea`, `#b08947`, `#9a2436`, `#1a1714` family) is BANNED. Rotate from: Cold Luxury (silver+chrome), Forest (deep green+bone+amber), Black and Tan (off-black+warm tan), Cobalt+Cream, Terracotta+Slate, Olive+Brick, or Pure monochrome+single saturated pop.

### 3.5 Dark Mode / Theme

- **Dual-mode by default.** Never ship light-only or dark-only without explicit instruction.
- Choose theme by writing one sentence of physical scene: who uses this, where, under what ambient light, in what mood.
- **Page Theme Lock:** ONE theme for the whole page. No section flips to inverted mode mid-page. Section-level background tints within the same theme family are fine; flipping to the opposite family is broken.

---

## 4. LAYOUT & SPACING

### 4.1 Hero Discipline

- **Hero MUST fit the initial viewport.** Headline max 2 lines on desktop, subtext max 20 words AND max 4 lines, CTAs visible without scroll.
- **Hero font-scale:** `text-4xl md:text-5xl lg:text-6xl` for most heroes; `text-6xl md:text-7xl` only when headline is 3-5 words.
- **Hero top padding cap:** max `pt-24` (~6rem) at desktop.
- **Hero stack max 4 text elements:** eyebrow OR brand strip (pick zero or one), headline, subtext, CTAs (1 primary + max 1 secondary). No taglines below CTAs, no trust micro-strips, no feature bullets in the hero.
- **"Used by" / "Trusted by" logo wall lives UNDER the hero, never inside it.**
- **Hero needs a real visual.** Text + gradient blob is not a hero. Use image-gen tool first, Picsum second, then explicit placeholder slots.

### 4.2 Layout Rules

- **Anti-center bias:** Centered hero/H1 avoided when `DESIGN_VARIANCE > 4`. Force split-screen, asymmetric whitespace, or scroll-pinned structures.
- **Section-layout repetition ban:** Each layout family appears at most ONCE per page. 8 sections → ≥ 4 different layout families.
- **Zigzag alternation cap:** Max 2 consecutive image+text split sections. The 3rd is a fail.
- **3-column equal feature cards are banned.** Use 2-column zig-zag, asymmetric grid, bento, or horizontal-scroll.
- **Bento grids must have rhythm.** Not 6 white-on-white text cards. ≥ 2-3 cells need real visual variation (image, gradient, pattern). Exact cell count: N items → N cells, no empty cells.
- **Eyebrow restraint:** Max 1 eyebrow per 3 sections. Hero counts as 1. Count mechanically: instances of `uppercase tracking` micro-labels ≤ ceil(sectionCount / 3).
- **Split-header ban:** "Left big headline + right small explainer paragraph" as section header is banned as default. Stack vertically.
- **Navigation:** ONE line at desktop, height ≤ 80px.

### 4.3 Spacing

- Vary spacing for rhythm. Section padding: `py-24` to `py-40`.
- Flexbox for 1D, Grid for 2D. `repeat(auto-fit, minmax(280px, 1fr))` for responsive grids.
- `min-h-[100dvh]` never `h-screen` (iOS Safari viewport jumping).
- Semantic z-index scale (dropdown → sticky → modal-backdrop → modal → toast → tooltip). Never arbitrary 999/9999.
- Standard breakpoints: `sm 640, md 768, lg 1024, xl 1280, 2xl 1536`.

### 4.4 Cards

- Cards are the lazy answer. Use only when elevation communicates real hierarchy. Prefer `border-t`, `divide-y`, or negative space.
- **No nested cards. Ever.**
- **Shape consistency lock:** Pick ONE corner-radius scale (all-sharp, all-soft 12-16px, or all-pill). Mixed only with documented rules. Cards top out at 12-16px; full-pill for tags/buttons only.
- **Ghost-card ban:** `border: 1px solid X` + `box-shadow: 0 Npx Mpx ...` with M ≥ 16px on the same element. Pick one.
- When shadow is used, tint it to the background hue. No pure-black drop shadows.

---

## 5. MOTION & ANIMATION — Emil Kowalski Standard

**This section is the definitive animation authority.** Emil's philosophy governs all motion decisions. Where other bundled sources conflict, Emil wins.

### 5.1 Core Philosophy

**Taste is trained, not innate.** Good taste is the ability to see beyond the obvious. Study why the best interfaces feel the way they do. Reverse engineer animations. Inspect interactions. Be curious.

**Unseen details compound.** Most details users never consciously notice — that is the point. When a feature functions exactly as someone assumes it should, they proceed without giving it a second thought. The aggregate of invisible correctness creates interfaces people love without knowing why.

**Beauty is leverage.** People select tools based on the overall experience, not just functionality. Good defaults and good animations are real differentiators.

### 5.2 The Animation Decision Framework

Before writing any animation code, answer these questions in order:

**1. Should this animate at all?**

Ask: How often will users see this animation?

| Frequency | Decision |
|---|---|
| 100+ times/day (keyboard shortcuts, command palette toggle) | No animation. Ever. |
| Tens of times/day (hover effects, list navigation) | Remove or drastically reduce |
| Occasional (modals, drawers, toasts) | Standard animation |
| Rare/first-time (onboarding, celebrations) | Can add delight |

**Never animate keyboard-initiated actions.** They are repeated hundreds of times daily. Animation makes them feel slow, delayed, and disconnected. Raycast has no open/close animation — that is the optimal experience.

**2. What is the purpose?**

Every animation must have a clear answer to "why does this animate?"

Valid purposes:
- **Spatial consistency**: toast enters and exits from the same direction
- **State indication**: a morphing button shows the state change
- **Explanation**: a marketing animation that shows how a feature works
- **Feedback**: a button scales down on press, confirming the interface heard the user
- **Preventing jarring changes**: elements appearing/disappearing without transition feel broken

If the purpose is just "it looks cool" and the user will see it often, don't animate.

**3. What easing should it use?**

```
Element entering or exiting?
  Yes → ease-out (starts fast, feels responsive)
  No →
    Moving/morphing on screen?
      Yes → ease-in-out (natural acceleration/deceleration)
    Hover/color change?
      Yes → ease
    Constant motion (marquee, progress bar)?
      Yes → linear
    Default → ease-out
```

### 5.3 Easing Curves (Emil Kowalski Standard)

**Never use ease-in for UI animations.** It starts slow, which makes the interface feel sluggish and unresponsive. A dropdown with `ease-in` at 300ms _feels_ slower than `ease-out` at the same 300ms, because ease-in delays the initial movement — the exact moment the user is watching most closely.

**Use custom cubic-bezier curves.** Built-in CSS easings are too weak. They lack the punch that makes animations feel intentional. Use [easing.dev](https://easing.dev/) or [easings.co](https://easings.co/) to find stronger custom variants.

```css
/* Emil's curves — the definitive set */
--ease-out: cubic-bezier(0.23, 1, 0.32, 1);       /* UI interactions: buttons, toasts, popovers */
--ease-in-out: cubic-bezier(0.77, 0, 0.175, 1);    /* on-screen movement, morphing */
--ease-drawer: cubic-bezier(0.32, 0.72, 0, 1);     /* iOS-like drawer (from Ionic Framework) */
```

**CRITICAL: Never use `transition: all`.** Specify exact properties. `transition: all` triggers unnecessary style recalculations. Always write `transition: transform 200ms ease-out`, not `transition: all 200ms`.

### 5.4 Duration Reference (Emil Kowalski Standard)

| Element | Duration |
|---|---|
| Button press feedback | 100-160ms |
| Tooltips, small popovers | 125-200ms |
| Dropdowns, selects | 150-250ms |
| Modals, drawers | 200-500ms |
| Marketing/explanatory | Can be longer |

**Rule: UI animations should stay under 300ms.** A 180ms dropdown feels more responsive than a 400ms one. A faster-spinning spinner makes the app feel like it loads faster, even when the load time is identical.

### 5.5 Perceived Performance

Speed in animation is not just about feeling snappy — it directly affects how users perceive your app's performance:
- A **fast-spinning spinner** makes loading feel faster (same load time, different perception)
- A **180ms select** animation feels more responsive than a **400ms** one
- **Instant tooltips** after the first one is open (skip delay + skip animation) make the whole toolbar feel faster

The perception of speed matters as much as actual speed. Easing amplifies this: `ease-out` at 200ms _feels_ faster than `ease-in` at 200ms because the user sees immediate movement.

### 5.6 CSS Transform Mastery

#### translateY with percentages
Percentage values in `translate()` are relative to the element's own size. Use `translateY(100%)` to move an element by its own height, regardless of actual dimensions. Prefer percentages over hardcoded pixel values — they are less error-prone and adapt to content.

```css
/* Works regardless of drawer height */
.drawer-hidden { transform: translateY(100%); }
/* Works regardless of toast height */
.toast-enter { transform: translateY(-100%); }
```

#### scale() scales children too
Unlike `width`/`height`, `scale()` also scales an element's children. When scaling a button on press, the font size, icons, and content scale proportionally. This is a feature, not a bug.

#### 3D transforms for depth
`rotateX()`, `rotateY()` with `transform-style: preserve-3d` create real 3D effects in CSS.

```css
.wrapper { transform-style: preserve-3d; }

@keyframes orbit {
  from { transform: translate(-50%, -50%) rotateY(0deg) translateZ(72px) rotateY(360deg); }
  to   { transform: translate(-50%, -50%) rotateY(360deg) translateZ(72px) rotateY(0deg); }
}
```

#### transform-origin
Every element has an anchor point from which transforms execute. The default is center. Set it to match where the trigger lives for origin-aware interactions.

### 5.7 clip-path for Animation

`clip-path` is not just for shapes. It is one of the most powerful animation tools in CSS.

#### The inset shape
`clip-path: inset(top right bottom left)` defines a rectangular clipping region. Each value "eats" into the element from that side.

```css
/* Reveal from left to right */
.overlay {
  clip-path: inset(0 100% 0 0);
  transition: clip-path 200ms ease-out;
}
.button:active .overlay {
  clip-path: inset(0 0 0 0);
  transition: clip-path 2s linear;
}
```

#### Tabs with perfect color transitions
Duplicate the tab list. Style the copy as "active" (different background, different text color). Clip the copy so only the active tab is visible. Animate the clip on tab change. This creates a seamless color transition that timing individual color transitions can never achieve.

#### Hold-to-delete pattern
Use `clip-path: inset(0 100% 0 0)` on a colored overlay. On `:active`, transition to `inset(0 0 0 0)` over 2s with linear timing. On release, snap back with 200ms ease-out. Add `scale(0.97)` on the button for press feedback.

#### Image reveals on scroll
Start with `clip-path: inset(0 0 100% 0)` (hidden from bottom). Animate to `inset(0 0 0 0)` when the element enters the viewport.

#### Comparison sliders
Overlay two images. Clip the top one with `clip-path: inset(0 50% 0 0)`. Adjust the right inset value based on drag position. No extra DOM elements needed, fully hardware-accelerated.

### 5.8 Spring Animations

Springs feel more natural than duration-based animations because they simulate real physics. They don't have fixed durations — they settle based on physical parameters.

**When to use springs:**
- Drag interactions with momentum
- Elements that should feel "alive" (like Apple's Dynamic Island)
- Gestures that can be interrupted mid-animation
- Decorative mouse-tracking interactions

**Spring-based mouse interactions:** Tying visual changes directly to mouse position feels artificial because it lacks motion. Use `useSpring` from Motion to interpolate value changes with spring-like behavior instead of updating immediately.

**Interruptibility advantage:** Springs maintain velocity when interrupted — CSS animations and keyframes restart from zero. This makes springs ideal for gestures users might change mid-motion.

```js
// Apple's approach (recommended — easier to reason about)
{ type: "spring", duration: 0.5, bounce: 0.2 }

// Traditional physics (more control)
{ type: "spring", mass: 1, stiffness: 100, damping: 10 }
```

Keep bounce subtle (0.1-0.3) when used. Avoid bounce in most UI contexts. Use it for drag-to-dismiss and playful interactions.

### 5.9 Component Animation Rules

#### Buttons must feel responsive
Every pressable element: `transform: scale(0.97)` on `:active`, `transition: transform 160ms ease-out`. This gives instant feedback. The scale should be subtle (0.95-0.98).

```css
.button { transition: transform 160ms ease-out; }
.button:active { transform: scale(0.97); }
```

#### Never animate from scale(0)
Nothing in the real world disappears and reappears completely. Start from `scale(0.9)` or higher, combined with opacity. Even a barely-visible initial scale makes the entrance feel more natural.

```css
/* Bad */
.entering { transform: scale(0); }
/* Good */
.entering { transform: scale(0.95); opacity: 0; }
```

#### Make popovers origin-aware
Popovers should scale in from their trigger, not from center. The default `transform-origin: center` is wrong for almost every popover. **Exception: modals.** Modals should keep `transform-origin: center` because they are not anchored to a specific trigger.

```css
/* Radix UI */
.popover { transform-origin: var(--radix-popover-content-transform-origin); }
/* Base UI */
.popover { transform-origin: var(--transform-origin); }
```

#### Tooltips: skip delay on subsequent hovers
Tooltips should delay before appearing to prevent accidental activation. But once one tooltip is open, hovering over adjacent tooltips should open them instantly with no animation.

```css
.tooltip {
  transition: transform 125ms ease-out, opacity 125ms ease-out;
  transform-origin: var(--transform-origin);
}
.tooltip[data-starting-style],
.tooltip[data-ending-style] { opacity: 0; transform: scale(0.97); }
.tooltip[data-instant] { transition-duration: 0ms; }
```

#### Use CSS transitions over keyframes for interruptible UI
CSS transitions can be interrupted and retargeted mid-animation. Keyframes restart from zero. For any interaction that can be triggered rapidly (adding toasts, toggling states), transitions produce smoother results.

#### Use blur to mask imperfect transitions
When a crossfade between two states feels off, add subtle `filter: blur(2px)` during the transition. Blur bridges the visual gap by blending the two states together, tricking the eye into perceiving a single smooth transformation. Keep blur under 20px. Heavy blur is expensive, especially in Safari.

```css
.button-content { transition: filter 200ms ease, opacity 200ms ease; }
.button-content.transitioning { filter: blur(2px); opacity: 0.7; }
```

#### Animate enter states with @starting-style
The modern CSS way to animate element entry without JavaScript:

```css
.toast {
  opacity: 1; transform: translateY(0);
  transition: opacity 400ms ease, transform 400ms ease;
  @starting-style { opacity: 0; transform: translateY(100%); }
}
```

This replaces the common React pattern of using `useEffect` to set `mounted: true` after initial render.

### 5.10 Asymmetric Enter/Exit Timing

Pressing should be slow when it needs to be deliberate (hold-to-delete: 2s linear), but release should always be snappy (200ms ease-out). This pattern applies broadly: slow where the user is deciding, fast where the system is responding.

```css
.overlay { transition: clip-path 200ms ease-out; }         /* Release: fast */
.button:active .overlay { transition: clip-path 2s linear; } /* Press: slow and deliberate */
```

### 5.11 Stagger Animations

When multiple elements enter together, stagger their appearance. Each element animates in with a small delay after the previous one. Keep stagger delays short (30-80ms between items). Long delays make the interface feel slow. Stagger is decorative — never block interaction while stagger animations are playing.

```css
.item { opacity: 0; transform: translateY(8px); animation: fadeIn 300ms ease-out forwards; }
.item:nth-child(1) { animation-delay: 0ms; }
.item:nth-child(2) { animation-delay: 50ms; }
.item:nth-child(3) { animation-delay: 100ms; }
.item:nth-child(4) { animation-delay: 150ms; }

@keyframes fadeIn { to { opacity: 1; transform: translateY(0); } }
```

### 5.12 Gesture & Drag Interactions

#### Momentum-based dismissal
Don't require dragging past a threshold. Calculate velocity: `Math.abs(dragDistance) / elapsedTime`. If velocity exceeds ~0.11, dismiss regardless of distance. A quick flick should be enough.

#### Damping at boundaries
When a user drags past the natural boundary, apply damping. The more they drag, the less the element moves. Things in real life don't suddenly stop; they slow down first.

#### Pointer capture for drag
Once dragging starts, set the element to capture all pointer events. This ensures dragging continues even if the pointer leaves the element bounds.

#### Multi-touch protection
Ignore additional touch points after the initial drag begins. Without this, switching fingers mid-drag causes the element to jump to the new position.

### 5.13 Performance Rules (Emil Kowalski Standard)

#### Only animate transform and opacity
These properties skip layout and paint, running on the GPU. Animating `padding`, `margin`, `height`, or `width` triggers all three rendering steps.

#### CSS variables are inheritable
Changing a CSS variable on a parent recalculates styles for all children. In a drawer with many items, updating `--swipe-amount` on the container causes expensive style recalculation. Update `transform` directly on the element instead.

```js
// Bad: triggers recalc on all children
element.style.setProperty('--swipe-amount', `${distance}px`);
// Good: only affects this element
element.style.transform = `translateY(${distance}px)`;
```

#### Framer Motion hardware acceleration caveat
Framer Motion's shorthand properties (`x`, `y`, `scale`) are NOT hardware-accelerated. They use `requestAnimationFrame` on the main thread. For hardware acceleration, use the full `transform` string:

```jsx
// NOT hardware accelerated (convenient but drops frames under load)
<motion.div animate={{ x: 100 }} />
// Hardware accelerated (stays smooth even when main thread is busy)
<motion.div animate={{ transform: "translateX(100px)" }} />
```

#### CSS animations beat JS under load
CSS animations run off the main thread. When the browser is busy loading a new page, Framer Motion animations (using `requestAnimationFrame`) drop frames. CSS animations remain smooth. Use CSS for predetermined animations; JS for dynamic, interruptible ones.

#### Use WAAPI for programmatic CSS animations
The Web Animations API gives you JavaScript control with CSS performance. Hardware-accelerated, interruptible, and no library needed.

```js
element.animate(
  [{ clipPath: 'inset(0 0 100% 0)' }, { clipPath: 'inset(0 0 0 0)' }],
  { duration: 1000, fill: 'forwards', easing: 'cubic-bezier(0.77, 0, 0.175, 1)' }
);
```

### 5.14 Reduced Motion (Mandatory)

Animations can cause motion sickness. Reduced motion means fewer and gentler animations, not zero. Keep opacity and color transitions that aid comprehension. Remove movement and position animations.

```css
@media (prefers-reduced-motion: reduce) {
  .element { animation: fade 0.2s ease; /* No transform-based motion */ }
}
```

```jsx
const shouldReduceMotion = useReducedMotion();
const closedX = shouldReduceMotion ? 0 : '-100%';
```

#### Touch device hover states
Touch devices trigger hover on tap, causing false positives. Gate hover animations behind a media query:

```css
@media (hover: hover) and (pointer: fine) {
  .element:hover { transform: scale(1.05); }
}
```

### 5.15 Motion Library Choice

- **Motion (`motion/react`)** — default for UI/state-change motion, bento, hover.
- **GSAP + ScrollTrigger** — for full-page scrolltelling, pinning, horizontal pan. Isolate in client-leaf components with `useEffect` cleanup.
- **CSS scroll-driven animations** — lighter alternative to GSAP for simple reveals.
- **WAAPI** — for programmatic CSS animations with JS control.
- **NEVER mix GSAP/Three.js with Motion in the same component tree.** They fight over the same frames.

### 5.16 GSAP Canonical Skeletons

**Sticky-Stack:** `start: "top top"`, `pin: true`, every card except last pinned. Scale/opacity on each card driven by next card's trigger. `pinSpacing: false`.

**Horizontal-Pan:** `start: "top top"`, `pin: true` on wrapper, `scrub: 1`, `end: () => \`+=${distance}\``. Wrapper pinned, inner track slides horizontally as user scrolls vertically.

### 5.17 Forbidden Animation Patterns

- **`window.addEventListener("scroll", ...)` is banned.** Use Motion's `useScroll()`, GSAP `ScrollTrigger`, IntersectionObserver, or CSS `animation-timeline: view()`.
- **Custom `window.scrollY` in React state.** Re-renders on every frame.
- **`requestAnimationFrame` touching React state.** Use motion values (`useMotionValue` + `useTransform`).
- **`transition: all` is banned.** Always specify exact properties.
- **`ease-in` on UI elements is banned.** Use `ease-out` or custom curve.
- **Keyframes on rapidly-triggered elements** — use CSS transitions for interruptibility.
- **Animating layout properties** (`top`, `left`, `width`, `height`) — animate only `transform` and `opacity`.

### 5.18 The Sonner Principles (Building Loved Components)

1. **Developer experience is key.** No hooks, no context, no complex setup. Insert once, call from anywhere.
2. **Good defaults matter more than options.** Ship beautiful out of the box. Most users never customize.
3. **Handle edge cases invisibly.** Pause timers when tab is hidden. Fill gaps between stacked elements. Users never notice these, and that is exactly right.
4. **Use transitions, not keyframes, for dynamic UI.** Items are added rapidly. Keyframes restart from zero on interruption. Transitions retarget smoothly.
5. **Cohesion matters.** The easing and duration must fit the vibe of the component. Match the motion to the mood. A playful component can be bouncier. A professional dashboard should be crisp and fast.

### 5.19 Debugging Animations

#### Slow motion testing
Play animations at reduced speed to spot issues invisible at full speed. Temporarily increase duration to 2-5x normal, or use browser DevTools animation inspector.

Things to look for:
- Do colors transition smoothly, or do you see two distinct states overlapping?
- Does the easing feel right, or does it start/stop abruptly?
- Is the transform-origin correct?
- Are multiple animated properties (opacity, transform, color) in sync?

#### Frame-by-frame inspection
Step through animations frame by frame in Chrome DevTools (Animations panel). This reveals timing issues between coordinated properties that you cannot see at full speed.

#### Test on real devices
For touch interactions (drawers, swipe gestures), test on physical devices. Connect your phone via USB, visit your local dev server by IP address, and use Safari's remote devtools.

#### Review your work the next day
Review animations with fresh eyes. You notice imperfections the next day that you missed during development.

---

## 6. REVIEW FORMAT (Required for UI Review)

When reviewing UI code, use a markdown table with **Before | After | Why** columns:

| Before | After | Why |
|---|---|---|
| `transition: all 300ms` | `transition: transform 200ms ease-out` | Specify exact properties; avoid `all` |
| `transform: scale(0)` | `transform: scale(0.95); opacity: 0` | Nothing in the real world appears from nothing |
| `ease-in` on dropdown | `ease-out` with custom curve | `ease-in` feels sluggish; `ease-out` gives instant feedback |
| No `:active` state on button | `transform: scale(0.97)` on `:active` | Buttons must feel responsive to press |
| `transform-origin: center` on popover | `transform-origin: var(--radix-popover-content-transform-origin)` | Popovers scale from trigger (modals stay centered) |
| Duration > 300ms on UI element | Reduce to 150-250ms | Faster feels more responsive |
| Framer Motion `x`/`y` under load | `transform: "translateX()"` for hardware acceleration | Shorthand uses main thread |
| Keyframes on rapidly-triggered element | CSS transitions | Transitions are interruptible; keyframes restart |
| Same enter/exit speed | Asymmetric: enter slow, exit fast | Slow where user decides, fast where system responds |

---

## 7. COMPONENT ARCHITECTURE (React)

### 7.1 Avoid Boolean Props

Don't add boolean props to customize behavior. Use composition: compound components, explicit variant components, or children.

```tsx
// Bad: boolean prop proliferation
<Card border shadow large padding />

// Good: composition
<Card>
  <Card.Header>...</Card.Header>
  <Card.Content>...</Card.Content>
</Card>
```

### 7.2 Compound Components with Shared Context

Structure complex components with shared context so siblings can communicate without prop drilling.

### 7.3 State Management

- **Provider is the only place that knows how state is managed.** Consumers receive interface.
- **Define generic interface** with `state`, `actions`, `meta` for dependency injection.
- **Lift state** into provider components for sibling access.
- **`useState` never tracks continuous input values** (mouse, scroll, physics). Use Motion's `useMotionValue` / `useTransform`.
- **Global state:** Zustand, Jotai, or React context. Only for deep prop-drilling avoidance.
- **RSC safety:** Wrap providers in `"use client"` components. Motion/scroll/pointer physics in isolated client leaves with `"use client"`.

### 7.4 React 19+ Only

- Don't use `forwardRef` (use `ref` prop directly).
- Use `use()` instead of `useContext()`.

---

## 8. COMPONENT BUILDING PATTERNS

### 8.1 The Double-Bezel (Nested Architecture)

Premium cards must use nested enclosures — outer shell + inner core — to feel like physical machined hardware, not flat CSS.

```
Outer shell: bg-black/5 or bg-white/5, ring-1 ring-black/5, p-1.5 or p-2, rounded-[2rem]
Inner core:  distinct bg, shadow-[inset_0_1px_1px_rgba(255,255,255,0.15)], rounded-[calc(2rem-0.375rem)]
```

### 8.2 Button-In-Button CTA

Primary CTAs: `rounded-full px-6 py-3`. Trailing icon (arrow) is nested inside its own circular wrapper (`w-8 h-8 rounded-full bg-black/5 dark:bg-white/10`), flush with button's right inner padding.

### 8.3 Button Responsiveness

Every pressable element: `transform: scale(0.97)` on `:active`, `transition: transform 160ms ease-out`. On hover: inner icon circle translates diagonally (`group-hover:translate-x-1 group-hover:-translate-y-[1px] scale-105`).

### 8.4 UI State Cycles (mandatory)

Always implement full cycles, not just static success:
- **Loading:** Skeletal loaders matching final layout shape. No generic spinners.
- **Empty:** Beautifully composed, indicate how to populate.
- **Error:** Clear, inline (forms), or contextual (toasts for transient).
- **Button contrast check:** Text readable against button bg (WCAG AA 4.5:1). No white-on-white, no transparent button against page bg without border.
- **CTA wrap ban:** Button text fits on ONE line at desktop. Fix by shortening label (1-2 words ideal) or widening button.
- **No duplicate CTA intent:** "Get in touch" + "Contact us" + "Let's talk" on same page = fail. One label per intent.

### 8.5 Form Patterns

- Label ABOVE input. `gap-2` for input blocks.
- No placeholder-as-label. Ever.
- Error text BELOW input.
- **Form contrast check:** Inputs, placeholders, focus rings, helper text, error text all pass WCAG AA against section background.

---

## 9. AI TELLS — FORBIDDEN PATTERNS

These are hard bans. Match-and-refuse. If you're about to write any of these, rewrite.

### 9.1 Visual & CSS

- Neon/outer glows by default. Use inner borders or tinted shadows.
- Pure black (`#000000`). Off-black always.
- Oversaturated accents. Desaturate to blend.
- Excessive gradient text (`background-clip: text` + gradient). Use solid color, weight, or size.
- Custom mouse cursors. Accessibility-hostile, perf-hostile.
- `repeating-linear-gradient(...)` stripe backgrounds.
- Hand-drawn / sketchy SVG illustrations (`feTurbulence`, `feDisplacementMap`, "doodle", "wavy" classes). Ship real assets or ship no illustration.
- Div-based fake screenshots (fake task lists, dashboards, terminals built from styled divs). #1 LLM-design tell. Use real screenshots, generated images, or skip entirely.
- `border-radius: 32px+` on cards/sections/inputs. Cards top out at 12-16px. The codex over-rounds.
- Side-stripe borders (`border-left`/`border-right` > 1px as colored accent). Rewrite with full borders, background tints, or nothing.
- Glassmorphism as default. Rare and purposeful, or nothing.
- Filled progress-bar tracks (`bg-zinc-200` background track with partial fill) on landing pages. Dashboard clutter.

### 9.2 Typography

- Inter as default (see §2.1 for override path).
- Em-dashes (`—`) anywhere (see §2.4 — zero tolerance).
- `<br>`-broken-and-italicized headlines as default design move. Headlines read naturally first.

### 9.3 Layout & Spacing

- The hero-metric template (big number + small label + supporting stats + gradient accent).
- Identical card grids (same-sized cards with icon + heading + text, repeated endlessly).
- Tiny uppercase tracked eyebrow above every section. Max 1 per 3 sections.
- Numbered section markers as default scaffolding (`01 / About`, `02 / Process`).
- 3-column equal feature cards. Banned.
- Overlapping card rotations on mobile (< 768px). Touch-target conflicts.

### 9.4 Content & Data

- Generic names ("John Doe", "Sarah Chan"). Use creative, realistic, locale-appropriate names.
- Generic avatars (SVG "egg", Lucide user icons). Use photo placeholders or specific styling.
- Fake-perfect numbers (`99.99%`, `50%`). Use organic data (`47.2%`).
- Startup-slop brand names ("Acme", "Nexus", "SmartFlow"). Invent premium-sounding names.
- Filler verbs ("Elevate", "Seamless", "Unleash"). Concrete verbs only.

### 9.5 Copy Tells

- "Quietly in use at" / "Quietly trusted by" social-proof headers. Use plain language.
- "From the field" / "Field notes" / "On our desks" poetic labels. Use functional labels.
- Weather/locale strips ("LIS 14:23 · 18°C"). Banned unless brief is place-focused.
- Version labels in hero (V0.6, BETA, INVITE-ONLY). Only for actual launch briefs.
- Version footers on marketing pages (`v1.4.2`, `Build 0048`). CLI tool fixtures.
- Micro-meta-sentences under eyebrows. Clutter.
- Generic step labels ("Stage 1 / Stage 2"). Verb-noun directly.
- Pills/labels overlaid on images.
- Photo-credit captions as decoration ("Field study no. 12 · Ines Caetano").
- Scroll cues ("↓ scroll", "Scroll to explore").
- Decorative colored status dots on nav/list/badge. Only for real semantic state.
- Decoration text strip at hero bottom (`BRAND. MOTION. SPATIAL.`).
- The em-dash character (`—`) in any visible text (see §2.4).

### 9.6 Structural

- Logo wall = logos only. No industry/category labels below each logo. Use Simple Icons CDN or generated SVG marks — not plain text wordmarks.
- `border-t` + `border-b` on every row of long lists/spec tables. Use card-per-spec, grouped chunks, or accordion.
- Long spec sheets with hairline under every row. Banned. Alternative UI components required.
- The middle-dot (`·`) is rationed. Max 1 per line in metadata strips.
- Section-numbering eyebrows (`00 / INDEX`, `001 · Capabilities`).

---

## 10. PERFORMANCE & ACCESSIBILITY

### 10.1 GPU-Safe Animation
- Animate ONLY `transform` and `opacity`.
- `will-change: transform` sparingly, only on elements that will actually animate.
- CSS variables are inheritable — updating `--swipe-amount` on container recalc children. Use `transform` directly.
- `backdrop-blur` only on fixed/sticky elements. Never on scrolling containers.
- Noise/grain filters exclusively on fixed `pointer-events-none` pseudo-elements.

### 10.2 Core Web Vitals
- **LCP** < 2.5s (hero image `priority` or preloaded).
- **INP** < 200ms (heavy work off main thread).
- **CLS** < 0.1 (reserve space for images, fonts, embeds).

### 10.3 Accessibility
- `prefers-reduced-motion: reduce` for all motion > MOTION_INTENSITY 3.
- `prefers-color-scheme: dark` respected by default.
- Touch hover states: `@media (hover: hover) and (pointer: fine)`.
- Semantic z-index scale. Never arbitrary values.

### 10.4 Dependency Verification
**Before importing ANY 3rd-party library, check `package.json`.** If missing, output the install command first. Never assume a library exists.

---

## 11. PRE-FLIGHT CHECK (Mandatory)

Run this matrix before delivering ANY frontend output.

### Design Integrity
- [ ] Design Read declared (one-liner)?
- [ ] Dial values explicit and reasoned from brief?
- [ ] Design system chosen, OR aesthetic labeled honestly?
- [ ] One accent color, locked across all sections?
- [ ] One corner-radius system, applied consistently?
- [ ] Page Theme Locked (one theme, no mid-page flips)?
- [ ] Serif discipline check (no Fraunces/Instrument_Serif default)?
- [ ] Premium-consumer palette check (no beige+brass default)?

### Typography
- [ ] ZERO em-dashes (`—`) anywhere?
- [ ] Inter not the default (unless justified)?
- [ ] Italic descender clearance on all italic display with descenders?
- [ ] Body line length ≤ 75ch?

### Layout
- [ ] Hero fits viewport (headline ≤ 2 lines, subtext ≤ 20 words, CTA visible)?
- [ ] Hero top padding ≤ pt-24?
- [ ] Hero stack ≤ 4 text elements, no trust strip inside hero?
- [ ] Logo wall UNDER hero, not inside?
- [ ] Nav on ONE line desktop, height ≤ 80px?
- [ ] Eyebrow count ≤ ceil(sectionCount / 3)?
- [ ] No 3+ consecutive image+text splits?
- [ ] Section-layout repetition: ≥ 4 different families for 8 sections?
- [ ] No identical card grids?
- [ ] No nested cards?
- [ ] Bento rhythm + exact cell count (no empty cells)?

### Component
- [ ] Button contrast: every CTA WCAG AA 4.5:1?
- [ ] No wrapped CTA text at desktop?
- [ ] No duplicate CTA intent?
- [ ] Form contrast: all inputs pass WCAG AA?
- [ ] Loading/empty/error states provided?

### Visual
- [ ] No div-based fake screenshots?
- [ ] Real images used (gen-tool > Picsum > placeholder slots)?
- [ ] No side-stripe borders?
- [ ] No ghost-card pattern (border + wide shadow on same element)?
- [ ] No `border-radius: 32px+` on cards?
- [ ] No hand-drawn SVG illustrations?
- [ ] No gradient text?

### Motion (Emil Standard)
- [ ] Every animation has a purpose (not "it looked cool")?
- [ ] No animation on keyboard-initiated actions?
- [ ] No `transition: all` used — every transition specifies exact properties?
- [ ] No `ease-in` used on any UI animation?
- [ ] All easing uses Emil's custom cubic-bezier curves?
- [ ] All UI animations ≤ 300ms?
- [ ] Popovers use origin-aware `transform-origin`?
- [ ] No elements animate from `scale(0)`?
- [ ] Framer Motion uses `transform: "translateX()"` for hardware acceleration (not `x`/`y` shorthand)?
- [ ] `backdrop-blur` only on fixed/sticky elements?
- [ ] Reduced motion gated for MOTION_INTENSITY > 3?
- [ ] Touch hover states gated behind `@media (hover: hover) and (pointer: fine)`?
- [ ] No `window.addEventListener('scroll')`?
- [ ] No `requestAnimationFrame` touching React state?
- [ ] Motion claimed = motion shown (no dial claim mismatch)?

### Content
- [ ] Copy self-audit: no AI-hallucinated phrases, no grammatically-broken strings?
- [ ] No em-dashes in copy?
- [ ] No weather/locale strips (unless brief-place)?
- [ ] No scroll cues?
- [ ] No version labels/footers (unless actual launch)?
- [ ] No decorative dots?
- [ ] No section-numbering eyebrows?
- [ ] No "Quietly in use at" copy?

### Output Quality
- [ ] No banned placeholder patterns (`// ...`, `// TODO`, etc.)?
- [ ] Every code block is complete, runnable, unabridged?
- [ ] Does the output feel deliberate, not templated?

**If any checkbox cannot be honestly ticked, the output is not done. Fix before delivering.**

---

## 12. QUICK REFERENCE

### Absolute Bans (match-and-refuse)
Inter as default, em-dashes, pure black/white, Fraunces/Instrument_Serif default, cream/beige body bg, premium-consumer beige+brass palette, AI-purple gradients, gradient text, side-stripe borders, ghost-card pattern, glassmorphism default, 32px+ border-radius on cards, identical card grids, 3-column feature cards, nested cards, div-based fake screenshots, hand-drawn SVG illustrations, eyebrow on every section, numbered section markers, scroll cues, weather strips, version labels on marketing, decorative dots, `repeating-linear-gradient` stripes, `window.addEventListener('scroll')`, animating layout properties (top/left/width/height), placeholder-as-label, custom mouse cursors, `transition: all`, `ease-in` on UI elements.

### Default Stack
React/Next.js (RSC default), Tailwind v4, Motion (`motion/react`), next/font, Phosphor icons.

### Easing Curves (Emil Kowalski Standard)
```css
--ease-out: cubic-bezier(0.23, 1, 0.32, 1);       /* UI interactions */
--ease-in-out: cubic-bezier(0.77, 0, 0.175, 1);    /* on-screen movement */
--ease-drawer: cubic-bezier(0.32, 0.72, 0, 1);     /* iOS-like drawer */
```

### Duration Caps
Button press: 100-160ms | Tooltips/popovers: 125-200ms | Dropdowns/selects: 150-250ms | Modals/drawers: 200-500ms | UI max: 300ms

### Dials Baseline
`DESIGN_VARIANCE: 8` | `MOTION_INTENSITY: 6` | `VISUAL_DENSITY: 4`
