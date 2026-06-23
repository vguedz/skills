---
name: react-testing
description: "React Testing Library: user-centric component testing with queries, user-event simulation, async utilities, and accessibility-first API. Use when writing React component tests, selecting elements by role/label/text, simulating user events, or testing async UI behavior."
---

# React Testing

## Quick Start

```tsx
import { render, screen, waitForElementToBeRemoved } from '@testing-library/react'
import userEvent from '@testing-library/user-event'

function setup() {
  const user = userEvent.setup()
  render(<MyForm />)
  return {
    user,
    waitForLoadToFinish: () =>
      waitForElementToBeRemoved(() => screen.getByText(/loading/i)),
  }
}

test('shows success after valid submission', async () => {
  const { user, waitForLoadToFinish } = setup()

  await user.type(screen.getByLabelText(/email/i), 'a@b.com')
  await user.click(screen.getByRole('button', { name: /submit/i }))

  await waitForLoadToFinish()
  expect(screen.getByText(/success/i)).toBeVisible()
})
```

## Workflow

1. **Inspect the test stack**: runner, environment, library versions, MSW, providers.
2. **Plan coverage**: map user scenarios, scaffold empty `describe`/`test` blocks, validate with user. See [testing-workflow.md](references/testing-workflow.md).
3. **Choose test level**: Static → Unit → Integration (preferred for UI) → E2E (critical paths only).
4. **Write as user workflow** (AAA): Arrange with `setup()`. Act with `userEvent`. Assert with jest-dom matchers.
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

Prefer a module-level `setup()` function over lifecycle hooks. Only nest inside `describe` when props or providers differ significantly between groups. Return shared async checkpoints (`waitForLoadToFinish`) when the UI has a meaningful loading, submitting, or pending state.

```tsx
// assuming Vitest globals; otherwise import { vi } from 'vitest'
type SetupProps = { /* test-specific config */ }
function setup(props?: SetupProps) {
  const user = userEvent.setup()
  const onSubmit = vi.fn()
  render(<MyComponent onSubmit={onSubmit} {...props} />)
  return {
    user,
    onSubmit,
    waitForLoadToFinish: () =>
      waitForElementToBeRemoved(() => screen.getByText(/loading/i)),
  }
}
```

### Naming

Format: `"should <result> - for <action>"`. Group related scenarios under `describe` blocks that describe conditions. Break long flows into smaller tests: if A → B and B → C, test A → B and B → C separately.

### File Organization

- Co-locate tests next to source: `Button.test.tsx` beside `Button.tsx`.
- Shared render, providers, and MSW server in `src/test-utils.tsx` or `src/test/`.
- Fixtures in `__fixtures__/` or `src/test/fixtures/`.
- See [test-organization.md](references/test-organization.md) for full conventions.

## Testing APIs

**Async**: `findBy*` (wait for appear), `waitForElementToBeRemoved` (wait for disappear, MutationObserver — preferred over polling), `waitFor(() => ...)`. Return `waitForLoadToFinish` from `setup()` when the component has a meaningful loading state. Avoid manual `act`. See [testing-appearance-disappearance.md](references/testing-appearance-disappearance.md) for patterns and trade-offs.

**Assertions**: `toBeVisible()` over `toBeInTheDocument`, `not.toBeInTheDocument()` for removal, `toBeDisabled()`, `toHaveTextContent()`, `toHaveValue()`, `toHaveBeenCalledWith()`.

## Pitfalls

See [testing-philosophy.md](references/testing-philosophy.md) for the principles behind these patterns: no implementation-detail assertions, no shared mutable state, no conditionals in test code, deterministic loading checkpoints, mock at the network boundary, small snapshots, avoid hasty abstractions.

## See Also

- [EXAMPLES.md](EXAMPLES.md) — concrete patterns for forms, async states with MSW, routing, multiple providers, loading checkpoints
- [references/testing-philosophy.md](references/testing-philosophy.md) — principles: determinism, no conditionals, no shared state, behavioral assertions, mock at boundaries
- [references/testing-workflow.md](references/testing-workflow.md) — full test planning methodology: mapping use cases, scaffolding blocks, user validation
- [references/test-organization.md](references/test-organization.md) — file and directory conventions for test suites
- [references/msw-mocking.md](references/msw-mocking.md) — MSW server mocking: test against responses, not requests. server.use() patterns, never leave unmatched handlers
- [references/testing-guide.md](references/testing-guide.md) — deeper rationale on test strategy, mocking, snapshots, and existing projects
- [references/testing-appearance-disappearance.md](references/testing-appearance-disappearance.md) — appearance and disappearance patterns: `findBy*`, `waitForElementToBeRemoved`, `waitFor`, and absence assertions
