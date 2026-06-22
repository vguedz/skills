# Performance & Accessibility

## GPU-Safe Animation

- Animate ONLY `transform` and `opacity`. These skip layout/paint, running on GPU.
- `will-change: transform` sparingly — only on elements that will actually animate.
- `backdrop-blur` only on fixed/sticky elements. Never on scrolling containers — continuous GPU repaints destroy mobile FPS.
- Noise/grain filters exclusively on fixed `pointer-events-none` pseudo-elements (e.g., `fixed inset-0 z-[60] pointer-events-none`). Never on scrolling containers.
- Be aware of bundle size. Motion is not tiny. Three.js is large. Lazy-load anything not above the fold.

## Core Web Vitals

- **LCP** < 2.5s. Hero image must be `next/image priority` or preloaded.
- **INP** < 200ms. Heavy work off main thread.
- **CLS** < 0.1. Reserve space for images, fonts, embeds.
- Run Lighthouse before declaring a page done.

## Z-Index Restraint

Never spam arbitrary `z-50` or `z-10`. Use z-index strictly for systemic layer contexts:
1. Dropdown
2. Sticky elements
3. Modal backdrop
4. Modal
5. Toast
6. Tooltip

Document the z-index scale in a project constants file.

## Accessibility

- `prefers-reduced-motion: reduce` for all motion > MOTION_INTENSITY 3. Keep opacity/color transitions that aid comprehension. Remove movement/position animations.
- `prefers-color-scheme: dark` respected by default.
- Touch hover states: `@media (hover: hover) and (pointer: fine)`.
- WCAG AA minimum contrast (4.5:1 body, 3:1 large text). AAA for hero copy.
- Semantic HTML and ARIA where needed.
- Keyboard navigation for all interactive elements.

## Dependency Verification

Before importing ANY 3rd-party library, check `package.json`. If the package is missing, output the install command first. Never assume a library exists.

## Resource Loading

- Fonts: Always use `next/font` (Next.js) or self-host with `@font-face` + `font-display: swap`. Never link Google Fonts via `<link>` in production.
- Images: Use `next/image` with explicit width/height to prevent CLS.
- Motion: Lazy-load GSAP and Three.js — only import in client components that actually use them.
