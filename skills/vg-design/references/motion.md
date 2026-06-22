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
  Yes → ease-out (starts fast, feels responsive)
  No →
    Moving/morphing on screen?
      Yes → ease-in-out
    Hover/color change?
      Yes → ease
    Constant motion (marquee, progress bar)?
      Yes → linear
    Default → ease-out
```

## Easing Curves (Emil Standard)

**Never use ease-in for UI.** It starts slow, making the interface feel sluggish. A dropdown with `ease-in` at 300ms feels slower than `ease-out` at the same 300ms.

**Never use `transition: all`.** Specify exact properties.

**Never use built-in CSS easings.** They lack the punch that makes animations feel intentional. Use custom cubic-bezier curves.

```css
--ease-out: cubic-bezier(0.23, 1, 0.32, 1);       /* UI interactions: buttons, toasts, popovers, tooltips */
--ease-in-out: cubic-bezier(0.77, 0, 0.175, 1);    /* on-screen movement, morphing, cards moving */
--ease-drawer: cubic-bezier(0.32, 0.72, 0, 1);     /* iOS-like drawer, drag-to-dismiss (from Ionic) */
```

**Easing curve resources:** Use [easing.dev](https://easing.dev/) or [easings.co](https://easings.co/) to find stronger variants.

## Duration Reference

| Element | Duration |
|---|---|
| Button press feedback | 100-160ms |
| Tooltips, small popovers | 125-200ms |
| Dropdowns, selects | 150-250ms |
| Modals, drawers | 200-500ms |
| Marketing/explanatory | Can be longer |

**Rule: UI animations stay under 300ms.** A 180ms dropdown feels more responsive than a 400ms one. A faster-spinning spinner makes loading feel faster (same load time, different perception).

## Perceived Performance

Speed in animation directly affects perceived performance:
- A fast-spinning spinner makes loading feel faster
- A 180ms select feels more responsive than 400ms
- Instant tooltips after the first one (skip delay + skip animation) make the whole toolbar feel faster

`ease-out` at 200ms feels faster than `ease-in` at 200ms because the user sees immediate movement.

## Spring Animations

Springs simulate real physics. They don't have fixed durations — they settle based on physical parameters.

**When to use:** drag interactions with momentum, Apple Dynamic Island, interruptible gestures, decorative mouse tracking.

**Spring-based mouse interactions:** Use `useSpring` from Motion to interpolate value changes. Never `useState` for continuous input values (mouse, scroll, physics).

```js
// Apple's approach (recommended)
{ type: "spring", duration: 0.5, bounce: 0.2 }

