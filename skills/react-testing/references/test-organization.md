# Test File Organization

Conventions for organizing test files, utilities, mocks, and fixtures in a React project.

---

## Recommended: Co-location

Place each test file next to the module it tests. This is the preferred pattern for component and unit tests.

### Why

- **Discoverability**: Open `Button.tsx`, find `Button.test.tsx` immediately next to it.
- **Proximity**: Imports stay short (`import { Button } from './Button'`). No deep relative paths.
- **Colocation signals coupling**: When a test file lives next to its source, it's clear they move together during refactors.
- **Dead code visible**: If `Button.tsx` is deleted, the adjacent `Button.test.tsx` is an obvious orphan — not buried in a distant `tests/` folder.

### Structure

```
src/
├── components/
│   ├── Button/
│   │   ├── Button.tsx
│   │   ├── Button.test.tsx
│   │   ├── Button.module.css
│   │   └── index.ts
│   ├── LoginForm/
│   │   ├── LoginForm.tsx
│   │   ├── LoginForm.test.tsx
│   │   └── LoginForm.module.css
│   └── Dashboard/
│       ├── Dashboard.tsx
│       ├── Dashboard.test.tsx
│       ├── DashboardSkeleton.tsx
│       └── DashboardSkeleton.test.tsx
├── hooks/
│   ├── useAuth.ts
│   └── useAuth.test.ts
├── utils/
│   ├── formatDate.ts
│   └── formatDate.test.ts
├── test-utils.tsx          # shared test utilities (see below)
└── mocks/
    ├── server.ts           # MSW server setup
    └── handlers/           # MSW request handlers
        ├── auth.ts
        └── users.ts
```

### Naming Convention

```
Module:  Button.tsx          →  Test:  Button.test.tsx
Module:  useAuth.ts          →  Test:  useAuth.test.ts
Module:  formatDate.ts       →  Test:  formatDate.test.ts
Module:  loginFlow.ts        →  Test:  loginFlow.test.ts
```

Use `.test.` (not `.spec.`) to match vitest/jest defaults. Be consistent across the project.

---

## Alternative: `__tests__` Directory

Place test files in a `__tests__` directory within the module folder. Useful when:

- The module has many test files (e.g., a complex form with separate validation, submission, and edge-case test files).
- The team has an existing convention or migration constraint.
- The source directory is already cluttered and you want visual separation.

```
src/
├── components/
│   ├── CheckoutForm/
│   │   ├── CheckoutForm.tsx
│   │   ├── CheckoutForm.module.css
│   │   └── __tests__/
│   │       ├── CheckoutForm.validation.test.tsx
│   │       ├── CheckoutForm.submission.test.tsx
│   │       └── CheckoutForm.edge-cases.test.tsx
```

Tools like Jest and Vitest pick up `__tests__` directories automatically.

### When NOT to use

Don't use `__tests__` when a module only needs one test file. It adds an unnecessary directory. Co-location is simpler.

---

## Alternative: Dedicated `tests/` Directory

A top-level or per-feature `tests/` directory. Appropriate for:

- **E2E tests** (Playwright, Cypress) — these test across the whole app and don't map to a single module.
- **Integration suites** that span multiple modules.
- **Contract tests** that verify API boundaries.

```
e2e/
├── auth.spec.ts
├── checkout.spec.ts
└── fixtures/
    └── users.json

src/
├── features/
│   └── billing/
│       ├── BillingPage.tsx
│       └── tests/
│           ├── BillingFlow.integration.test.tsx
│           └── BillingCalculations.unit.test.ts
```

### When NOT to use

Don't use a top-level `tests/` directory for unit or component tests. It divorces the test from the source, making refactors and deletions error-prone.

---

## Test Utilities Organization

### Single `test-utils.tsx` (Recommended)

Keep a single `src/test-utils.tsx` file with shared test infrastructure:

```tsx
// src/test-utils.tsx
import { render, RenderOptions } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import { MemoryRouter } from 'react-router'
import { ReactElement } from 'react'

function createTestQueryClient() {
  return new QueryClient({
    defaultOptions: {
      queries: { retry: false },
      mutations: { retry: false },
    },
  })
}

interface RenderWithProvidersOptions extends RenderOptions {
  route?: string
}

export function renderWithProviders(
  ui: ReactElement,
  options?: RenderWithProvidersOptions,
) {
  const queryClient = createTestQueryClient()
  const user = userEvent.setup()

  function Wrapper({ children }: { children: React.ReactNode }) {
    return (
      <QueryClientProvider client={queryClient}>
        <MemoryRouter initialEntries={[options?.route ?? '/']}>
          {children}
        </MemoryRouter>
      </QueryClientProvider>
    )
  }

  return {
    user,
    queryClient,
    ...render(ui, { wrapper: Wrapper, ...options }),
  }
}

// Re-export everything so tests only import from here
export { screen, waitFor, within } from '@testing-library/react'
export { userEvent }
```

Tests then import from a single place:

```tsx
import { renderWithProviders, screen, userEvent } from '@/test-utils'
```

### When `test-utils.tsx` Grows Too Large

Split into a `src/test/` directory:

