# Component Patterns

## React Composition Patterns

### Avoid Boolean Props
Use composition over boolean prop proliferation:

```tsx
// Bad
<Card border shadow large padding />

// Good
<Card>
  <Card.Header>...</Card.Header>
  <Card.Content>...</Card.Content>
</Card>
```

### Compound Components with Shared Context
Structure complex components with shared context so siblings communicate without prop drilling.

### Explicit Variants over Boolean Modes
Create named variant components instead of boolean flags.

### Children over Render Props
Use `children` for composition instead of `renderX` props.

### State Management
- Provider is the only place that knows how state is managed
- Define generic interface with `state`, `actions`, `meta` for dependency injection
- Lift state into provider components for sibling access
- `useState` never tracks continuous input values — use `useMotionValue` / `useTransform`
- **RSC safety:** Wrap providers in `"use client"`. Motion/scroll/pointer physics in isolated client leaves with `"use client"`

### React 19+
Don't use `forwardRef` (use `ref` prop directly). Use `use()` instead of `useContext()`.

## Component Building Patterns

### The Double-Bezel (Premium Nested Architecture)

For premium cards, use nested enclosures to feel like physical machined hardware. This uses larger radii deliberately as a premium aesthetic — distinct from standard UI card radius rules.

```
Outer shell: bg-black/5 or bg-white/5, ring-1 ring-black/5, p-1.5 or p-2, rounded-[2rem]
Inner core:  distinct bg, shadow-[inset_0_1px_1px_rgba(255,255,255,0.15)], rounded-[calc(2rem-0.375rem)]
```

### Button-In-Button CTA

Primary CTAs: `rounded-full px-6 py-3`. Trailing icon nested in its own circular wrapper (`w-8 h-8 rounded-full bg-black/5 dark:bg-white/10`), flush with button's right inner padding. On hover: inner icon translates diagonally (`group-hover:translate-x-1 group-hover:-translate-y-[1px] scale-105`).

### Button Responsiveness

Every pressable element: `transform: scale(0.97)` on `:active`, `transition: transform 160ms cubic-bezier(0.23, 1, 0.32, 1)`.

### UI State Cycles (Mandatory)

Always implement full cycles:
- **Loading:** Skeletal loaders matching final layout shape. No generic spinners.
- **Empty:** Beautifully composed, indicate how to populate.
- **Error:** Clear, inline (forms), or contextual (toasts for transient).
- **Button contrast check:** Text readable against button bg (WCAG AA 4.5:1). No white-on-white, no transparent against page bg without border.
- **CTA wrap ban:** Button text fits on ONE line at desktop. Shorten label (1-2 words ideal) or widen button.
- **No duplicate CTA intent:** "Get in touch" + "Contact us" on same page = fail. One label per intent.

### Form Patterns

- Label ABOVE input. `gap-2` for input blocks.
- No placeholder-as-label. Ever.
- Error text BELOW input.
- **Form contrast check:** Inputs, placeholders, focus rings, helper text, error text all pass WCAG AA against section background.

## Review Format (Required for UI Review)

When reviewing UI code, use a markdown table:

| Before | After | Why |
|---|---|---|
| `transition: all 300ms` | `transition: transform 200ms cubic-bezier(0.23, 1, 0.32, 1)` | Specify exact properties; avoid `all` |
| `transform: scale(0)` | `transform: scale(0.95); opacity: 0` | Nothing in the real world appears from nothing |
| `ease-in` on dropdown | `ease-out` with custom curve | `ease-in` feels sluggish |
| No `:active` state on button | `transform: scale(0.97)` | Buttons must feel responsive |
| `transform-origin: center` on popover | `transform-origin: var(--radix-popover-content-transform-origin)` | Popovers scale from trigger |
| Duration > 300ms | Reduce to 150-250ms | Faster feels more responsive |
| Framer Motion `x`/`y` shorthand | `transform: "translateX()"` | Shorthand uses main thread |
| Keyframes on rapidly-triggered | CSS transitions | Transitions are interruptible |
