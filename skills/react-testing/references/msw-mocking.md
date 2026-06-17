# MSW Server Mocking

How to use Mock Service Worker to test your application's reaction to server responses. Never mimic server behavior. Prefer asserting user-visible outcomes over request shape. Assert request payloads only when the request body is the meaningful contract — data-writing flows, form serialization, destructive actions, or when no visible outcome proves the data was sent correctly.

---

## Philosophy: Test Against Responses, Not Requests

### The Core Rule

**You know what response the server should return for a given action. Assume the server delivers it. Test how your application behaves against that response.**

This is the opposite of asserting that a request was made with the right payload. The server is a black box — your application doesn't control it, it only reacts to it. Your tests should reflect that reality.

### What This Means in Practice

| Do this | Don't do this |
|---|---|
| Declare: "When `GET /users` returns `[{name: 'Alice'}]`, the page shows Alice" | Assert: "The page called `GET /users` with the correct query params" |
| Declare: "When `POST /login` returns 401, the form shows 'Invalid credentials'" | Assert: "The login function sent `{email, password}` in the request body" |
| Declare the server response, then assert the UI outcome | Spy on `fetch`, assert request shape |

### Why

Request assertions are implementation details. They break when you:
- Change the HTTP client library
- Add/remove query parameters
- Refactor the data-fetching layer
- Switch from REST to GraphQL

Response-driven tests survive all of these because you only care about what the user sees after the server responds.

---

## Server Setup

### Single Server Instance (Recommended)

Create one server instance that all tests share. Use lifecycle hooks to start/stop it and `server.resetHandlers()` to isolate tests.

```ts
// src/mocks/server.ts
import { setupServer } from 'msw/node'
import { http, HttpResponse } from 'msw'

// Initial handlers act as fallback behavior for all tests.
// Define common, non-controversial defaults here.
// Individual tests override specific endpoints with server.use().
const handlers = [
  http.get('/api/health', () => HttpResponse.json({ status: 'ok' })),
]

export const server = setupServer(...handlers)
```

### Test Setup File

```ts
// src/test/setup.ts (or vitest.setup.ts / jest.setup.ts)
import { server } from '@/mocks/server'

beforeAll(() => server.listen({ onUnhandledRequest: 'error' }))
afterEach(() => server.resetHandlers())
afterAll(() => server.close())
```

Key choices:
- `onUnhandledRequest: 'error'` — **never leave unmatched requests.** Any request your component makes that isn't handled will fail the test immediately. This forces you to explicitly declare every network call.
- `server.resetHandlers()` in `afterEach` — removes any runtime handlers added via `server.use()`, ensuring test isolation.
- `beforeAll` / `afterAll` — start the server once, close it once. Don't restart per test.

---

## Declaring Handlers: Use `server.use()` Locally

### The Pattern

Declare handlers **inside the test** (or inside a local `setup()` function). Never define handlers at the module level that change between tests — it creates hidden coupling.

```tsx
import { http, HttpResponse } from 'msw'
import { server } from '@/mocks/server'

test('shows user list after successful fetch', async () => {
  // Declare the network behavior for THIS test
  server.use(
    http.get('/api/users', () =>
      HttpResponse.json([
        { id: 1, name: 'Alice' },
        { id: 2, name: 'Bob' },
      ]),
    ),
  )

  render(<UserList />)

  expect(await screen.findByText('Alice')).toBeVisible()
  expect(screen.getByText('Bob')).toBeVisible()
})
```

### Why Local Declarations

- **Self-contained**: Reading a test tells you everything about its network state. No need to hunt through shared files.
- **No cross-test pollution**: `server.resetHandlers()` in `afterEach` cleans them up, but declaring locally makes the intent explicit.
- **Easy to modify**: Adding a new scenario means adding one test with its own handler. No risk of breaking other tests.

### Grouping Handlers in a `describe` Block

When multiple tests share the same handler, declare it in a `beforeEach` inside the `describe`:

```tsx
describe('when the user API returns data', () => {
  beforeEach(() => {
    server.use(
      http.get('/api/users', () =>
        HttpResponse.json([
          { id: 1, name: 'Alice' },
          { id: 2, name: 'Bob' },
        ]),
      ),
    )
  })

  test('should display all user names', async () => {
    render(<UserList />)
    expect(await screen.findByText('Alice')).toBeVisible()
    expect(screen.getByText('Bob')).toBeVisible()
  })

  test('should show user count', async () => {
    render(<UserList />)
    expect(await screen.findByText('2 users')).toBeVisible()
  })
})
```

---

## Happy Path: Valid Responses

Test what the application *does* with the data it receives.

```tsx
test('renders product details from API response', async () => {
  server.use(
    http.get('/api/products/42', () =>
      HttpResponse.json({
        id: 42,
        name: 'Ergonomic Keyboard',
        price: 12990,  // cents
        inStock: true,
      }),
    ),
  )

  render(<ProductPage productId={42} />)

  expect(await screen.findByRole('heading', { name: 'Ergonomic Keyboard' })).toBeVisible()
  expect(screen.getByText('$129.90')).toBeVisible()
  expect(screen.getByText(/in stock/i)).toBeVisible()
})
```

### Dynamic Responses

When the response depends on the request, use the request object in the resolver:

```tsx
test('returns specific user by ID', async () => {
  server.use(
    http.get('/api/users/:id', ({ params }) => {
      const { id } = params
      return HttpResponse.json({ id, name: `User ${id}` })
    }),
  )

  render(<UserProfile userId="5" />)

  expect(await screen.findByText('User 5')).toBeVisible()
})
```

---

## Error Path: Invalid and Error Responses

### HTTP Error Status

```tsx
test('shows error message when API returns 500', async () => {
  server.use(
    http.get('/api/users', () =>
      HttpResponse.json(
        { error: 'Internal server error' },
        { status: 500 },
      ),
    ),
  )

  render(<UserList />)

  expect(await screen.findByText(/something went wrong/i)).toBeVisible()
})
```

### Network Error

When the server is unreachable (no response at all):

```tsx
test('shows connection error when network fails', async () => {
  server.use(
    http.get('/api/users', () => HttpResponse.error()),
  )

  render(<UserList />)

  expect(await screen.findByText(/connection error/i)).toBeVisible()
})
```

### 401 Unauthorized

```tsx
test('redirects to login when session expires', async () => {
  server.use(
    http.get('/api/profile', () =>
      HttpResponse.json({ error: 'Unauthorized' }, { status: 401 }),
    ),
  )

  render(<ProfilePage />)

  expect(await screen.findByText(/please log in/i)).toBeVisible()
})
```

### Empty Response

```tsx
test('shows empty state when API returns no data', async () => {
  server.use(
    http.get('/api/users', () => HttpResponse.json([])),
  )

  render(<UserList />)

  expect(await screen.findByText(/no users found/i)).toBeVisible()
})
```

---

## The Golden Rule: Never Leave Unmatched Requests

### Why

An unmatched request means your component made a network call you didn't account for. This is a bug — either:
1. The component is calling an endpoint it shouldn't.
2. You forgot to declare a handler for an endpoint the component needs.

In both cases, the test should fail loudly.

### Configuration

Set this in your test setup file:

```ts
server.listen({ onUnhandledRequest: 'error' })
```

This throws an error immediately when any request goes unhandled. The error message includes the full request URL, method, and headers so you can quickly identify what's missing.

### What to Do When You Get an Unhandled Request Error

1. Read the error message to see which request was unmatched.
2. Add a handler for that endpoint:
   - If it's relevant to the test, declare it with `server.use()`.
   - If it's a global dependency (e.g., feature flags, auth check), add it to the initial handlers in `server.ts`.
3. Do NOT set `onUnhandledRequest: 'warn'` or `'bypass'` as a permanent solution. That hides bugs.

