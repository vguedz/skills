# Visual QA — Implementation Verification

Code on screen is not verified. Verify with actual browser rendering before declaring work done.

## Required Verification Steps

### 1. Viewport Screenshot Check

Check at minimum these widths. Take screenshots or render the page in each:

| Width | Device | What to check |
|---|---|---|
| 375px | iPhone SE / small Android | Single column, no horizontal overflow, tap targets ≥ 44px, nav collapsed |
| 768px | iPad portrait / small laptop | Layout transition point, no broken grids, readable text |
| 1024px | iPad landscape / small desktop | Nav full width check, section spacing appropriate |
| 1440px | Standard desktop | Full layout, max-width containers centered, no excessive whitespace edges |
| 1920px | Large desktop (optional) | `max-w-[1400px]` or similar container working, no content stretching |

### 2. Overflow Check

At every viewport, check:
- [ ] No horizontal scrollbar (unless intentional horizontal pan).
- [ ] No text truncation without ellipsis (headlines, buttons, nav items).
- [ ] No image overflow beyond container.
- [ ] No card overflow from grid cells.
- [ ] Long words (German compound nouns, URLs, email addresses) don't break layout. Use `break-words` or `overflow-wrap: anywhere` on user-generated content.
- [ ] Test with realistic long data: full names ("Alexandra Cunningham-Smith"), long emails, 6+ digit numbers, currencies with symbols, dates in different formats.

### 3. Interaction Verification

For each interactive element:
- [ ] **Hover states** work and are visible (cursor change, background shift, scale).
- [ ] **Focus states** visible on keyboard Tab navigation. Never remove `:focus-visible` outline without replacement.
- [ ] **Active/press states** provide feedback within 100-160ms.
- [ ] **Disabled states** are visually distinct and have `cursor-not-allowed`.
- [ ] **Loading states** replace content without layout shift.
- [ ] **Error states** show specific, actionable messages.

### 4. Contrast & Accessibility Audit

- [ ] Body text ≥ 4.5:1 contrast against background. Verify with DevTools or a contrast checker.
- [ ] Large text (≥18px or bold ≥14px) ≥ 3:1.
- [ ] Placeholder text ≥ 4.5:1 against input background.
- [ ] Focus ring visible on all interactive elements.
- [ ] Tab order logical (left to right, top to bottom). No focus traps except in modals.
- [ ] Modal focus trapped inside modal. Close on Escape. Focus returns to trigger on close.
- [ ] Touch targets ≥ 44×44px (WCAG 2.5.5). Check small icons, inline links, close buttons.
- [ ] Form inputs have associated `<label>` elements. Autocomplete attributes set where applicable (`autocomplete="email"`, `autocomplete="tel"`).
- [ ] Landmarks present: `<header>`, `<main>`, `<nav>`, `<footer>`. `aria-label` on nav elements when multiple exist.
- [ ] Disabled buttons have sufficient contrast to be distinguishable from active buttons (WCAG requires no contrast minimum for disabled, but usability requires visual distinction).

### 5. Motion Verification

- [ ] `prefers-reduced-motion: reduce` removes all translational/scale animation. Opacity/color transitions preserved.
- [ ] Touch hover states gated behind `@media (hover: hover) and (pointer: fine)`. No sticky hover on mobile.
- [ ] No `window.addEventListener('scroll')` in the codebase (grep for it).
- [ ] Scroll-driven animations use `ScrollTrigger`, `useScroll()`, or CSS `animation-timeline`. Never `requestAnimationFrame` + `scrollY` in React state.
- [ ] Clip-path and transform animations are smooth (check DevTools Performance panel for dropped frames).

### 6. Cross-Browser Check

At minimum, verify in:
- **Chrome:** Primary development target. Check DevTools for warnings.
- **Firefox:** Check backdrop-filter support. Some glass effects may degrade.
- **Safari:** Critical for iOS users. Check `@starting-style` support (Safari 17.5+), `min-h-[100dvh]` viewport behavior, sticky positioning.

### 7. Content Validation

- [ ] No lorem ipsum in any visible text, alt text, or placeholder.
- [ ] No "John Doe", "Acme Corp", "123 Main St", "test@example.com" in any visible content.
- [ ] All numbers in charts/tables/metrics either (a) come from real data, (b) are labeled `<!-- mock -->`.
- [ ] Real screenshots are redacted: no secrets, emails, tokens, customer data, internal URLs, or production identifiers.
- [ ] All images have `alt` text. Decorative images have `alt=""`.
- [ ] No em-dashes (`—`) in any visible text.

## Final Evidence Report

After verification, report the following before declaring work done:

```
## Verification Report

### Viewports checked
- [ ] 375px (mobile)
- [ ] 768px (tablet)
- [ ] 1024px (small desktop)
- [ ] 1440px (desktop)

### Screenshots
Screenshots captured at [widths]. Available at: [paths or "viewable in browser at localhost:3000"]

### Accessibility
- Contrast: [passed / issues found: list]
- Keyboard nav: [passed / issues found: list]
- Touch targets: [passed / issues found: list]
- Focus states: [passed / issues found: list]
- Landmarks: [passed / issues found: list]

### Motion
- Reduced motion verified: [yes / no]
- Scroll performance: [smooth / issues found]
- No banned scroll patterns: [yes / issues found]

### Content
- No placeholder text: [yes / issues found]
- Real or labeled-mock data: [yes / issues found]
- Screenshot redaction: [passed / issues found]
- Image alt text: [complete / missing for: list]

### Unresolved risks
- [Any known issues not yet fixed]
- [Browser-specific concerns]
- [Performance concerns for low-end devices]
```

If verification requires access to tools the agent does not have (browser screenshotting, live rendering), state explicitly what could not be verified and what the user should check manually.
