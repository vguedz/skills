# Motion & Animation — Emil Kowalski Standard

This is the definitive animation authority. Emil's philosophy governs all motion, easing, and interaction decisions.

## Core Philosophy

**Taste is trained, not innate.** Study why the best interfaces feel the way they do. Reverse engineer animations. Inspect interactions. Be curious.

**Unseen details compound.** Most details users never consciously notice — that is the point. The aggregate of invisible correctness creates interfaces people love without knowing why.

**Beauty is leverage.** People select tools based on the overall experience, not just functionality.

## The Animation Decision Framework

Before any animation code, answer these questions in order:

### 1. Should this animate at all?

| Frequency | Decision |
|---|---|
| 100+ times/day (keyboard shortcuts, command palette) | No animation. Ever. |
| Tens of times/day (hover effects, list navigation) | Remove or drastically reduce |
| Occasional (modals, drawers, toasts) | Standard animation |
| Rare/first-time (onboarding, celebrations) | Can add delight |

**Never animate keyboard-initiated actions.** Raycast has no open/close animation — that is the optimal experience.

### 2. What is the purpose?

Valid: spatial consistency, state indication, explanation, feedback, preventing jarring changes. Invalid: "it looks cool" (for frequent interactions).

### 3. What easing?

```
Element entering or exiting?
  Yes → custom ease-out curve (starts fast, feels responsive)
  No →
    Moving/morphing on screen?
      Yes → custom ease-in-out curve
    Hover/color change?
      Yes → ease (for deliberate color-only micro-transitions < 200ms)
    Constant motion (marquee, progress bar)?
      Yes → linear
    Default → custom ease-out curve
```

## Easing Curves (Emil Standard)

**Never use `ease-in` for UI.** It starts slow, making the interface feel sluggish. A dropdown with `ease-in` at 300ms feels slower than `ease-out` at the same 300ms.

**Never use `transition: all`.** Specify exact properties.

```css
--ease-out: cubic-bezier(0.23, 1, 0.32, 1);       /* directional UI movement, popovers, toasts, tooltips */
--ease-in-out: cubic-bezier(0.77, 0, 0.175, 1);    /* on-screen movement, morphing, cards moving */
--ease-drawer: cubic-bezier(0.32, 0.72, 0, 1);     /* iOS-like drawer, drag-to-dismiss (from Ionic) */
```

**When to use which:**
- `--ease-out`: Any UI element entering/exiting (toasts, popovers, tooltips, dropdowns, button feedback)
- `--ease-in-out`: Elements moving on screen without appearing/disappearing (card reorder, layout shift, morph)
- `--ease-drawer`: Drawers, bottom sheets, drag-to-dismiss, swipe interactions
- `linear`: Constant/scrubbed motion (marquees, scroll-driven animations, hold-to-delete press phase)
- CSS `ease`: Only for deliberate color-only micro-transitions under 200ms (hover bg color change). Not for directional movement.