### Common Unhandled Request Scenarios

| Request | Solution |
|---|---|
| `/api/feature-flags` loaded by a provider | Add to initial handlers in `server.ts` |
| `/api/auth/session` checked on every page | Add to initial handlers |
| Favicon, fonts, static assets | MSW ignores these by default — if you see them, check your `isCommonAssetRequest` config |
| Third-party analytics | Add a handler that returns 200 (or use `passthrough` if you must) |

---

## Anti-patterns: What Never to Do

### Prefer Not to Assert on the Request

Asserting on request payloads couples tests to the transport. Prefer asserting user-visible outcomes instead. However, assert request payloads when the request body *is* the meaningful contract: checkout payloads, destructive actions, form serialization where the user-visible outcome doesn't prove the data was sent correctly.

```tsx
// WRONG — implementation detail
test('sends correct payload', async () => {
  const spy = vi.fn()
  server.use(
    http.post('/api/login', async ({ request }) => {
      const body = await request.json()
      spy(body)  // DON'T DO THIS
      return HttpResponse.json({ token: 'abc' })
    }),
  )

  render(<LoginForm />)
  await user.type(screen.getByLabelText(/email/i), 'a@b.com')
  await user.click(screen.getByRole('button', { name: /log in/i }))

  expect(spy).toHaveBeenCalledWith({ email: 'a@b.com', password: 'secret123' })
})
```

**Why it's riskier**: If you refactor the login flow to use a different HTTP client or endpoint, this test fails even though the user experience is identical. Reserve request assertions for cases where the payload is the contract.

**Do this instead**:

```tsx
// RIGHT — behavior-driven
test('logs in and shows dashboard', async () => {
  server.use(
    http.post('/api/login', () =>
      HttpResponse.json({ token: 'abc', user: { name: 'Alice' } }),
    ),
  )

  render(<LoginForm />)
  await user.type(screen.getByLabelText(/email/i), 'a@b.com')
  await user.click(screen.getByRole('button', { name: /log in/i }))

  // Assert what the user sees: they're now on the dashboard
  expect(await screen.findByText(/welcome, alice/i)).toBeVisible()
})
```

### Never: Mimic Server Business Logic

```tsx
// WRONG — mimicking server validation
server.use(
  http.post('/api/login', async ({ request }) => {
    const body = await request.json()
    // DON'T reimplement server-side validation
    if (!body.email.includes('@')) {
      return HttpResponse.json({ error: 'Invalid email' }, { status: 400 })
    }
    if (body.password.length < 8) {
      return HttpResponse.json({ error: 'Password too short' }, { status: 400 })
    }
    return HttpResponse.json({ token: 'abc' })
  }),
)
```

**Why it's wrong**: You're reimplementing the server. Now you have two codebases to keep in sync. When the server changes its validation rules, your tests silently diverge from reality.

**Do this instead**: Declare the exact response you want the test to exercise:

```tsx
// Test 1: Invalid email scenario
test('shows error for invalid email', async () => {
  server.use(
    http.post('/api/login', () =>
      HttpResponse.json({ error: 'Invalid email format' }, { status: 400 }),
    ),
  )
  // ... assert the error message appears
})

// Test 2: Valid credentials scenario
test('logs in successfully', async () => {
  server.use(
    http.post('/api/login', () =>
      HttpResponse.json({ token: 'abc' }),
    ),
  )
  // ... assert the user is logged in
})
```

Each test declares the server response it needs. No server logic duplicated.

### Never: Shared Mutable Handler State

```tsx
// WRONG — shared mutable state between tests
let shouldFail = false

beforeEach(() => {
  shouldFail = false  // easy to forget to reset
  server.use(
    http.get('/api/data', () => {
      if (shouldFail) return HttpResponse.error()
      return HttpResponse.json({ data: 'ok' })
    }),
  )
})

test('handles success', () => { /* ... */ })

test('handles failure', () => {
  shouldFail = true  // mutates shared state — fragile
  // ...
})
```

