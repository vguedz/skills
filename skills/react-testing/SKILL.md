---
name: react-testing
description: "React Testing Library: user-centric component testing with queries, user-event simulation, async utilities, and accessibility-first API. Use when writing React component tests, selecting elements by role/label/text, simulating user events, or testing async UI behavior."
---

# React Testing

## Quick Start

```tsx
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'

test('shows success after valid submission', async () => {
  const user = userEvent.setup()
  render(<MyForm />)

  await user.type(screen.getByLabelText(/email/i), 'a@b.com')
  await user.click(screen.getByRole('button', { name: /submit/i }))

  expect(await screen.findByText(/success/i)).toBeVisible()
})
```

## Workflow

1. **Inspect the test stack**: runner (vitest/jest), environment (jsdom), Testing Library version, user-event setup, jest-dom matchers, network mocking (MSW), provider wrappers (router, query client, store), and existing helper patterns.

2. **Plan test coverage** (see [testing-workflow.md](references/testing-workflow.md)):
   - Map all use cases for the feature. If no feature is specified, ask for scope confirmation.
   - Extensively investigate every possible scenario from a user perspective.
   - Identify conditions, edge cases, and risk gaps to tackle.
   - Scaffold empty `describe`/`test` blocks first to capture full coverage.
   - Validate each test block with the user interactively before filling in implementation.

3. **Choose test level**: Static (syntax/types) → Unit (pure logic) → **Integration** (preferred for UI, covers composition + providers + workflows) → E2E (critical full-system flows).

4. **Write as user workflow** (AAA): Arrange with real providers and local `setup()`. Act with `userEvent.setup()` and `screen` queries. Assert visible outcomes with jest-dom matchers.

5. **Verify**: run suite, lint, type checks.

## Query Priority

Prefer queries in this order (most to least accessible):

1. `getByRole` (with `name`) — matches how assistive tech sees the UI
2. `getByLabelText` — form fields
3. `getByPlaceholderText` — inputs
4. `getByText` — visible text content
5. `getByDisplayValue` — input values
6. `getByTestId` — only when no accessible surface exists

Always use `screen` queries so tests read like looking at the page.

## Test Structure

### Setup Pattern

Prefer a local `setup()` function over lifecycle hooks. Pass overrides as arguments rather than mutating shared globals.

```tsx
type SetupProps = { /* test-specific config */ }
function setup(props?: SetupProps) {
  const user = userEvent.setup()
  const onSubmit = vi.fn()
  render(<MyComponent onSubmit={onSubmit} {...props} />)
  return { user, onSubmit }
}
```

### Naming

Format: `"should <result> - for <action>"`. Group related scenarios under `describe` blocks that describe conditions. Break long flows into smaller tests: if A → B and B → C, test A → B and B → C separately.

### File Organization

- **Co-locate**: `Button.test.tsx` next to `Button.tsx`. Keeps tests discoverable and close to source.
- **Shared utilities**: `src/test-utils.tsx` or `src/test/` for custom render, provider wrappers, MSW server.
- **Fixtures**: `__fixtures__/` or `src/test/fixtures/` for test data and mocks.
- See [test-organization.md](references/test-organization.md) for full conventions.

## Async Patterns

- **Wait for appearance**: `findBy*` (combines `waitFor` + `getBy`)
- **Wait for disappearance**: `waitForElementToBeRemoved`
- **Loading checkpoints**: Assert intermediate loading states (e.g. `"Loading..."` disappears) instead of waiting for a final state.
- Avoid manual `act` — Testing Library and userEvent handle this.

## Assertion Guidelines

- `toBeVisible()` — displayed (preferred over `toBeInTheDocument`)
- `not.toBeInTheDocument()` — element removed
- `toBeDisabled()`, `toHaveTextContent()`, `toHaveValue()` — form states
- `toHaveBeenCalledWith()` — callback verification

## Pitfalls

- **Implementation details** (state, instances, CSS selectors, Enzyme-style `instance()`/`find('.class')`): test user-visible behavior instead.
- **Shared `beforeEach` / deep nesting**: prefer local `setup()` so each test owns its scenario.
- **Large snapshots**: keep small and targeted; prefer explicit assertions.
- **Coverage chasing**: value meaningful paths over 100%.
- **Over-abstracted helpers**: favor small, local setup functions that return handles.

## See Also

- [EXAMPLES.md](EXAMPLES.md) — concrete patterns for forms, async states, routing, optimistic updates, provider wrappers
- [references/testing-workflow.md](references/testing-workflow.md) — full test planning methodology: mapping use cases, scaffolding blocks, user validation
- [references/test-organization.md](references/test-organization.md) — file and directory conventions for test suites
- [references/msw-mocking.md](references/msw-mocking.md) — MSW server mocking: test against responses, not requests. server.use() patterns, never leave unmatched handlers
- [references/testing-guide.md](references/testing-guide.md) — deeper rationale on test strategy, mocking, snapshots, and existing projects
