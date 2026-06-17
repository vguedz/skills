# Testing Philosophy

Principles behind the patterns in this skill. Understand these to know when rules apply and when they bend.

---

## 1. Tests are user workflows, not implementation checks

Test what the user sees and does. A test should survive a refactor that changes how the code works internally but preserves the user-visible outcome.

- **Assert visible outcomes**: `toBeVisible()`, `toBeDisabled()`, text content, not state variables or component instances.
- **Act through the UI**: `userEvent.click()`, `userEvent.type()`, not direct prop calls or store mutations.
- **Query accessibly**: `getByRole`, `getByLabelText`, `getByText` — the same surface assistive tech sees. Avoid `getByTestId` except when no accessible surface exists.

## 2. Tests are deterministic

Every test knows its exact starting state and expected outcome. There is no uncertainty, so there is no conditional logic.

- **Never use `if`, `switch`, or ternary in test code.** If you think you need one, you're either uncertain about the component's behaviour (investigate first), or you're abstracting two distinct scenarios into one test (split them instead).
- **No conditionals in setup helpers either.** `waitForLoadToFinish` does not check "is there a loading state?" — it asserts one exists and waits for it to disappear. If there's no loading state, the test should fail immediately.

## 3. No shared mutable state between tests

Each test owns its scenario completely. Shared state creates coupling that makes failures hard to debug and tests fragile to reordering.

- **Prefer `setup()` over `beforeEach`.** A setup function returns handles; the test destructures only what it needs. Tests don't depend on module-level variables set by a previous test.
- **Pass overrides as arguments.** Never mutate a shared global to signal "this test is different."
- **`setup()` goes at module level**, outside `describe` blocks, so it's visible as a shared arrange pattern. Only nest it inside a `describe` when the component props or providers differ so much that the outermost setup would be misleading.

## 4. Loading states are part of the user workflow

The user sees a spinner, then data. Your test should assert both. Skipping the loading state is missing a user-visible behaviour.

- **Assert the loading checkpoint, then the result.** Use `waitForLoadToFinish` from `setup()`, then assert content.
- **Return loaders from `setup()`** so every test shares the same loading assertion. No ad-hoc `findByText` scattered across tests.

## 5. Mocks go at the true boundary

Mock at the network, not inside the UI tree. Use real providers, routers, and stores — they surface integration bugs.

- **Network is the right boundary.** Use MSW handlers. Declare the response, then assert the UI outcome. Prefer user-visible outcomes over request assertions. Assert request payloads when the request body is the meaningful contract (checkout, destructive actions, form serialization where no visible outcome proves data was sent correctly).
- **Never replicate server business logic** in handlers. Each test declares the exact response it needs — no server-side validation duplicated in the mock.
- **Never leave unmatched requests.** `server.listen({ onUnhandledRequest: 'error' })`. If the component makes a call you didn't handle, the test fails immediately.

## 6. Snapshots are small and intentional

Large UI snapshots become update-by-reflex noise. Use them only for small, intentional outputs where formatting matters: serialized config, error messages with exact wording, tiny presentational primitives. For behaviour, prefer explicit assertions.

## 7. Don't chase coverage percentages

Cover meaningful paths, not every branch. A 100% coverage number with weak assertions is worse than 80% coverage with strong user-visible assertions. Focus on high-risk gaps: payment flows, auth boundaries, data loss scenarios.

## 8. Avoid hasty abstractions

Prefer duplication over cleverness. A helper is useful when it removes incidental setup and makes the scenario easier to read. It's harmful when it hides the behaviour under test, requires jumping through multiple files, or encodes its own mini-language. If a helper needs conditionals (`if`, `switch`), it's an abstraction that doesn't pull its weight.
