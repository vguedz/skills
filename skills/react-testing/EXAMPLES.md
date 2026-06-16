# React Testing Examples

## Form Validation

```tsx
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { LoginForm } from './LoginForm'

test('shows error when email is missing', async () => {
  const user = userEvent.setup()
  render(<LoginForm />)

  await user.click(screen.getByRole('button', { name: /log in/i }))

  expect(screen.getByText(/email is required/i)).toBeVisible()
})

test('submits with valid data', async () => {
  const user = userEvent.setup()
  const onSubmit = vi.fn()
  render(<LoginForm onSubmit={onSubmit} />)

  await user.type(screen.getByLabelText(/email/i), 'user@example.com')
  await user.type(screen.getByLabelText(/password/i), 'secret123')
  await user.click(screen.getByRole('button', { name: /log in/i }))

  expect(onSubmit).toHaveBeenCalledWith({
    email: 'user@example.com',
    password: 'secret123',
  })
})
```

## Async Loading & Error States (with MSW)

```tsx
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { http, HttpResponse } from 'msw'
import { setupServer } from 'msw/node'

const server = setupServer()

beforeAll(() => server.listen())
afterEach(() => server.resetHandlers())
afterAll(() => server.close())

test('shows loading then data', async () => {
  server.use(
    http.get('/api/users', () =>
      HttpResponse.json([{ id: 1, name: 'Alice' }]),
    ),
  )
  render(<UserList />)

  expect(screen.getByText(/loading/i)).toBeVisible()
  expect(await screen.findByText('Alice')).toBeVisible()
})

test('shows error on failure', async () => {
  server.use(
    http.get('/api/users', () => HttpResponse.error()),
  )
  render(<UserList />)

  expect(await screen.findByText(/failed to load/i)).toBeVisible()
})

test('shows empty state', async () => {
  server.use(
    http.get('/api/users', () => HttpResponse.json([])),
  )
  render(<UserList />)

  expect(await screen.findByText(/no users found/i)).toBeVisible()
})
```

## Routing & Navigation

```tsx
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { MemoryRouter } from 'react-router'
import { AppRoutes } from './AppRoutes'

test('navigates to settings page', async () => {
  const user = userEvent.setup()
  render(
    <MemoryRouter initialEntries={['/']}>
      <AppRoutes />
    </MemoryRouter>,
  )

  await user.click(screen.getByRole('link', { name: /settings/i }))

  expect(
    screen.getByRole('heading', { name: /settings/i }),
  ).toBeVisible()
})
```

## Optimistic Updates

```tsx
test('optimistically marks todo as done', async () => {
  render(
    <QueryClientProvider client={queryClient}>
      <TodoList />
    </QueryClientProvider>,
  )
  const user = userEvent.setup()

  await user.click(
    screen.getByRole('checkbox', { name: /buy milk/i }),
  )

  expect(
    screen.getByRole('checkbox', { name: /buy milk/i }),
  ).toBeChecked()
})
```

## Provider Wrappers (shared render helper)

```tsx
function renderWithProviders(
  ui: React.ReactElement,
  options?: { route?: string },
) {
  const queryClient = new QueryClient({
    defaultOptions: { queries: { retry: false } },
  })

  function Wrapper({ children }: { children: React.ReactNode }) {
    return (
      <QueryClientProvider client={queryClient}>
        <MemoryRouter initialEntries={[options?.route ?? '/']}>
          {children}
        </MemoryRouter>
      </QueryClientProvider>
    )
  }

  return { ...render(ui, { wrapper: Wrapper }), queryClient }
}

test('uses shared wrapper', async () => {
  renderWithProviders(<Dashboard />, { route: '/dashboard' })
  expect(await screen.findByText(/welcome/i)).toBeVisible()
})
```

## Async Utility Pattern

Extract reusable async workflows into helper functions that take the `user` instance. This keeps tests readable while avoiding deep abstraction.

```tsx
function setup() {
  const user = userEvent.setup()
  return { user }
}

async function submitForm(
  user: ReturnType<typeof userEvent.setup>,
) {
  await user.click(screen.getByRole('button', { name: /save/i }))
  await screen.findByText(/saved/i)
}

test('save triggers confirmation', async () => {
  const { user } = setup()
  render(<SettingsForm />)

  await user.type(screen.getByLabelText(/name/i), 'New Name')
  await submitForm(user)

  expect(screen.getByText(/changes saved/i)).toBeVisible()
})
```