// Traditional physics
{ type: "spring", mass: 1, stiffness: 100, damping: 10 }
```

Keep bounce 0.1-0.3. Avoid bounce in most UI contexts. Use for drag-to-dismiss and playful interactions.

**Interruptibility:** Springs maintain velocity when interrupted — CSS animations and keyframes restart from zero. Ideal for gestures users might change mid-motion.

## CSS Transform Mastery

### translateY with percentages
Percentage values are relative to the element's own size. Prefer percentages over hardcoded pixels — they adapt to content.

```css
.drawer-hidden { transform: translateY(100%); } /* works regardless of drawer height */
.toast-enter { transform: translateY(-100%); }   /* works regardless of toast height */
```

### scale() scales children too
Unlike `width`/`height`, `scale()` scales an element's children proportionally (font, icons, content). This is a feature.

### 3D transforms for depth
```css
.wrapper { transform-style: preserve-3d; }
@keyframes orbit {
  from { transform: translate(-50%,-50%) rotateY(0deg) translateZ(72px) rotateY(360deg); }
  to   { transform: translate(-50%,-50%) rotateY(360deg) translateZ(72px) rotateY(0deg); }
}
```

### transform-origin
Default is center. Set to match the trigger for origin-aware interactions.

## clip-path for Animation

`clip-path` is not just for shapes — it is one of the most powerful animation tools in CSS.

### The inset shape
`clip-path: inset(top right bottom left)` — each value "eats" into the element from that side.

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

### Tabs with perfect color transitions
Duplicate the tab list. Style the copy as "active". Clip it so only the active tab is visible. Animate the clip on tab change. This creates seamless color transitions that timing individual color transitions can never achieve.

### Hold-to-delete
`clip-path: inset(0 100% 0 0)` on overlay. On `:active`, transition to `inset(0 0 0 0)` over 2s linear. On release, snap back 200ms ease-out. Add `scale(0.97)` for press feedback.

### Image reveals on scroll
Start `clip-path: inset(0 0 100% 0)`. Animate to `inset(0 0 0 0)` when entering viewport.

### Comparison sliders
Overlay two images. Clip top one with `inset(0 50% 0 0)`. Adjust right inset based on drag. Fully hardware-accelerated.

## Component Animation Rules

### Buttons must feel responsive
```css
.button { transition: transform 160ms cubic-bezier(0.23, 1, 0.32, 1); }
.button:active { transform: scale(0.97); }
```
Apply to every pressable element. Scale should be 0.95-0.98.

### Never animate from scale(0)
Nothing in the real world appears from nothing. Start from `scale(0.95)` combined with `opacity: 0`.

```css
/* Bad */
.entering { transform: scale(0); }
/* Good */
.entering { transform: scale(0.95); opacity: 0; }
```

### Make popovers origin-aware
Popovers scale from their trigger, not from center. Modals keep `transform-origin: center`.

```css
.popover { transform-origin: var(--radix-popover-content-transform-origin); }
```

### Tooltips: skip delay on subsequent hovers
Delay before appearing to prevent accidental activation. But once one tooltip is open, adjacent tooltips open instantly.

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
Transitions can be interrupted and retargeted mid-animation. Keyframes restart from zero. For rapidly-triggered elements (toasts, toggles), use transitions.

### Use blur to mask imperfect crossfades
When a crossfade between two states feels off, add `filter: blur(2px)` during the transition. Blur bridges the visual gap. Keep under 20px — expensive in Safari.

```css
.button-content { transition: filter 200ms cubic-bezier(0.23, 1, 0.32, 1), opacity 200ms ease; }
.button-content.transitioning { filter: blur(2px); opacity: 0.7; }
```

### Animate enter states with @starting-style
```css
.toast {
  opacity: 1; transform: translateY(0);
  transition: opacity 400ms cubic-bezier(0.23, 1, 0.32, 1),
              transform 400ms cubic-bezier(0.23, 1, 0.32, 1);
  @starting-style { opacity: 0; transform: translateY(100%); }
}
```
Browser support: Chrome 117+, Firefox 129+, Safari 17.5+. Fall back to `data-mounted` attribute pattern for older browsers.

## Asymmetric Enter/Exit Timing

Slow where the user is deciding, fast where the system responds:

```css
.overlay { transition: clip-path 200ms cubic-bezier(0.23, 1, 0.32, 1); }  /* release: fast */
.button:active .overlay { transition: clip-path 2s linear; }              /* press: slow */
```

## Stagger Animations

Keep stagger delays short (30-80ms between items). Long delays feel slow. Decorative — never block interaction.

```css
.item { opacity: 0; transform: translateY(8px); animation: fadeIn 300ms cubic-bezier(0.23, 1, 0.32, 1) forwards; }
.item:nth-child(1) { animation-delay: 0ms; }
.item:nth-child(2) { animation-delay: 50ms; }
.item:nth-child(3) { animation-delay: 100ms; }
.item:nth-child(4) { animation-delay: 150ms; }
@keyframes fadeIn { to { opacity: 1; transform: translateY(0); } }
```

## Gesture & Drag

- **Momentum-based dismissal:** Calculate `Math.abs(dragDistance) / elapsedTime`. If velocity > 0.11, dismiss regardless of distance.
- **Damping at boundaries:** The more the user drags past the natural boundary, the less the element moves.
- **Pointer capture:** Once dragging starts, set the element to capture all pointer events.
- **Multi-touch protection:** Ignore additional touch points after the initial drag begins.

## Performance Rules

- **Animate ONLY `transform` and `opacity`.** These skip layout/paint, running on GPU.
- **CSS variables are inheritable.** Updating `--swipe-amount` on a container causes expensive recalculation on all children. Update `transform` directly instead.
- **Framer Motion shorthand (`x`, `y`, `scale`) is NOT hardware-accelerated.** It uses `requestAnimationFrame` on the main thread. Use `transform: "translateX(100px)"` for hardware acceleration.
- **CSS animations beat JS under load.** CSS runs off the main thread. Use CSS for predetermined animations, JS for dynamic/interruptible ones.
- **Use WAAPI for programmatic CSS animations.** JavaScript control with CSS performance:
  ```js
  element.animate(
    [{ clipPath: 'inset(0 0 100% 0)' }, { clipPath: 'inset(0 0 0 0)' }],
    { duration: 1000, fill: 'forwards', easing: 'cubic-bezier(0.77, 0, 0.175, 1)' }
  );
  ```

## Reduced Motion (Mandatory)

Keep opacity and color transitions that aid comprehension. Remove movement and position animations.

```css
@media (prefers-reduced-motion: reduce) {
  .element { animation: fade 0.2s ease; /* No transform-based motion */ }
}
```

In Motion: `const shouldReduceMotion = useReducedMotion();` Degrade to static/instant.

**Touch device hover states:** Gate behind `@media (hover: hover) and (pointer: fine)` — touch devices trigger hover on tap.

## Motion Library Choice

- **Motion (`motion/react`)** — default for UI/state-change motion, bento, hover, stagger reveals
- **GSAP + ScrollTrigger** — for full-page scrolltelling, sticky-stack, horizontal pan
- **CSS scroll-driven animations** — lighter alternative to GSAP for simple reveals
- **WAAPI** — programmatic CSS animations with JS control
- **Never mix GSAP/Three.js with Motion in the same component tree**

## GSAP Canonical Skeletons

**Sticky-Stack:** `start: "top top"`, `pin: true`, every card except last pinned. `pinSpacing: false`. Scale/opacity driven by next card's trigger.

**Horizontal-Pan:** `start: "top top"`, `pin: true` on wrapper, `scrub: 1`, `end: () => `+=${distance}`. Wrapper pinned, inner track slides horizontally.

