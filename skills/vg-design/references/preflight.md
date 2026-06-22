# Pre-Flight Checklist

## Critical Path (Run Every Time)

If any critical gate fails, the output is broken.

- [ ] **1. Design Read declared.** Dials set and reasoned from brief.
- [ ] **2. No undocumented Tier-0 AI tells.** No em-dashes, no Inter default, no cream/beige bg, no fake screenshots, no `transition: all`, unless mandated by project/brand/design-system constraints and documented.
- [ ] **3. Value prop clear in first viewport.** User understands what this is and why they should care without scrolling.
- [ ] **4. CTA intent obvious and unduplicated.** One label per intent. CTA fits on one line at desktop. Readable against background (WCAG AA 4.5:1).
- [ ] **5. Hero fits viewport.** Headline ≤ 2 lines. Subtext ≤ 20 words. CTA visible. No trust strip inside hero. No version labels.
- [ ] **5b. Responsive text check.** Test with realistic data: long names ("Alexandra Cunningham-Smith"), 6+ digit numbers, currencies, dates. No button/card/title overflow at any viewport. Long words use `break-words`.
- [ ] **6. Content hierarchy matches business priority.** Most important thing is most prominent. Not everything can be "primary."
- [ ] **7. Navigation path clear.** User knows where they are and what to do next. Nav on one line desktop, height ≤ 80px.
- [ ] **8. Emil's easing standard applied.** No `transition: all`. No `ease-in` on UI. Popovers origin-aware. No `scale(0)` entrances. Scale-on-active used only where motion/product UI gates allow it. UI animations ≤ 300ms.
- [ ] **9. One accent color locked.** One page theme locked. One corner-radius system applied.
- [ ] **10. Reduced motion gated.** Touch hover gated. `min-h-[100dvh]`, never `h-screen`.
- [ ] **11. Loading, empty, error states provided** for any interactive surface.
- [ ] **12. No placeholder patterns.** Every code block complete, runnable, unabridged. At least one real image on the page (unless product UI — dashboards, forms, settings pages exempt from image requirement).
- [ ] **13. Not the vg-design default aesthetic.** Does this output look different from the last one? Vary font, hero structure, section rhythm, CTA style, or image treatment across projects.

## Success Checks (Run for Marketing/Landing Pages)

- [ ] **Value prop clarity:** Can a first-time visitor state what this is in one sentence after 5 seconds?
- [ ] **Objection handling:** Does the page preempt the most likely objection (price, trust, complexity)?
- [ ] **Social proof freshness:** Are testimonials/logos relevant to the audience? Are they current?
- [ ] **Scannability:** Can someone skim headlines + CTAs and understand the offer?
- [ ] **Visual differentiation:** Does this page look distinct from "AI-designed landing page"? Would the audience remember it?
- [ ] **Information density is right:** DENSITY 1-3 breathing room, 4-7 balanced, 8-10 efficient. No data-dump sections on marketing pages (use top-N + "view all").
- [ ] **Copy register consistent:** One voice throughout. No mixing of technical mono, editorial prose, and marketing punch unless deliberate.

## Full Audit (Deep Quality Pass)

### Design Integrity
- [ ] One accent color, locked across all sections?
- [ ] One corner-radius system, applied consistently?
- [ ] Page Theme Locked (one theme, no mid-page flips)?
- [ ] Serif discipline check (no Fraunces/Instrument_Serif default)?
- [ ] Premium-consumer palette check (no beige+brass default)?

### Typography
- [ ] ZERO em-dashes (`—`) anywhere?
- [ ] Inter not the default (unless justified)?
- [ ] Italic descender clearance on all italic display with descenders (y g j p q)?
- [ ] Body line length ≤ 75ch?
- [ ] Display letter-spacing ≥ -0.04em?

