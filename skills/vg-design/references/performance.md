# Performance & Accessibility

## GPU-Safe Animation

See [motion.md](motion.md#performance-rules) for the authoritative animation performance rules in Emil's standard. This file covers non-animation performance and accessibility.

**Key animation performance rules (from motion.md):**
- Animate `transform` and `opacity` by default; `clip-path` is the documented exception in [motion.md](motion.md#performance-rules)
- Framer Motion shorthand (`x`, `y`, `scale`) is NOT hardware-accelerated — use `transform: "translateX()"`
- CSS animations beat JS under load
- `backdrop-blur` only on fixed/sticky elements
- Noise/grain filters only on fixed `pointer-events-none` pseudo-elements

## Core Web Vitals

- **LCP** < 2.5s. Hero image must be `next/image priority` or preloaded.
- **INP** < 200ms. Heavy work off main thread.
- **CLS** < 0.1. Reserve space for images, fonts, embeds.

## Z-Index Restraint

Never spam arbitrary `z-50` or `z-10`. Document the scale:

1. Content (default)
2. Dropdown
3. Sticky elements (nav)
4. Modal backdrop
5. Modal
6. Toast
7. Tooltip

## Accessibility

- `prefers-reduced-motion: reduce` for all motion > MOTION_INTENSITY 3
- `prefers-color-scheme: dark` respected by default
- Touch hover states: `@media (hover: hover) and (pointer: fine)`
- WCAG AA minimum contrast (4.5:1 body, 3:1 large text)
- **Visible focus rings:** Never remove `:focus-visible` outline without a visible replacement (2px offset ring in accent color). Every interactive element must show focus.
- **Tab order:** Logical left-to-right, top-to-bottom. No focus traps except in modals.
- **Modal focus trap:** Focus trapped inside open modal. Close on Escape. Focus returns to trigger element on close.
- **Touch targets:** Minimum 44×44px (WCAG 2.5.5). Check small icons, inline links, close buttons.
- **Form autocomplete:** Set `autocomplete` attributes on inputs (`email`, `tel`, `name`, `address-line1`, `cc-number`, etc.).
- **Landmarks:** Use `<header>`, `<main>`, `<nav>`, `<footer>`. `aria-label` on nav elements when multiple exist.
- **Disabled-state contrast:** Disabled buttons must be visually distinguishable from active buttons (reduced opacity + `cursor-not-allowed`). Sufficient contrast to read the label even when disabled.
- Semantic HTML and ARIA where needed. Buttons are `<button>`, not `<div>` with `onClick`.

## Dependency Verification

**Before importing ANY 3rd-party library, check `package.json`.** If missing, output the install command first. Never assume a library exists. This applies to: Motion, GSAP, Three.js, Phosphor, HugeIcons, Radix icons, Tabler, and all design system packages.

## Resource Loading

- **Fonts:** Always `next/font` (Next.js) or self-host with `@font-face` + `font-display: swap`. Never link Google Fonts via `<link>` in production.
- **Images:** Use `next/image` with explicit width/height to prevent CLS.
- **Motion/GSAP:** Lazy-load GSAP and Three.js — only import in client components that actually use them. Never import at module level if only rare pages need them.
- **Be aware of bundle size.** Motion is not tiny. Three.js is large.
