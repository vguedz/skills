# Product UI — Dashboards, Tables, Forms & Repeated-Use Interfaces

This skill's typography, color, and layout rules still apply, but product UI has its own density, interaction, and visual language requirements. Landing-page defaults are wrong here.

## When to Use This Reference

Apply this when building:
- Dashboards (analytics, monitoring, ops)
- Data tables, list views, CRUD interfaces
- Forms, settings pages, wizards
- Admin tools, internal tools, devtools
- Code editors, terminals, CLIs
- Charts, graphs, data visualization
- Any interface used daily by the same person

## Core Principle: Utility Over Delight

Product UI is measured by task completion, not first impression. Every visual choice must serve utility:
- Motion that speeds up a repeated task: yes.
- Motion that slows down a repeated task: no.
- Images that convey data: yes.
- Decorative images: no.
- Cards that organize information: yes.
- Cards that add visual weight to a single metric: no.

## Density & Spacing

Product UI defaults to DENSITY 6-8. Do not use DENSITY 1-5 for dashboards unless the page has ≤ 3 data points total.

| Element | DENSITY 6-7 | DENSITY 8-10 |
|---|---|---|
| Section padding | `py-6` to `py-8` | `py-3` to `py-4` |
| Table row padding | `py-2 px-3` | `py-1 px-2` |
| Card padding | `p-4` | `p-3` |
| Gap between elements | `gap-4` | `gap-2` |
| Font size (data) | `text-sm` | `text-xs` |
| Font size (labels) | `text-xs` | `text-[11px]` |

## Tables

### Structure
- **Header row:** Sticky (`sticky top-0`). Background must match table bg (not page bg). `font-medium`, uppercase or small-caps for column labels.
- **Row dividers:** 1px hairline `border-b` in the neutral-200 range. No vertical dividers.
- **Row hover:** Subtle background change (`bg-neutral-50` / `dark:bg-neutral-900`). Must not obscure zebra striping if used.
- **Zebra striping:** Acceptable for tables with > 5 columns where horizontal scanning is required. Use `bg-neutral-50/50` on alternating rows.
- **Numeric columns:** Right-aligned. Monospaced if values are compared across rows. Use tabular-nums (`font-variant-numeric: tabular-nums`).
- **Text columns:** Left-aligned. Truncate with `truncate` + title attribute for long values.
- **Empty state:** "No results" row spanning all columns, centered, with clear next action.
- **Loading state:** Skeleton rows matching column layout. Never a single spinner.

### Sorting & Filtering
- **Sort indicators:** Arrow (▲/▼) or chevron (˄/˅) in column header. Active sort column highlighted.
- **Filters:** Above the table, not inside it. Filter chips show active state. "Clear all" available.
- **Search:** Debounced input (300ms). Placeholder: "Search [entity]" (e.g., "Search users").

### Pagination vs Infinite Scroll
- **Pagination:** Use for tables where users need to find specific rows (search, sort, jump to page).
- **Infinite scroll:** Use for feeds, activity logs, notification lists where chronological scanning is primary.

## Forms

- **Label above input.** Always. `gap-1.5` between label and input.
- **Placeholder:** Example input, not the label. "e.g., acme@company.com" not "Email".
- **Helper text:** Below input, `text-xs text-ink-muted`. Used for format hints ("Must be at least 8 characters").
- **Error text:** Below input, replaces helper text on error. `text-sm text-error`. Include specific fix: "Password must be at least 8 characters" not "Invalid password".
- **Required indicator:** Asterisk (`*`) or "(required)" text. Consistent across the form.
- **Submit button:** Full width on mobile, natural width on desktop. Disabled state must have visible reason (missing fields highlighted).
- **Disabled inputs:** Use reduced opacity + `cursor-not-allowed`. Never remove disabled inputs from the DOM (layout shift).
- **Focus ring:** Visible on all inputs. 2px offset ring in accent color. Never remove `:focus-visible` outline without replacement.

### Form Layouts
- **Single-column:** Default for most forms. Labels above, inputs full width. Max width 480px for simple forms, 640px for complex.
- **Two-column:** Only for related fields (first name + last name, city + state, start date + end date). Not for unrelated fields forced into columns.
- **Wizard/multi-step:** Show progress (step 2 of 4). Back button always available. Allow skipping optional steps.