**Do this instead**: Declare separate handlers per test:

```tsx
test('handles success', () => {
  server.use(http.get('/api/data', () => HttpResponse.json({ data: 'ok' })))
  // ...
})

test('handles failure', () => {
  server.use(http.get('/api/data', () => HttpResponse.error()))
  // ...
})
```

### Never: `onUnhandledRequest: 'bypass'` in Tests

```tsx
// WRONG — hides bugs
server.listen({ onUnhandledRequest: 'bypass' })
```

This silently ignores unknown requests. You'll never know your component is making calls you didn't handle. Always use `'error'` in test environments.

---

## Complete Example: A Data-Fetching Component

```tsx
// UserList.tsx — the component under test
function UserList() {
  const [users, setUsers] = useState<User[] | null>(null)
  const [error, setError] = useState<string | null>(null)

  useEffect(() => {
    fetch('/api/users')
      .then(res => {
        if (!res.ok) throw new Error('Failed')
        return res.json()
      })
      .then(setUsers)
      .catch(() => setError('Failed to load users'))
  }, [])

  if (error) return <div role="alert">{error}</div>
  if (!users) return <div>Loading...</div>
  if (users.length === 0) return <div>No users found</div>

  return (
    <ul>
      {users.map(u => <li key={u.id}>{u.name}</li>)}
    </ul>
  )
}
```

```tsx
// UserList.test.tsx
import { http, HttpResponse } from 'msw'
import { server } from '@/mocks/server'

describe('UserList', () => {
  describe('when the API returns users', () => {
    beforeEach(() => {
      server.use(
        http.get('/api/users', () =>
          HttpResponse.json([
            { id: 1, name: 'Alice' },
            { id: 2, name: 'Bob' },
          ]),
        ),
      )
    })

    test('should display user names', async () => {
      render(<UserList />)
      expect(await screen.findByText('Alice')).toBeVisible()
      expect(screen.getByText('Bob')).toBeVisible()
    })

    test('should show loading state before data arrives', async () => {
      render(<UserList />)
      expect(screen.getByText('Loading...')).toBeVisible()
      await screen.findByText('Alice')
    })
  })

  describe('when the API returns an empty list', () => {
    test('should show empty state', async () => {
      server.use(http.get('/api/users', () => HttpResponse.json([])))
      render(<UserList />)
      expect(await screen.findByText('No users found')).toBeVisible()
    })
  })

  describe('when the API fails', () => {
    test('should show error message on 500', async () => {
      server.use(
        http.get('/api/users', () =>
          HttpResponse.json(null, { status: 500 }),
        ),
      )
      render(<UserList />)
      expect(await screen.findByRole('alert')).toHaveTextContent('Failed to load users')
    })

    test('should show error message on network failure', async () => {
      server.use(http.get('/api/users', () => HttpResponse.error()))
      render(<UserList />)
      expect(await screen.findByRole('alert')).toHaveTextContent('Failed to load users')
    })
  })
})
```

---

## Quick Reference Checklist

**Setup:**
- [ ] One `server.ts` with initial handlers (common defaults)
- [ ] Test setup file with `beforeAll(server.listen)`, `afterEach(server.resetHandlers)`, `afterAll(server.close)`
- [ ] `server.listen({ onUnhandledRequest: 'error' })` — always

**Per test / describe:**
- [ ] Declare handlers with `server.use()` inside the test or `beforeEach`
- [ ] Declare only the endpoints this test needs
- [ ] Prefer asserting user-visible outcomes over request shape, method, or payload
- [ ] Never replicate server business logic in handlers
- [ ] Declare the response, then assert the UI outcome

**Response types to test:**
- [ ] Valid response → data renders correctly
- [ ] Empty response → empty state shown
- [ ] Error status (400, 401, 403, 500) → error state shown
- [ ] Network error → connection error shown
- [ ] Loading → loading indicator shown before data arrives
