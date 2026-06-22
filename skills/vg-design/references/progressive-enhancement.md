# Progressive Enhancement & Degradation

Every feature must work without its enhancement. Animation, blur, glass, and scroll effects degrade gracefully.

## Baseline Rule

Design the static, JS-disabled, reduced-motion, no-blur, no-glass, mobile-first experience first. Then layer enhancements. The baseline must be complete and usable.

## Reduced Motion

Every animation with `MOTION_INTENSITY > 3` must have a `prefers-reduced-motion: reduce` fallback.

**In CSS:**
```css
/* Active animation */
.element {
  transition: transform 350ms cubic-bezier(0.23, 1, 0.32, 1);
}
/* Instant fallback */
@media (prefers-reduced-motion: reduce) {
  .element {
    transition: none;
    animation: none;
  }
}
```

**In Motion (React):**
```tsx
import { motion, useReducedMotion } from "motion/react";

function AnimatedComponent() {
  const reduce = useReducedMotion();
  return (
    <motion.div
      initial={reduce ? false : { opacity: 0, transform: "translateY(24px)" }}
      animate={{ opacity: 1, transform: "translateY(0px)" }}
      transition={reduce ? { duration: 0 } : { duration: 0.3, ease: [0.23, 1, 0.32, 1] }}
    />
  );
}
```

**What to keep:** Opacity transitions and color changes that aid comprehension. Simple `opacity` fades are non-disorienting.

**What to collapse:** Translational movement, scale changes, rotations, parallax, immersive scroll sections (sticky-stack, horizontal pan), infinite loops, magnetic physics.

## Touch Device Hover States

Touch devices trigger `:hover` on tap, causing false-positive activation. Gate all hover effects:

```css
@media (hover: hover) and (pointer: fine) {
  .element:hover {
    transform: scale(1.05);
  }
}
```

For Tailwind, use the `hover:` variant as normal, but wrap mobile-unsafe hover logic in a media query or use `useMediaQuery('(hover: hover)')` in components.

## @starting-style Fallback

`@starting-style` (for element entry without JS) is supported in Chrome 117+, Firefox 129+, Safari 17.5+.

**Fallback pattern for broader support:**
```jsx
// React entry animation without @starting-style
function AnimatedEntry({ children }) {
  const [mounted, setMounted] = useState(false);
  useEffect(() => { setMounted(true); }, []);

  return (
    <div
      style={{
        opacity: mounted ? 1 : 0,
        transform: mounted ? 'translateY(0)' : 'translateY(8px)',
        transition: 'opacity 300ms cubic-bezier(0.23, 1, 0.32, 1), transform 300ms cubic-bezier(0.23, 1, 0.32, 1)',
      }}
    >
      {children}
    </div>
  );
}
```

Use `@starting-style` directly in CSS when you control the markup and browser targets are modern. Fall back to the `data-mounted` / `useEffect` pattern for older targets.

## Backdrop-Filter & Blur

`backdrop-filter` degrades gracefully in most modern browsers, but `prefers-reduced-transparency` (limited support) should gate glass effects.

```css
.glass-panel {
  background: oklch(0.98 0.005 250 / 0.12);
  backdrop-filter: blur(24px);
}

@media (prefers-reduced-transparency: reduce) {
  .glass-panel {
    background: oklch(0.98 0.005 250 / 0.92);
    backdrop-filter: none;
  }
}
```

Always provide a solid-fill fallback in case `backdrop-filter` is unsupported. Use `@supports (backdrop-filter: blur(1px))` to gate blur-dependent layouts.

## JS-Disabled Baseline

Essential content and navigation must work without JavaScript.

- **Navigation:** Use `<details>`/`<summary>` or CSS-only menus as fallback. If JS enhances a hamburger, the mobile nav must still be reachable via visible links.
- **Forms:** Standard `<form>` submission works without JS. Enhance with `fetch`/`useActionState` on top.
- **Content visibility:** Never gate content on a class-triggered transition. Transitions pause on hidden tabs and headless renderers, so the reveal never fires and the section ships blank. Always render content in the default state; use `opacity: 0` + `transition` to animate it in AFTER mount.

## Responsive Collapse

Every multi-column layout must declare its < 768px fallback explicitly in the same component.

```tsx
<section className="grid grid-cols-1 md:grid-cols-2 gap-6 md:gap-12">
```

Asymmetric layouts (VARIANCE > 4) MUST collapse to single-column with `w-full`, `px-4`, `py-8` on mobile. Overlapping elements and negative margins reset to standard spacing.

## Image & Font Loading

- **Images:** Always `next/image` with explicit width/height to prevent CLS. `priority` on hero images.
- **Fonts:** `next/font` or `@font-face` with `font-display: swap`. Never external `<link>` to Google Fonts.
- **Animation libraries:** Lazy-load GSAP and Three.js in the client components that use them. Never import at the module level if only rare pages need them.