## Charts & Data Visualization

### Color Palette for Charts
The "one accent color" rule applies to brand identity. Charts need a distinct semantic palette:

```css
--chart-1: oklch(0.55 0.18 250);  /* blue */
--chart-2: oklch(0.55 0.18 25);   /* red/coral */
--chart-3: oklch(0.55 0.15 145);  /* green */
--chart-4: oklch(0.55 0.15 85);   /* yellow/amber */
--chart-5: oklch(0.50 0.15 310);  /* purple */
--chart-6: oklch(0.50 0.10 200);  /* teal */
```

- **Sequential data** (0 → N): Single hue, varying luminance.
- **Categorical data:** Distinct hues, equal luminance. Max 6 categories before grouping "Other."
- **Diverging data** (-N → 0 → +N): Two hues meeting at a neutral midpoint.
- **Brand accent:** Use only when a single data series represents the brand. Never for categorical palettes.

### Chart States
- **Loading:** Skeleton chart (gray placeholder matching chart dimensions).
- **Empty:** "No data yet" with a clear next action ("Connect your first integration").
- **Error:** "Could not load data" with retry button. Never a blank chart area.
- **Single data point:** Still render the chart. A single bar is informative. A blank chart is not.

### Chart Elements
- **Legends:** Position below the chart, not floating inside it. Interactive (click to toggle series).
- **Axes:** Label both axes. Show units. Keep tick marks minimal.
- **Tooltips:** On hover, show exact values. Format numbers with appropriate precision.
- **Fake data labeling:** Every chart with mock data must be labeled `<!-- mock data -->`. Fake-precise chart numbers are banned (same rule as text).

## Buttons in Product UI

- **Primary:** One per view. The main action. Brand accent color.
- **Secondary:** Alternative actions. Neutral bg with border. Used for "Cancel", "Back", "Reset."
- **Tertiary/Ghost:** Minimal visual weight. Used for row actions (edit, delete, copy). Icon + text or icon-only with tooltip.
- **Destructive:** Red/coral. Used for delete, remove, reset. Requires confirmation for irreversible actions.
- **Disabled buttons:** Visible but non-interactive. Explain WHY with adjacent helper text, validation messages, or a focusable wrapper when a tooltip is appropriate. Do not rely on tooltip-only explanations.
- **Loading buttons:** Spinner replaces icon, text stays. Button remains same width (no layout shift). Disabled during loading.

### Scale-on-Active Gate (Overrides General Rule)

The "all pressables scale on active" rule from motion.md DOES NOT APPLY to:
- **Keyboard-driven tools** (command palette, terminal, code editor, search)
- **Repeated actions** (> 10 times per session: table row clicks, checkbox toggles, filter chips, nav items, pagination)
- **Dense product UI** (DENSITY ≥ 7: dashboard cards, data tables, form fields)
- **Segmented controls, toggle groups, tab bars** (constant scale-on-active creates visual noise)

These elements use a 1px translate or background change instead of scale. Scale is reserved for standalone primary CTAs, landing-page buttons, and premium marketing interactions.

## Repeated-Use Interactions

For interfaces used daily:

- **Skip intro animations.** No stagger reveals, no entrance transitions. Content renders immediately.
- **Instant state changes.** Toggles, checkboxes, selects must not animate. The state change IS the feedback.
- **Optimistic updates.** Show the new state immediately, revert on error. Never show a spinner for a toggle.
- **Keyboard shortcuts.** High-frequency power-user actions should have shortcuts. Show the shortcut in tooltips or menu items when one exists.
- **Undo, not confirm.** For reversible actions, show an undo toast instead of a confirmation dialog.

## Dashboards — Layout Rules

- **No hero section.** The first row of data IS the hero.
- **No decorative images.** Every image is a chart, logo, or avatar.
- **No eyebrow labels.** Section headers are functional: "Revenue", "Active users", "Recent activity."
- **No card shadows on metric tiles.** Use `border` or `bg` elevation instead. Shadows on dense grids create visual noise.
- **Responsive behavior:** Charts collapse to full-width single column below 768px. Tables add horizontal scroll. Filter bar collapses to a drawer or dropdown.
