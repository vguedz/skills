# Layout & Spacing

## Hero Discipline

- **Hero MUST fit the initial viewport.** Headline max 2 lines on desktop, subtext max 20 words AND max 4 lines, CTAs visible without scroll.
- **Hero font-scale discipline.** Plan font size and image size together. Default sensible range: `text-4xl md:text-5xl lg:text-6xl` for most heroes; `text-6xl md:text-7xl` only when headline is 3-5 words.
- **Hero top padding cap:** max `pt-24` (~6rem) at desktop. More than that floats the hero halfway down the viewport.
- **Hero stack max 4 text elements:**
  1. Eyebrow (small label) OR brand strip — pick zero or one
  2. Headline (max 2 lines)
  3. Subtext (max 20 words, max 4 lines)
  4. CTAs (1 primary + max 1 secondary)
- **Banned in hero:** taglines below CTAs, trust micro-strips ("Used by engineering teams at..."), pricing teaser, feature bullets, social-proof avatar rows, "Used by" / "Trusted by" logo wall.
- **Hero needs a real visual.** Text + gradient blob is not a hero. Use image-gen tool first, Picsum second, then explicit placeholder slots.

## Layout Rules

- **Anti-center bias:** Centered hero/H1 avoided when `DESIGN_VARIANCE > 4`. Force split-screen, asymmetric whitespace, or scroll-pinned structures. Override: centered hero is OK for editorial/manifesto/launch-announcement.
- **Section-layout repetition ban:** Each layout family appears at most ONCE per page. A landing page with 8 sections must use ≥ 4 different layout families.
- **Zigzag alternation cap:** Max 2 consecutive image+text split sections. The 3rd consecutive split is a pre-flight fail. Break pattern with full-width section, vertical stack, bento, marquee.
- **3-column equal feature cards are banned.** Use 2-column zig-zag, asymmetric grid, bento, scroll-pinned, or horizontal-scroll.
- **Bento grids must have rhythm.** Not 6 white-on-white text cards. ≥ 2-3 cells need real visual variation (image, gradient, pattern). Exact cell count: N items → N cells, no empty cells.
- **Eyebrow restraint:** Max 1 eyebrow per 3 sections. Hero counts as 1. Count mechanically: instances of `uppercase tracking` micro-labels ≤ ceil(sectionCount / 3).
- **Split-header ban:** "Left big headline + right small explainer paragraph" as section header is banned as default. Stack vertically (headline on top, body below, max-width 65ch).
- **Navigation:** ONE line at desktop, height ≤ 80px. If items don't fit at lg (1024px), condense or use hamburger.
- **Mobile collapse must be explicit per section.** For every multi-column layout, declare the < 768px fallback.

## Spacing

- Vary spacing for rhythm. Section padding: `py-24` to `py-40` for premium.
- Flexbox for 1D, Grid for 2D. `repeat(auto-fit, minmax(280px, 1fr))` for responsive grids without breakpoints.
- `min-h-[100dvh]` never `h-screen` (prevents iOS Safari viewport jumping).
- Semantic z-index scale: dropdown → sticky → modal-backdrop → modal → toast → tooltip. Never arbitrary 999/9999.
- Standard breakpoints: `sm 640, md 768, lg 1024, xl 1280, 2xl 1536`.

## Cards

- Cards are the lazy answer. Use only when elevation communicates real hierarchy. Prefer `border-t`, `divide-y`, or negative space.
- **No nested cards. Ever.**
- **Shape consistency lock:** Pick ONE corner-radius scale for the page and stick to it: all-sharp (0), all-soft (12-16px), all-pill (full for interactive only). Mixed systems allowed only with documented rules.
- **Ghost-card ban:** `border: 1px solid X` + `box-shadow: 0 Npx Mpx ...` with M ≥ 16px on the same element. Pick one.
- When shadow is used, tint it to the background hue. No pure-black drop shadows on light backgrounds.
