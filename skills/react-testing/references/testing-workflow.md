# Test Planning & Execution Workflow

This guide combines a structured test planning methodology with practical execution patterns. Use it as the primary reference when building test coverage for a feature.

---

## Phase 1: Discovery — Map The Use Cases

Before writing a single line of test code, invest time in understanding the full picture.

### 1.1 Determine Scope

If a specific feature is provided, focus there. If not, ask the user to confirm scope before proceeding. Testing without a clear boundary leads to scope creep and unfocused coverage.

### 1.2 Investigate Exhaustively From a User Perspective

Walk through the application as a user would. For each feature, ask:

- What does the user see on first load?
- What actions can the user take? (click, type, select, navigate)
- What feedback does the user receive? (success, error, loading, empty)
- What happens at the edges? (no data, invalid input, timeout, permission denied)
- What state transitions occur? (loading → loaded, idle → submitting → success)

Write these down as plain-language scenarios. Don't think about test code yet.

### 1.3 Identify Conditions and Risk Gaps

For each scenario, classify the condition and risk level:

```
Condition: "when the user submits an empty form"
Risk: HIGH — blocks core workflow, visible to user
```

```
Condition: "when the API returns a 500 error"
Risk: HIGH — user sees a broken state, no fallback
```

```
Condition: "when the user has no items in their cart"
Risk: MEDIUM — edge case, but empty state should be handled
```

Focus testing effort on high-risk gaps. A missing validation message on a payment form is more critical than a missing tooltip on an info icon.

### 1.4 Focus On Use Cases, Not Implementation

A use case describes a user-visible outcome:

| Use case (good) | Implementation detail (avoid) |
|---|---|
| "User submits form with valid data" | "Component calls onSubmit prop" |
| "User sees error when email is invalid" | "validateEmail returns false" |
| "User navigates to settings page" | "Router pushes /settings" |

Use cases survive refactors. Implementation details don't.

---

## Phase 2: Scaffolding — Block Out The Coverage

### 2.1 Map Empty Describe/Test Blocks First

Before writing assertions, lay out the full coverage plan as empty test blocks. This forces you to think about what to test, not how.

```tsx
describe('LoginForm', () => {
  describe('when form is empty', () => {
    test('should show validation errors on submit')
    test('should keep submit button disabled')
  })

  describe('when email is invalid', () => {
    test('should show email error message')
    test('should prevent submission')
  })

  describe('when credentials are valid', () => {
    test('should call onSubmit with form data')
    test('should show loading state during submission')
  })

  describe('when submission fails', () => {
    test('should show server error message')
    test('should allow retry')
  })
})
```

This gives you immediate visibility into coverage gaps. Are all user conditions covered? Are error states handled? Are loading and empty states accounted for?

### 2.2 Validate Each Block With the User

Present the scaffolded blocks to the user interactively, grouped by feature or condition. This serves two purposes:

1. **Alignment**: The user confirms what will be tested before you invest time writing it.
2. **Gap detection**: The user often spots missing scenarios you didn't consider.

Prompt format:

```
Here's the proposed test coverage for LoginForm:

When form is empty:
  - should show validation errors on submit
  - should keep submit button disabled

When email is invalid:
  - should show email error message
  - should prevent submission

... (remaining blocks)

Does this cover all scenarios you care about? Any gaps or changes?
```

Only proceed to writing tests after the user confirms the plan.

---

## Phase 3: Execution — Fill In The Tests

### 3.1 Use a Setup Function

Prefer a local `setup()` function over `beforeEach`. Lifecycle hooks couple tests and obscure what each test depends on. A setup function makes dependencies explicit and scope clear.

```tsx
type SetupProps = {
  initialEmail?: string
  isSubmitting?: boolean
}

function setup(props?: SetupProps) {
  const user = userEvent.setup()
  const onSubmit = vi.fn()

  render(
    <LoginForm
      onSubmit={onSubmit}
      isSubmitting={props?.isSubmitting ?? false}
    />,
  )

  // Pre-fill fields if specified
  if (props?.initialEmail) {
    // Note: userEvent is async, but we can't await in setup.
    // Pre-fill should be done in the test body or via props/defaults.
  }

  return { user, onSubmit }
}
```

