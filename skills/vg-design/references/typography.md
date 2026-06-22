# Typography

## Font Selection

- **Inter is discouraged as default.** Reach for Geist, Outfit, Cabinet Grotesk, Satoshi, or a brand-appropriate serif first. Inter acceptable only for neutral/Linear-style or accessibility-first sites.
- **Serif is VERY discouraged as default.** "Creative brief = serif" is the #1 AI typography tell. Serif only when the brand brief literally names a serif, OR the aesthetic is genuinely editorial/luxury/publication/heritage. Default sans-serif display for everything else.
- **Specifically banned as defaults:** Fraunces, Instrument_Serif (the LLM-favorite display serifs).
- **Emphasis rule:** Use italic or bold of the SAME font for word emphasis. Never inject a random serif word into a sans headline.
- **One family in multiple weights** beats pairing similar-but-not-identical fonts.

## Italic Descender Clearance (Mandatory)

When italic display type contains descender letters (`y g j p q`), use `leading-[1.1]` minimum + `pb-1` reserve. `leading-none` will clip the descender. Audit every italic word in display headlines before shipping.

## Type Scale

- Hero/display heading ceiling: `clamp()` max ≤ 6rem (~96px). Above that the page is shouting.
- Display letter-spacing floor: ≥ -0.04em. Default -0.02 to -0.03em for tight grotesque display. -0.05+ and letters touch.
- Body line length: cap at 65-75ch.
- `text-wrap: balance` on h1-h3; `text-wrap: pretty` on long prose.
- Default scale: Display `text-4xl md:text-6xl tracking-tighter leading-none`. Body `text-base leading-relaxed max-w-[65ch]`.

## Font Pairings

`Geist` + `Geist Mono`, `Satoshi` + `JetBrains Mono`, `Cabinet Grotesk` + `Inter Tight`, `GT America` + `IBM Plex Mono`.

## Em-Dash Ban (Non-Negotiable)

`—` (em-dash) is COMPLETELY banned everywhere. Headlines, eyebrows, pills, body, quotes, attribution, captions, buttons, alt text. Use hyphen `-`, period, comma, colon, or parentheses instead. Zero em-dashes. Zero en-dashes (`–`) as separators. This is the #1 AI visual tell.
