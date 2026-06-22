# Layout & Spacing

## Hero Discipline (Invariant)

These rules apply regardless of dial settings. Hero is where the user decides to stay or leave.

- **Hero MUST fit the initial viewport.** Headline max 2 lines on desktop, subtext max 20 words AND max 4 lines, CTAs visible without scroll. This is a usability invariant — DENSITY/VARIANCE affect spacing and content amount, but must not hide CTAs below fold.
- **Hero font-scale discipline.** Plan font size and image size together. Default: `text-4xl md:text-5xl lg:text-6xl` for most heroes; `text-6xl md:text-7xl` only when headline is 3-5 words.
- **Hero top padding cap:** max `pt-24` (~6rem) at desktop.
- **Hero stack max 4 text elements:**
  1. Eyebrow (small label) OR brand strip — pick zero or one
  2. Headline (max 2 lines)
  3. Subtext (max 20 words, max 4 lines)
  4. CTAs (1 primary + max 1 secondary)
- **Banned in hero:** taglines below CTAs, trust micro-strips, pricing teaser, feature bullets, social-proof avatar rows, "Used by" / "Trusted by" logo wall.
- **Hero needs a real visual.** Text + gradient blob is not a hero.
- **Centered hero:** Only when VARIANCE ≤ 4, or editorial/manifesto/launch-announcement brief where the message itself IS the design. For VARIANCE > 4, use split-screen, asymmetric whitespace, or scroll-pinned structures.

## Layout Rules (Gated by VARIANCE)

### Section Rhythm
- **Section-layout repetition ban:** Each layout family appears at most ONCE per page. 8 sections → ≥ 4 different layout families.
- **Zigzag alternation cap:** Max 2 consecutive image+text split sections. The 3rd is a fail. Break with full-width section, vertical stack, bento, marquee.
- **3-column equal feature cards are banned** regardless of VARIANCE. Use 2-column zig-zag, asymmetric grid, bento, or horizontal-scroll.

### Bento Grids (VARIANCE ≥ 5)
- Not 6 white-on-white text cards. ≥ 2-3 cells need real visual variation (image, gradient, pattern).
- Exact cell count: N items → N cells, no empty cells, no placeholder filler.

### Eyebrow Restraint
Max 1 eyebrow per 3 sections. Hero counts as 1. Count mechanically: instances of `uppercase tracking` micro-labels ≤ ceil(sectionCount / 3). This applies regardless of VARIANCE.

### Split-Header Ban
"Left big headline + right small explainer paragraph" as section header is banned as default. Stack vertically (headline on top, body below, max-width 65ch).

### Navigation
ONE line at desktop, height ≤ 80px. If items don't fit at lg (1024px), condense or use hamburger.

### Mobile Collapse
Every multi-column layout must declare its < 768px fallback explicitly. For VARIANCE > 4, collapse to single-column with `w-full`, `px-4`, `py-8`. No rotations, no overlaps, no negative margins.

## Spacing (Gated by DENSITY)

| DENSITY | Section padding | Content per section | Card usage |
|---|---|---|---|
| 1-3 (Gallery) | `py-32` to `py-48` | Max 2 content units | Few, large, with breathing room |
| 4-7 (Daily) | `py-16` to `py-24` | Multiple content units | Standard use |
| 8-10 (Cockpit) | `py-8` to `py-12` | Dense, efficient | Banned — use dividers, not cards |

- Flexbox for 1D, Grid for 2D. `repeat(auto-fit, minmax(280px, 1fr))` for responsive grids without breakpoints.
- `min-h-[100dvh]` never `h-screen` (prevents iOS Safari viewport jumping).
- Semantic z-index scale: dropdown → sticky → modal-backdrop → modal → toast → tooltip. Never arbitrary 999/9999.
- Standard breakpoints: `sm 640, md 768, lg 1024, xl 1280, 2xl 1536`.

## Cards

- Cards are the lazy answer. Use only when elevation communicates real hierarchy. Prefer `border-t`, `divide-y`, or negative space.
- **No nested cards. Ever.**
- **Shape consistency lock:** Pick ONE corner-radius scale for the page: all-sharp (0), all-soft (12-16px), all-pill (full for interactive only). Mixed only with documented rules.
- **Ghost-card ban:** `border: 1px solid X` + `box-shadow: 0 Npx Mpx ...` with M ≥ 16px on same element. Pick one.
- When shadow is used, tint it to the background hue. No pure-black drop shadows.
- **Double-bezel premium cards** have their own radius scale (see [code.md](code.md)) — they are a premium technique, not part of the standard card system. Max 3 per page. Only when VARIANCE ≥ 6 AND DENSITY ≤ 5.
