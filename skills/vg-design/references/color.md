# Color

## Color Strategy (pick before picking colors)

| Level | Description |
|---|---|
| **Restrained** | Tinted neutrals + one accent ≤10%. Product default; brand minimalism. |
| **Committed** | One saturated color carries 30-60% of the surface. Brand default for identity-driven pages. |
| **Full palette** | 3-4 named roles, each used deliberately. Brand campaigns; product data viz. |
| **Drenched** | The surface IS the color. Brand heroes, campaign pages. |

## Core Rules

- **Use OKLCH** for new projects.
- **One accent color per project.** Saturation < 80% by default. Once chosen, lock it — audit every component before shipping.
- **The LILA Rule:** AI-purple/blue glow aesthetic is discouraged as default. No automatic purple button glows, no random neon gradients. Use neutral bases (Zinc/Slate/Stone) with high-contrast singular accents (Emerald, Electric Blue, Deep Rose, Burnt Orange).
- **No pure `#000000` or `#ffffff`.** Off-black (zinc-950, near-black warm gray) and off-white always. Pure values kill depth.
- **Verify contrast.** Body text ≥ 4.5:1 against background; large text (≥18px or bold ≥14px) needs ≥3:1. Placeholder text needs 4.5:1 too. Most common failure: muted gray body text on tinted near-white.
- Gray text on a colored background looks washed out. Use a darker shade of the background's own hue, or a transparency of the text color.
- **One palette per project.** Don't fluctuate between warm and cool grays within the same project.

## The Cream/Beige Ban (AI Default)

The warm-neutral band (OKLCH L 0.84-0.97, C < 0.06, hue 40-100) is banned as default body bg. Token names like `--paper`, `--cream`, `--sand`, `--bone`, `--linen` are tells. Pick one:
- A saturated brand color as the body (terracotta, oxblood, deep ochre, near-black)
- A true off-white at chroma 0 (or chroma toward the brand's own hue, not toward warmth-by-default)
- A darker mid-tone tinted neutral that's clearly the brand's own

## Premium-Consumer Palette Ban

For cookware/wellness/artisan/luxury/DTC briefs, the AI-default beige+brass+oxblood+espresso palette is BANNED. Concretely banned hex families:
- Backgrounds: `#f5f1ea`, `#f7f5f1`, `#fbf8f1`, `#efeae0`, `#ece6db`, `#faf7f1`, `#e8dfcb`
- Accents: `#b08947`, `#b6553a`, `#9a2436`, `#9c6e2a`, `#bc7c3a`, `#7d5621`
- Text: `#1a1714`, `#1a1814`, `#1b1814`

**Default alternatives (rotate, do not reuse):**
- Cold Luxury: silver-grey + chrome + smoke
- Forest: deep green + bone + amber
- Black and Tan: true off-black + warm tan
- Cobalt + Cream: saturated blue against neutral
- Terracotta + Slate: warm rust against cool grey
- Olive + Brick + Paper: muted olive plus brick-red
- Pure monochrome + single saturated pop

## Dark Mode / Theme

- **Dual-mode by default.** Never ship light-only or dark-only without explicit instruction.
- Choose theme by writing one sentence of physical scene: who uses this, where, under what ambient light, in what mood.
- **Page Theme Lock:** ONE theme for the whole page. No section flips to inverted mode mid-page. Section-level bg tints within the same theme family are fine (`bg-zinc-950` next to `bg-zinc-900`); flipping to `bg-amber-50` in the middle of a `bg-zinc-950` page is broken.
- Respect `prefers-color-scheme` unless the brand insists on one mode. Add manual toggle if needed.
- Use Tailwind `dark:` variant or CSS variables. Pick one strategy per project.
- **Token strategy** (pick one, stick to it): Tailwind `dark:` variant (default for utility-first), CSS variables (for shadcn/ui, Radix Themes).
- Maintain contrast, hierarchy parity, brand fidelity across both modes.
