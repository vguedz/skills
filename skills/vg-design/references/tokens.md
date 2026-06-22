# Design Tokens & Theming

Build tokens before components. A consistent token layer prevents drift and makes dark mode, accessibility, and responsive design affordable.

> **Note on pure white/black:** Token examples use `--neutral-50` (near-white, `oklch(0.98 0.005 ...)`) and `--neutral-950` (near-black, `oklch(0.12 0.005 ...)`). Components that read "white" or "black" in semantic names (e.g., `--color-accent-ink`) map to `--neutral-50` or `--neutral-950`, never `#fff` or `#000`.

## Semantic Token Architecture

Define tokens in layers. Never hardcode values in components.

```
Raw values (OKLCH/hex) → Primitive tokens → Semantic tokens → Components
```

### Layer 1: Primitive Tokens (raw values)

```css
:root {
  /* Brand primitives */
  --brand-50: oklch(0.98 0.01 250);
  --brand-100: oklch(0.95 0.02 250);
  --brand-200: oklch(0.88 0.04 250);
  --brand-300: oklch(0.76 0.08 250);
  --brand-400: oklch(0.64 0.12 250);
  --brand-500: oklch(0.55 0.15 250);
  --brand-700: oklch(0.40 0.12 250);
  --brand-900: oklch(0.25 0.05 250);

  /* Neutral primitives (chroma toward brand hue, not warmth-by-default) */
  --neutral-50:  oklch(0.98 0.005 250);
  --neutral-100: oklch(0.95 0.005 250);
  --neutral-200: oklch(0.88 0.005 250);
  --neutral-400: oklch(0.65 0.005 250);
  --neutral-600: oklch(0.45 0.005 250);
  --neutral-800: oklch(0.28 0.005 250);
  --neutral-900: oklch(0.18 0.005 250);
  --neutral-950: oklch(0.12 0.005 250);
}
```

### Layer 2: Semantic Tokens (role-bound)

```css
:root {
  --color-bg: var(--neutral-50);
  --color-bg-elevated: var(--neutral-100);
  --color-surface: var(--neutral-50);
  --color-surface-hover: var(--neutral-100);
  --color-border: var(--neutral-200);
  --color-border-strong: var(--neutral-400);
  --color-ink: var(--neutral-900);
  --color-ink-muted: var(--neutral-600);
  --color-ink-subtle: var(--neutral-400);
  --color-accent: var(--brand-500);
  --color-accent-hover: var(--brand-700);
  --color-accent-ink: var(--neutral-50);
  --color-error: oklch(0.55 0.18 25);
  --color-success: oklch(0.55 0.15 145);
}
```

### Layer 3: Component-agnostic tokens (reusable across components)

```css
:root {
  --radius-sm: 6px;
  --radius-md: 10px;
  --radius-lg: 14px;
  --radius-full: 999px;

  --shadow-sm: 0 1px 2px oklch(0.12 0.005 250 / 0.06);
  --shadow-md: 0 4px 12px oklch(0.12 0.005 250 / 0.08);
  --shadow-lg: 0 12px 32px oklch(0.12 0.005 250 / 0.10);

  --ease-out: cubic-bezier(0.23, 1, 0.32, 1);
  --ease-in-out: cubic-bezier(0.77, 0, 0.175, 1);
  --ease-drawer: cubic-bezier(0.32, 0.72, 0, 1);

  --duration-fast: 160ms;
  --duration-normal: 200ms;
  --duration-slow: 350ms;
  --duration-enter: 300ms;
}
```

## Dark Mode Pairing

Use the CSS variable strategy so a single `data-theme` or `prefers-color-scheme` swap cascades everywhere.

```css
[data-theme="dark"] {
  --color-bg: var(--neutral-950);
  --color-bg-elevated: var(--neutral-900);
  --color-surface: var(--neutral-900);
  --color-surface-hover: var(--neutral-800);
  --color-border: var(--neutral-800);
  --color-border-strong: var(--neutral-600);
  --color-ink: var(--neutral-100);
  --color-ink-muted: var(--neutral-400);
  --color-ink-subtle: var(--neutral-600);
  --color-accent: var(--brand-400);
  --color-accent-hover: var(--brand-300);
  --color-accent-ink: var(--neutral-950);
}
```

**Rules for dark mode tokens:**
- Never desaturate the accent into a different color. Shift luminance, keep chroma.
- Maintain hierarchy parity: if a CTA pops in light, it pops in dark.
- Shadows in dark mode: use near-black overlays (`oklch(0.05 0.005 250 / 0.30)`) so they're visible against dark surfaces.
- `--neutral-950` (near-black), never `#000`. `--neutral-50` (near-white), never `#fff`.

## Tailwind v4 Mapping

In Tailwind v4, map tokens directly in CSS using `@theme`:

```css
@import "tailwindcss";

@theme {
  --color-app-bg: var(--color-bg);
  --color-app-surface: var(--color-surface);
  --color-app-border: var(--color-border);
  --color-app-ink: var(--color-ink);
  --color-app-ink-muted: var(--color-ink-muted);
  --color-brand: var(--color-accent);
  --color-brand-hover: var(--color-accent-hover);
  --color-brand-ink: var(--color-accent-ink);
  --radius-sm: var(--radius-sm);
  --radius-md: var(--radius-md);
  --radius-lg: var(--radius-lg);
  --animate-fade-in: fade-in var(--duration-enter) var(--ease-out);
}
```

Components then use `bg-app-bg`, `text-app-ink`, `border-app-border`, `bg-brand`, `text-brand-ink`, `rounded-md`, `animate-fade-in`, etc.

## When Arbitrary Values Are Acceptable

Arbitrary Tailwind values (`w-[320px]`, `rounded-[2rem]`) are acceptable when:

1. **The value is structural, not thematic.** Grid spans and layout declarations are not tokens.
2. **The value is a one-off premium technique.** Double-bezel cards use `rounded-[2rem]`.
3. **The value is a prototype iteration placeholder.** Promote repeated values to tokens before shipping.

**Never use arbitrary values for:** colors, spacing that repeats, typography scales, shadows, border widths that form a system.

## OKLCH Quick Reference

```
oklch(L C H)
  L = Lightness (0-1, practical range 0.05-0.98)
  C = Chroma (0-0.37, higher = more saturated)
  H = Hue (0-360, same as HSL hue wheel)
```

```css
--neutral-100: oklch(0.95 0.005 250);   /* light near-white with brand tint */
--accent: oklch(0.55 0.15 250);          /* saturated accent */
--ink: oklch(0.18 0.01 250);             /* deep near-black with hint of brand */
--off-white: oklch(0.97 0 0);            /* true off-white at chroma 0 */
```

The cream/beige AI-default lives at L 0.84-0.97, C < 0.06, H 40-100. Avoid this band for body backgrounds.
