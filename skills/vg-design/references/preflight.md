# Pre-Flight Checklist

Run this matrix before delivering ANY frontend output. If any checkbox cannot be honestly ticked, the output is not done.

## Design Integrity

- [ ] Design Read declared (one-liner)?
- [ ] Dial values explicit and reasoned from brief?
- [ ] Design system chosen, or aesthetic labeled honestly?
- [ ] One accent color, locked across all sections?
- [ ] One corner-radius system, applied consistently?
- [ ] Page Theme Locked (one theme, no mid-page flips)?
- [ ] Serif discipline check (no Fraunces/Instrument_Serif default)?
- [ ] Premium-consumer palette check (no beige+brass default)?

## Typography

- [ ] ZERO em-dashes (`—`) anywhere?
- [ ] Inter not the default (unless justified)?
- [ ] Italic descender clearance on all italic display with descenders (y g j p q)?
- [ ] Body line length ≤ 75ch?
- [ ] Display letter-spacing ≥ -0.04em?

## Layout

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

## Component

- [ ] Button contrast: every CTA WCAG AA 4.5:1?
- [ ] No wrapped CTA text at desktop?
- [ ] No duplicate CTA intent?
- [ ] Form contrast: all inputs pass WCAG AA?
- [ ] Loading/empty/error states provided?
- [ ] No placeholder-as-label?

## Visual

- [ ] No div-based fake screenshots?
- [ ] Real images used (gen-tool > Picsum > placeholder slots)?
- [ ] No side-stripe borders?
- [ ] No ghost-card pattern (border + wide shadow on same element)?
- [ ] No `border-radius: 32px+` on cards?
- [ ] No hand-drawn SVG illustrations?
- [ ] No gradient text?
- [ ] No glassmorphism as default?
- [ ] No `repeating-linear-gradient` stripe backgrounds?

## Motion (Emil Standard)

- [ ] Every animation has a purpose (not "it looked cool")?
- [ ] No animation on keyboard-initiated actions?
- [ ] No `transition: all` — every transition specifies exact properties?
- [ ] No `ease-in` used on any UI animation?
- [ ] All easing uses Emil's custom cubic-bezier curves?
- [ ] All UI animations ≤ 300ms?
- [ ] Popovers use origin-aware `transform-origin`? Modals stay centered.
- [ ] No elements animate from `scale(0)`?
- [ ] Framer Motion uses `transform: "translateX()"` for hardware acceleration (not `x`/`y` shorthand)?
- [ ] `backdrop-blur` only on fixed/sticky elements?
- [ ] Reduced motion gated for MOTION_INTENSITY > 3?
- [ ] Touch hover states gated behind `@media (hover: hover) and (pointer: fine)`?
- [ ] No `window.addEventListener('scroll')`?
- [ ] No `requestAnimationFrame` touching React state?
- [ ] Motion claimed = motion shown (no dial claim mismatch)?

## Content

- [ ] Copy self-audit: no AI-hallucinated phrases, no grammatically-broken strings?
- [ ] No em-dashes in copy?
- [ ] No weather/locale strips (unless brief-place)?
- [ ] No scroll cues?
- [ ] No version labels/footers on marketing (unless actual launch)?
- [ ] No decorative colored dots?
- [ ] No section-numbering eyebrows?
- [ ] No "Quietly in use at" / "Field notes" / "From the field" copy?
- [ ] No fake-precise numbers without real data?
- [ ] Logo wall = logos only (no industry labels)?

## Output Quality

- [ ] No banned placeholder patterns (`// ...`, `// TODO`, `// rest of code`, bare `...`)?
- [ ] Every code block is complete, runnable, unabridged?
- [ ] Does the output feel deliberate, not templated?
- [ ] At least one real image on the page?
- [ ] Mobile collapse explicit per section?
- [ ] No mixing of GSAP/Three.js with Motion in same component tree?