```
src/test/
├── render.tsx         # renderWithProviders
├── server.ts          # MSW setup
├── fixtures/
│   ├── users.ts       # user factory functions
│   └── products.ts    # product factory functions
└── index.ts           # re-exports everything
```

The `index.ts` barrel file keeps imports clean:

```tsx
export { renderWithProviders } from './render'
export { server } from './server'
export * from './fixtures/users'
```

---

## Mock Organization

### Module Mocks (`__mocks__/`)

For mocking entire modules (e.g., libraries, SDKs):

```
src/
├── __mocks__/
│   ├── analytics.ts       # mock for analytics SDK
│   └── featureFlags.ts    # mock for feature flag service
```

Vitest/Jest automatically uses these when `vi.mock('@/analytics')` is called.

### MSW Handlers

Organize MSW handlers by domain:

```
src/mocks/
├── server.ts              # setupServer instance
├── handlers/
│   ├── auth.ts            # login, logout, session handlers
│   ├── users.ts           # GET /users, POST /users
│   └── products.ts        # GET /products, product CRUD
└── data/
    └── users.json         # static mock data (optional)
```

```tsx
// src/mocks/server.ts
import { setupServer } from 'msw/node'
import { handlers } from './handlers'

export const server = setupServer(...handlers)
```

```tsx
// src/mocks/handlers/index.ts
import { authHandlers } from './auth'
import { userHandlers } from './users'

export const handlers = [...authHandlers, ...userHandlers]
```

### Inline Test Mocks

For mocks specific to one test file, define them at the top of the test file:

```tsx
// Dashboard.test.tsx
vi.mock('@/hooks/useAnalytics', () => ({
  useAnalytics: () => ({ track: vi.fn() }),
}))
```

Don't scatter these across the codebase. Keep them in the test file that needs them.

---

## Fixtures and Test Data

### Factory Functions (Recommended)

Prefer factory functions over static JSON. They let each test customize only the fields it cares about:

```tsx
// src/test/fixtures/users.ts
export function createUser(overrides?: Partial<User>): User {
  return {
    id: 'user-1',
    name: 'Alice',
    email: 'alice@example.com',
    role: 'user',
    ...overrides,
  }
}

// Usage in test:
const admin = createUser({ role: 'admin' })
const inactive = createUser({ name: 'Bob', role: 'inactive' })
```

### Static Fixtures

Use static JSON when the data is large and rarely changes:

```
src/test/fixtures/
├── largeProductCatalog.json
└── countryList.json
```

### Where to Put Fixtures

| Scope | Location |
|---|---|
| Shared across project | `src/test/fixtures/` |
| Specific to one module | `./__fixtures__/` next to the module |
| MSW response data | `src/mocks/data/` |

---

## Anti-patterns

### Don't: Distant Test Directory

```
src/
└── components/
    └── Button.tsx
tests/
└── components/
    └── Button.test.tsx    # far from source, fragile on rename/delete
```

### Don't: Mixing Test Levels

```
src/
└── features/
    └── billing/
        └── tests/
            ├── BillingPage.test.tsx     # unit test
            ├── BillingFlow.spec.ts      # E2E test (wrong runner?)
            └── BillingAPI.contract.ts   # contract test
```

Keep E2E tests in a top-level `e2e/` directory. Keep unit and integration tests co-located.

### Don't: Inconsistent Extensions

```
Button.test.tsx
LoginForm.spec.tsx
Card.component.test.tsx
```

Pick one convention (`.test.tsx`) and apply it universally.

### Don't: Deep Fixture Nesting

```
src/
└── __fixtures__/
    └── users/
        └── admin/
            └── withPermissions/
                └── fullAccess.json
```

Prefer factory functions with overrides. No one will discover a fixture buried 5 folders deep.

---

## Migration Guide

### From `tests/` to Co-location

```
Before:
tests/components/Button.test.tsx
src/components/Button.tsx

After:
src/components/Button.test.tsx
src/components/Button.tsx
```

Steps:
1. Move file: `mv tests/components/Button.test.tsx src/components/Button.test.tsx`
2. Fix imports: `from '../src/components/Button'` → `from './Button'` (or `'@/components/Button'`)
3. Update any test config glob patterns
4. Run the suite to confirm

### From `__tests__/` to Co-location

```
Before:
src/components/Button/__tests__/Button.test.tsx

After:
src/components/Button/Button.test.tsx
```

Steps:
1. Move file up one level
2. Remove empty `__tests__/` directory
3. Verify imports still resolve

### Adding Tests to an Existing Project

Start with one clear pattern. Pick co-location, create `src/test-utils.tsx`, and write the first few tests. Let the pattern prove itself before migrating existing tests. A consistent partial migration is better than two competing conventions.

---

## Quick Reference

| What | Where |
|---|---|
| Component test | `Component.test.tsx` next to `Component.tsx` |
| Hook test | `useHook.test.ts` next to `useHook.ts` |
| Utility test | `util.test.ts` next to `util.ts` |
| Shared render helper | `src/test-utils.tsx` (single file) |
| MSW server | `src/mocks/server.ts` |
| MSW handlers | `src/mocks/handlers/` by domain |
| Shared fixtures | `src/test/fixtures/` (factory functions) |
| Module mocks | `src/__mocks__/` (auto-discovered) |
| E2E tests | `e2e/` (top-level, separate from unit tests) |