Key rules for setup functions:

- Declare mocks, spies, and renderings inside the function — they are cleaned up naturally when the test ends.
- Return handles the test needs (user instance, mocks, async helpers). Don't return render internals.
- Accept overrides as arguments instead of mutating shared globals.
- For async helpers (like waiting for a page to load), return functions the test can await:

```tsx
function setup() {
  render(<HomePage />)
  return {
    waitForPageToLoad: async () => {
      const loadingLabel = screen.queryByText('Loading...')
      await waitForElementToBeRemoved(loadingLabel)
    },
  }
}
```

### 3.2 Follow the AAA Pattern

Every test follows **Arrange → Act → Assert**:

```tsx
test('should show error when email is missing', async () => {
  // Arrange
  const { user } = setup()

  // Act
  await user.click(screen.getByRole('button', { name: /submit/i }))

  // Assert
  expect(screen.getByText(/email is required/i)).toBeVisible()
})
```

### 3.3 Query By Accessibility

Priority order (most to least resilient):

1. `getByRole('button', { name: /submit/i })` — matches assistive tech
2. `getByLabelText(/email/i)` — form fields
3. `getByPlaceholderText(/search/i)` — inputs
4. `getByText(/welcome back/i)` — visible text
5. `getByDisplayValue('user@email.com')` — input values
6. `getByTestId('submit-btn')` — last resort

Always use `screen` queries so tests read like looking at the page.

### 3.4 Interact With userEvent

Use `userEvent.setup()` for all interactions. It models real user behavior (typing, clicking, tabbing) more accurately than `fireEvent`.

```tsx
const user = userEvent.setup()
await user.type(screen.getByLabelText(/email/i), 'a@b.com')
await user.click(screen.getByRole('button', { name: /submit/i }))
await user.selectOptions(screen.getByLabelText(/role/i), 'admin')
await user.tab() // keyboard navigation
```

### 3.5 Handle Async With Loading Checkpoints

Don't wait for a final state. Assert intermediate state transitions.

**Good** — asserts the transition:
```tsx
test('should display user data after loading', async () => {
  const { waitForPageToLoad } = setup()
  await waitForPageToLoad() // asserts loading disappeared

  const username = screen.getByText('Fulano de Tal')
  expect(username).toBeVisible()
})
```

**Avoid** — just waits for the end state:
```tsx
test('should display user data', async () => {
  setup()
  const username = await screen.findByText('Fulano de Tal')
  // Missed: did it show a loading state? Did it handle errors?
})
```

Async pattern reference:

| Goal | Method |
|---|---|
| Element should appear | `findBy*` (combines `waitFor` + `getBy`) |
| Element should disappear | `waitForElementToBeRemoved` |
| Complex assertion over time | `waitFor(() => expect(...))` |
| Manual act needed? | Almost never — Testing Library handles it |

### 3.6 Assert What the User Sees

Prefer visibility and existence matchers over DOM property checks:

```
toBeVisible()              — element is displayed (preferred over toBeInTheDocument)
not.toBeInTheDocument()    — element no longer exists
toBeDisabled()             — form element is disabled
toHaveTextContent()        — element contains text
toHaveValue()              — input has a value
toHaveBeenCalledWith()     — mock was called with specific args
```

### 3.7 Test Naming Convention

Format: `"should <result> - for <action>"`

Describe blocks describe conditions. Test names describe outcomes.

```tsx
describe('when user is shopping an item', () => {
  test('should add item to cart - for user click on add button')
  test('should show item count in header - for any cart update')
  test('should disable add button - when item is out of stock')
})
```

### 3.8 Break Long Flows Into Smaller Tests

If a workflow is A → B → C, don't write one test for A → C. Write two tests:

