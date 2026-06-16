# Testing Strategy Guide

This reference synthesizes established testing guidance for UI apps. Use it to inform test strategy, tradeoffs, and review comments; keep concrete code aligned with the app's existing test stack.

## Confidence First

Tests should increase confidence that the software works for users. Start by asking what could break in a way users or the business would notice. Favor tests that exercise real workflows over tests that only prove a component's current implementation shape.

High-confidence UI tests usually render a realistic slice of the app, interact with accessible controls, and verify visible or semantic outcomes. This does not mean every test must be broad. Small unit tests are valuable for pure logic, reducers, formatters, and branches that are difficult to reach through UI. The point is to match the test level to the confidence gap.

## Testing Trophy

A balanced testing portfolio includes:

- **Static checks** catch syntax, type, lint, and import mistakes quickly.
- **Unit tests** are useful for isolated logic with little setup.
- **Integration tests** provide the most return for many UI apps because they cover component composition, state, providers, and user workflows without the cost of full browser E2E.
- **E2E tests** cover the few flows that need full-system confidence, such as login, routing, browser behavior, backend integration, and critical purchase or data-writing paths.

When adding tests to an existing project, first inspect what already exists. Use the runner, helpers, provider wrappers, MSW handlers, fixtures, and command conventions the repo already trusts unless there is a clear reason to change them.

## Test Behavior, Not Implementation

Implementation details are things users do not directly rely on: state variables, component instances, lifecycle methods, private functions, component names, DOM structure, CSS selectors, and child component invocation details. Tests that assert those details fail during harmless refactors and can pass while user behavior is broken.

Prefer this shape:

1. **Arrange** the screen in a state a user can encounter.
2. **Act** through user-level interactions.
3. **Assert** the result the user can observe or that the app contract exposes.

Examples of resilient assertions include displayed text, accessible names, form values, disabled states, validation messages, route changes, persisted network calls at the boundary, and visible loading, success, or error states.

## Testing Library Practices

Testing Library is designed around user behavior. Prefer `screen` so queries read like looking at the page. Query by accessible role and name first because that aligns tests with how assistive technology understands the interface. Use `userEvent` for interactions because it models user behavior more closely than firing isolated DOM events.

Async tests should wait for the UI outcome. Use `findBy*` when an element should appear. Use `waitForElementToBeRemoved` for disappearance. Use `waitFor` sparingly for assertions that cannot be expressed as a single query, and keep the assertion inside the callback.

Use jest-dom matchers to state intent clearly. `toBeInTheDocument`, `toBeVisible`, `toBeDisabled`, `toHaveAccessibleName`, `toHaveTextContent`, and `toHaveValue` communicate user-facing expectations better than raw truthiness or DOM property checks.

Avoid manual `act` around Testing Library utilities unless a specific external async source requires it. Most render and interaction helpers already handle the common cases.

## Mocking Strategy

Mock less of the UI tree and more of the true boundary. Real providers, routers, query clients, stores, and child components tend to reveal integration problems that shallow tests miss. Network is often the right boundary for UI tests: use MSW-style request handlers where the project has them so the component exercises realistic loading, success, and error behavior without hitting real services.

Module mocks are still appropriate for hard boundaries such as browser APIs, time, randomness, analytics, auth SDKs, feature flags, or expensive platform integrations. Keep those mocks narrow and named after the user scenario they support.

## Avoid Hasty Abstractions (AHA)

Avoid Hasty Abstractions applies to tests too. A helper is useful when it removes incidental setup and makes the scenario easier to read. It is harmful when it hides the behavior under test, couples unrelated tests, or requires readers to jump through multiple files to understand a single assertion.

Prefer small, local setup functions that return useful handles. Allow options for meaningful scenario differences, not every possible prop. If a helper starts encoding a second testing language, inline the workflow or split it into clearer helpers.

## Test Structure

Keep tests flat enough that the scenario is visible. Deep `describe` nesting and shared mutable `beforeEach` setup make tests harder to understand and easier to accidentally couple. It is usually clearer for each test to arrange its own important data, even if that repeats a few lines.

Name tests after behavior: "shows a validation message when the email is invalid" is more useful than "calls validateEmail" or "renders error state".

## Snapshots

Snapshots are most useful when they are small, intentional, and easy to review, such as serialized config, generated markup for a tiny presentational primitive, or an error message with exact formatting. Large UI snapshots are difficult to review and often become update-by-reflex files. For behavior, prefer explicit assertions.

## Existing Projects

When adding tests to a project with little coverage, begin with the most valuable use cases instead of trying to retrofit exhaustive coverage. Add tests around bugs being fixed, critical flows, and areas likely to change. Make it easy for future work to copy a good pattern: one clear render helper, realistic providers, and readable tests are more valuable than a broad but brittle suite.