### Layout
- [ ] Hero top padding ≤ pt-24?
- [ ] Hero stack ≤ 4 text elements, no trust strip inside hero?
- [ ] Logo wall UNDER hero, not inside?
- [ ] Eyebrow count ≤ ceil(sectionCount / 3)?
- [ ] No 3+ consecutive image+text splits?
- [ ] Section-layout repetition: ≥ 4 different families for 8 sections?
- [ ] No identical card grids?
- [ ] No nested cards?
- [ ] Bento rhythm + exact cell count (no empty cells)?
- [ ] Bento background diversity: ≥ 2-3 cells have real visual variation?
- [ ] Long lists use the right UI component (not default `<ul>` with `divide-y` for > 5 items)?

### Component
- [ ] Button contrast: every CTA WCAG AA 4.5:1?
- [ ] No wrapped CTA text at desktop?
- [ ] No duplicate CTA intent?
- [ ] Form contrast: inputs, placeholders, focus rings, labels all pass WCAG AA?
- [ ] No placeholder-as-label?

### Visual
- [ ] No div-based fake screenshots?
- [ ] Asset priority followed (provided assets > real screenshots > generated/verified stock > labeled prototype placeholders)?
- [ ] No side-stripe borders?
- [ ] No ghost-card pattern (border + wide shadow on same element)?
- [ ] No `border-radius: 32px+` on cards?
- [ ] No hand-drawn SVG illustrations?
- [ ] No gradient text?
- [ ] No glassmorphism as default?
- [ ] No `repeating-linear-gradient` stripe backgrounds?
- [ ] No decorative colored dots?

### Motion (Emil Standard)
- [ ] Every animation has a purpose?
- [ ] No animation on keyboard-initiated actions?
- [ ] No `transition: all`?
- [ ] No `ease-in` on UI?
- [ ] All easing uses Emil's custom cubic-bezier?
- [ ] All UI animations ≤ 300ms?
- [ ] Popovers use origin-aware `transform-origin`? Modals stay centered.
- [ ] No elements animate from `scale(0)`?
- [ ] Framer Motion uses `transform: "translateX()"` (not `x`/`y` shorthand)?
- [ ] `backdrop-blur` only on fixed/sticky elements?
- [ ] No `window.addEventListener('scroll')`?
- [ ] No `requestAnimationFrame` touching React state?
- [ ] Motion claimed = motion shown?

### Content
- [ ] Copy self-audit: no AI-hallucinated phrases?
- [ ] No weather/locale strips (unless brief-place)?
- [ ] No scroll cues?
- [ ] No version labels/footers on marketing (unless actual launch)?
- [ ] No section-numbering eyebrows?
- [ ] No "Quietly in use at" / "Field notes" copy?
- [ ] No fake-precise numbers without data source?
- [ ] Logo wall = logos only (no industry labels)?
- [ ] Quotes ≤ 3 lines? Attribution clean?

### Progressive Enhancement
- [ ] Reduced motion fallback for every animation MOTION_INTENSITY > 3?
- [ ] Touch hover gated behind `@media (hover: hover) and (pointer: fine)`?
- [ ] `min-h-[100dvh]` used instead of `h-screen`?
- [ ] Backdrop-filter elements have solid-fill fallback?
- [ ] Content visible in default state (not gated on transition class)?
- [ ] Mobile collapse explicit per section?
- [ ] Responsive text: tested with long names, currencies, dates, long words — no overflow?

### Performance
- [ ] LCP target < 2.5s (hero image priority/preloaded)?
- [ ] Bundle size: GSAP/Three.js lazy-loaded?
- [ ] Z-index scale documented (no arbitrary values)?
- [ ] Dependency check: package.json verified for all imports?

## Final Evidence Report

After building, report what was verified and what was not:

```
## Verification Report

### Implementation Discovery
- Design system/framework: [name or "none"]
- Icon library: [name]
- Font stack: [names]
- Existing conventions followed: [yes / exceptions noted]

### Viewports checked
- [ ] 375px (mobile)
- [ ] 768px (tablet)
- [ ] 1024px (small desktop)
- [ ] 1440px (desktop)

### Accessibility
- Contrast verified: [passed / issues]
- Keyboard nav: [passed / issues]
- Focus states: [passed / issues]
- Touch targets: [passed / issues]

### Unresolved risks
- [Known issues not yet fixed]
- [Browser-specific concerns]
```

If browser screenshotting is unavailable, state what was verified in code and what needs manual browser check.