- Test 1: A → B
- Test 2: B → C (with the assumption that A → B works)

This makes failures more precise and tests easier to debug.

### 3.9 Avoid Hasty Abstractions

Prefer duplication over cleverness. A helper is useful when it removes incidental setup and makes the scenario easier to read. It's harmful when it hides the behavior under test or requires jumping through multiple files.

If a helper starts encoding its own mini-language, inline the workflow instead.

---

## Phase 4: Strategy — Choose The Right Approach

### 4.1 Match Test Level to Confidence Need

| Level | What it covers | When to use |
|---|---|---|
| **Static** | Syntax, types, lint | Always — caught before runtime |
| **Unit** | Pure logic, reducers, formatters | Branches hard to reach via UI |
| **Integration** | Components, providers, workflows | **Preferred for most UI behavior** |
| **E2E** | Full system: login, routing, browser APIs | Critical paths only (fewest, highest confidence) |

Integration tests give the best return for UI apps: they cover real component composition with providers, routers, and stores without the cost of full browser automation.

### 4.2 Mock at the Right Boundary

Mock less of the UI tree and more of the true boundary:

- **Use real providers, routers, and stores** — they surface integration bugs that shallow tests miss.
- **Network is the right boundary** — use MSW handlers so components exercise realistic loading, success, and error states without hitting real services.
- **Module mocks for hard boundaries** — browser APIs (`IntersectionObserver`), time (`Date`), analytics SDKs, auth SDKs, feature flags.

```tsx
// Good: mock at the network boundary with MSW
server.use(
  http.get('/api/users', () => HttpResponse.json([{ id: 1, name: 'Alice' }])),
)

// Good: mock hard browser APIs
vi.spyOn(IntersectionObserver.prototype, 'observe').mockReturnValue()
```

### 4.3 Test Behavior, Not Implementation

Implementation details are things users don't see: state variables, component instances, lifecycle methods, CSS selectors, DOM shape. Tests that assert these break during harmless refactors.

**Avoid:**
```tsx
expect(wrapper.state('isOpen')).toBe(true)        // Enzyme-style
expect(component.find('.dropdown').exists()).toBe(true) // CSS selector
expect(MyChild).toHaveBeenCalledWith({ foo: 1 })     // mocked child props
```

**Prefer:**
```tsx
expect(screen.getByRole('menu')).toBeVisible()
expect(screen.getByText('Profile')).toBeVisible()
```

### 4.4 Keep Tests Flat

Deep `describe` nesting and shared mutable `beforeEach` make tests harder to understand and easier to accidentally couple. Each test should arrange its own important data, even if that repeats a few lines.

### 4.5 Snapshots: Small and Intentional

Snapshots are useful for small, intentional outputs: serialized config, error messages with exact formatting, tiny presentational primitives. Large UI snapshots become update-by-reflex noise. For behavior, prefer explicit assertions.

---

## Quick Reference Checklist

Before writing tests:
- [ ] Feature scope confirmed with user
- [ ] All user-visible scenarios mapped (loading, empty, error, success, edge cases)
- [ ] Risk gaps identified and prioritized
- [ ] Empty `describe`/`test` blocks scaffolded
- [ ] Blocks validated with user

While writing tests:
- [ ] Each test uses a local `setup()` function (not `beforeEach`)
- [ ] Follows AAA: Arrange → Act → Assert
- [ ] Queries use accessible roles (getByRole, getByLabelText)
- [ ] Interactions use `userEvent.setup()`
- [ ] Async handled with `findBy*` / `waitForElementToBeRemoved`
- [ ] Asserts what the user sees (toBeVisible, not.toBeInTheDocument)
- [ ] Test name follows "should <result> - for <action>"
- [ ] No implementation details asserted (state, instances, CSS selectors)

After writing tests:
- [ ] Suite passes
- [ ] Lint and type checks pass
- [ ] Coverage focused on meaningful paths, not 100%