**Easing curve resources:** [easing.dev](https://easing.dev/) or [easings.co](https://easings.co/)

## Duration Reference

| Element | Duration |
|---|---|
| Button press feedback | 100-160ms |
| Tooltips, small popovers | 125-200ms |
| Dropdowns, selects | 150-250ms |
| Modals, drawers | 200-300ms for UI; up to 500ms only for large gesture-driven sheets |
| Marketing/explanatory | Can be longer |

**Rule: UI animations stay under 300ms.**

## Perceived Performance

Speed in animation directly affects perceived performance:
- A fast-spinning spinner makes loading feel faster (same load time, different perception)
- A 180ms select feels more responsive than 400ms
- Instant tooltips after the first one (skip delay + skip animation) make the whole toolbar feel faster
- `ease-out` at 200ms feels faster than `ease-in` at 200ms because the user sees immediate movement

## Spring Animations

Springs simulate real physics. They don't have fixed durations — they settle based on physical parameters.

**When to use:** drag interactions with momentum, Apple Dynamic Island, interruptible gestures, decorative mouse tracking.

```js
// Apple's approach (recommended)
{ type: "spring", duration: 0.5, bounce: 0.2 }

// Traditional physics
{ type: "spring", mass: 1, stiffness: 100, damping: 10 }
```

Keep bounce 0.1-0.3. Avoid bounce in most UI contexts.

**Spring-based mouse interactions:** Use `useSpring` from Motion. Never `useState` for continuous values.

## CSS Transform Mastery

### translateY with percentages
Percentage values are relative to the element's own size. Prefer percentages over hardcoded pixels.

```css
.drawer-hidden { transform: translateY(100%); }
.toast-enter { transform: translateY(-100%); }
```

### scale() scales children too
Unlike `width`/`height`, `scale()` scales children proportionally. This is a feature.

### transform-origin
Default is center. Set to match the trigger for origin-aware interactions.

## clip-path for Animation

`clip-path: inset(top right bottom left)` — each value eats into the element from that side.

```css
.overlay {
  clip-path: inset(0 100% 0 0);
  transition: clip-path 200ms cubic-bezier(0.23, 1, 0.32, 1);
}
.button:active .overlay {
  clip-path: inset(0 0 0 0);
  transition: clip-path 2s linear;
}
```

**Tabs with perfect color transitions:** Duplicate tab list. Style copy as "active". Clip it so only the active tab is visible. Animate clip on tab change.

**Hold-to-delete:** `inset(0 100% 0 0)` → `inset(0 0 0 0)` over 2s linear on `:active`. Snap back 200ms ease-out. Add `scale(0.97)` for press feedback.

**Image reveals on scroll:** Start `inset(0 0 100% 0)`. Animate to `inset(0 0 0 0)` when entering viewport.

**Comparison sliders:** Overlay two images. Clip top with `inset(0 X% 0 0)`. Fully hardware-accelerated.

## Component Animation Rules

### Buttons must feel responsive
```css
.button { transition: transform 160ms cubic-bezier(0.23, 1, 0.32, 1); }
.button:active { transform: scale(0.97); }
```

**Scale-on-active gate:** This rule applies to standalone CTAs, landing-page buttons, and premium marketing interactions. It does NOT apply to:
- Keyboard-driven tools (command palette, terminal, search) — any interaction used 100+ times/day
- Repeated actions (> 10 times per session: table rows, checkboxes, filter chips, nav items, pagination)
- Dense product UI (DENSITY ≥ 7: dashboard cards, data tables, form fields)
- Segmented controls, toggle groups, tab bars (use background change or 1px translate instead)

For excluded elements, use a background change or 1px translate for feedback.

### Never animate from scale(0)
```css
/* Bad */
.entering { transform: scale(0); }
/* Good */
.entering { transform: scale(0.95); opacity: 0; }
```

### Make popovers origin-aware
```css
.popover { transform-origin: var(--radix-popover-content-transform-origin); }
```
Modals keep `transform-origin: center`.

### Tooltips: skip delay on subsequent hovers
```css
.tooltip {
  transition: transform 125ms cubic-bezier(0.23, 1, 0.32, 1),
              opacity 125ms cubic-bezier(0.23, 1, 0.32, 1);
  transform-origin: var(--transform-origin);
}
.tooltip[data-starting-style], .tooltip[data-ending-style] { opacity: 0; transform: scale(0.97); }
.tooltip[data-instant] { transition-duration: 0ms; }
```

### Use CSS transitions over keyframes for interruptible UI
Transitions can be interrupted and retargeted. Keyframes restart from zero. For toasts, toggles, rapidly-triggered elements — use transitions.

### Use blur to mask imperfect crossfades
```css
.button-content { transition: filter 200ms cubic-bezier(0.23, 1, 0.32, 1), opacity 200ms cubic-bezier(0.23, 1, 0.32, 1); }
.button-content.transitioning { filter: blur(2px); opacity: 0.7; }
```
Keep blur under 20px.

### Animate enter states with @starting-style
```css
.toast {
  opacity: 1; transform: translateY(0);
  transition: opacity 200ms cubic-bezier(0.23, 1, 0.32, 1),
              transform 200ms cubic-bezier(0.23, 1, 0.32, 1);
  @starting-style { opacity: 0; transform: translateY(100%); }
}
```
Browser: Chrome 117+, FF 129+, Safari 17.5+. Fallback: `data-mounted` / `useEffect` pattern.

## Asymmetric Enter/Exit Timing

Slow where user decides, fast where system responds:
```css
.overlay { transition: clip-path 200ms cubic-bezier(0.23, 1, 0.32, 1); }
.button:active .overlay { transition: clip-path 2s linear; }
```

## Stagger Animations

30-80ms between items. Decorative — never block interaction.

```css
.item { opacity: 0; transform: translateY(8px); animation: fadeIn 300ms cubic-bezier(0.23, 1, 0.32, 1) forwards; }
.item:nth-child(1) { animation-delay: 0ms; }
.item:nth-child(2) { animation-delay: 50ms; }
.item:nth-child(3) { animation-delay: 100ms; }
@keyframes fadeIn { to { opacity: 1; transform: translateY(0); } }
```

## Gesture & Drag

- **Momentum-based dismissal:** `Math.abs(dragDistance) / elapsedTime`. If velocity > 0.11, dismiss.
- **Damping at boundaries:** More drag past boundary = less movement.
- **Pointer capture:** Capture all pointer events once drag starts.
- **Multi-touch protection:** Ignore additional touch points after initial drag.

## Performance Rules

- **Animate ONLY `transform` and `opacity`.** GPU-accelerated, skip layout/paint. `clip-path` is also GPU-accelerated — it's the exception to this rule and animates on the compositor without triggering layout or paint. `filter: blur()` is GPU-accelerated but expensive at high values on Safari; keep blur under 6px.
- **CSS variables are inheritable.** Updating parent variable recalculates all children. Update `transform` directly instead.
- **Framer Motion shorthand (`x`, `y`, `scale`) is NOT hardware-accelerated.** Use `transform: "translateX(100px)"`.
- **CSS animations beat JS under load.** They run off the main thread.
- **WAAPI:** JavaScript control with CSS performance:
  ```js
  element.animate(
    [{ clipPath: 'inset(0 0 100% 0)' }, { clipPath: 'inset(0 0 0 0)' }],
    { duration: 1000, fill: 'forwards', easing: 'cubic-bezier(0.77, 0, 0.175, 1)' }
  );
  ```

## Motion Library Choice

- **Motion (`motion/react`)** — default for UI/state-change motion, bento, hover, stagger reveals
- **GSAP + ScrollTrigger** — for full-page scrolltelling, sticky-stack, horizontal pan
- **CSS scroll-driven animations** — lighter alternative to GSAP for simple reveals
- **WAAPI** — programmatic CSS animations with JS control
- **Never mix GSAP/Three.js with Motion in the same component tree**

## Concrete Component Rules (from Sonner Principles)

### Minimal API
A component's public API should be zero-config for the default case:
```tsx
// Ideal: single import, single call, works immediately
toast("Saved successfully");

// Acceptable: one-time setup
<Toaster /> // in root, once
toast("Saved successfully"); // call from anywhere
```

### Default Behavior (not just visual)
- **Timers pause when tab is hidden** (`visibilitychange` event). Resume on return.
- **Stacking:** New items push existing items up. Fill gaps between stacked elements so hover state is continuous.
- **Keyboard behavior:** Escape dismisses the topmost item. Focus trapped inside interactive items.
- **Transition strategy:** Use CSS transitions (interruptible), not keyframes. Items are added rapidly.

### Escape Hatches
Every opinionated default needs an opt-out, but opt-outs should be explicit:
```tsx
toast("Processing...", { duration: Infinity }); // don't auto-dismiss
toast.error("Failed", { dismissible: false });  // must be programmatically dismissed
```
Never expose timing internals as public API unless the user asks.

### Cohesion
The easing, duration, and visual style must fit the component's personality. A playful toast can be bouncier. A professional dashboard toast should be crisp and fast. Match the motion to the mood.

## GSAP Canonical Skeletons

**Sticky-Stack:** `start: "top top"`, `pin: true`, `pinSpacing: false`. Every card except last pinned. Scale/opacity driven by next card's trigger.

**Horizontal-Pan:** `start: "top top"`, `pin: true` on wrapper, `scrub: 1`, `end: () => `+=${distance}`. Wrapper pinned, inner track slides horizontally.

## Debugging Animations

- **Slow motion:** Play at 2-5x speed. Check easing, sync, transform-origin, overlapping states.
- **Frame-by-frame:** Chrome DevTools Animations panel.
- **Real devices:** Test touch interactions on physical hardware.
- **Review next day:** Fresh eyes catch what development focus misses.

## Forbidden Animation Patterns

- `window.addEventListener("scroll", ...)` — banned. Use `useScroll()`, `ScrollTrigger`, `IntersectionObserver`, CSS `animation-timeline: view()`.
- Custom `window.scrollY` in React state.
- `requestAnimationFrame` touching React state — use motion values.
- `transition: all` — specify exact properties.
- `ease-in` on UI elements.
- Keyframes on rapidly-triggered elements — use CSS transitions.
- Animating layout properties (`top`, `left`, `width`, `height`).