## The Sonner Principles (Building Loved Components)

1. **Developer experience is key.** No hooks, no context, no complex setup. Insert once, call from anywhere.
2. **Good defaults matter more than options.** Ship beautiful out of the box. Most users never customize.
3. **Handle edge cases invisibly.** Pause timers when tab is hidden. Users never notice these.
4. **Use transitions, not keyframes, for dynamic UI.** Rapidly-added items. Keyframes restart from zero.
5. **Cohesion matters.** Match the motion to the mood. Playful = bouncier. Professional = crisp and fast.

## Debugging Animations

- **Slow motion testing:** Play at 2-5x normal speed. Temporarily increase durations.
- **Frame-by-frame:** Chrome DevTools Animations panel. Check easing, sync between properties.
- **Real devices:** Test touch interactions on physical devices, not simulators.
- **Review next day:** You notice imperfections the next day that you missed during development.

## Forbidden Animation Patterns

- `window.addEventListener("scroll", ...)` — banned. Use `useScroll()`, `ScrollTrigger`, `IntersectionObserver`, CSS `animation-timeline: view()`.
- Custom `window.scrollY` in React state — re-renders on every frame.
- `requestAnimationFrame` touching React state — use motion values.
- `transition: all` — always specify exact properties.
- `ease-in` on UI elements.
- Keyframes on rapidly-triggered elements — use CSS transitions.
- Animating layout properties (`top`, `left`, `width`, `height`).
