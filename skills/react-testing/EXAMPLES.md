# React Testing Examples

## Form Validation

Uses a local `setup()` with `render()` inside. `describe` groups conditions. Test names follow `"should <result> - for <action>"`. Callback assertions (like `onSubmit`) are valid here because the form's public contract is the callback — the user-visible outcome is what data was submitted.

```tsx
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { LoginForm } from './LoginForm'
// assuming Vitest globals; otherwise import { vi } from 'vitest'

function setup() {
  const user = userEvent.setup()
  const onSubmit = vi.fn()
  render(<LoginForm onSubmit={onSubmit} />)
  return { user, onSubmit }
}

describe('LoginForm', () => {
  describe('when email is missing', () => {
    test('should show validation error - for submit with empty form', async () => {
      const { user } = setup()

      await user.click(screen.getByRole('button', { name: /log in/i }))

      expect(screen.getByText(/email is required/i)).toBeVisible()
    })
  })

  describe('when credentials are valid', () => {
    test('should call onSubmit with form data - for valid submission', async () => {
      const { user, onSubmit } = setup()

      await user.type(screen.getByLabelText(/email/i), 'user@example.com')
      await user.type(screen.getByLabelText(/password/i), 'secret123')
      await user.click(screen.getByRole('button', { name: /log in/i }))

      expect(onSubmit).toHaveBeenCalledWith({
        email: 'user@example.com',
        password: 'secret123',
      })
    })
  })
})
```

## Async States with MSW

MSW handlers are declared locally with `server.use()` inside the test or `beforeEach`. The shared `server` instance is defined once per project (see `references/msw-mocking.md`). `render()` stays inside `setup()`. Loading checkpoint helpers decouple "wait for load" from "assert result".

```tsx
import { render, screen, waitForElementToBeRemoved } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { http, HttpResponse } from 'msw'
import { server } from '@/mocks/server'
import { UserList } from './UserList'

function setup() {
  render(<UserList />)
  return {
    waitForLoadToFinish: () =>
      waitForElementToBeRemoved(() => screen.getByText(/loading/i)),
  }
}

describe('UserList', () => {
  describe('when API returns users', () => {
    beforeEach(() => {
      server.use(
        http.get('/api/users', () =>
          HttpResponse.json([{ id: 1, name: 'Alice' }]),
        ),
      )
    })

    test('should display user names - after data loads', async () => {
      const { waitForLoadToFinish } = setup()

      await waitForLoadToFinish()

      expect(screen.getByText('Alice')).toBeVisible()
    })
  })

  describe('when API returns an empty list', () => {
    test('should show empty state - for no users', async () => {
      server.use(http.get('/api/users', () => HttpResponse.json([])))
      const { waitForLoadToFinish } = setup()

      await waitForLoadToFinish()

      expect(screen.getByText(/no users found/i)).toBeVisible()
    })
  })

  describe('when the API fails', () => {
    test('should show error message - on network failure', async () => {
      server.use(http.get('/api/users', () => HttpResponse.error()))
      const { waitForLoadToFinish } = setup()

      await waitForLoadToFinish()

      expect(screen.getByText(/failed to load/i)).toBeVisible()
    })
  })
})
```

## Routing and Navigation

Providers (router, query client, store) wrap `render()` inside `setup()`. Accept route overrides so each test controls its starting state.

```tsx
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { MemoryRouter } from 'react-router'
import { AppRoutes } from './AppRoutes'

function setup(route = '/') {
  const user = userEvent.setup()
  render(
    <MemoryRouter initialEntries={[route]}>
      <AppRoutes />
    </MemoryRouter>,
  )
  return { user }
}

describe('App navigation', () => {
  describe('when on the home page', () => {
    test('should navigate to settings - for click on settings link', async () => {
      const { user } = setup('/')

      await user.click(screen.getByRole('link', { name: /settings/i }))

      expect(
        screen.getByRole('heading', { name: /settings/i }),
      ).toBeVisible()
    })
  })
})
```

## Multiple Providers (Query Client + Router)

When components need multiple providers, create a local render helper inside the `setup()` or as a shared utility. The key is `render()` is never inline — it's always inside the function that returns test handles.

```tsx
import { render, screen, waitForElementToBeRemoved } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import { MemoryRouter } from 'react-router'
import { Dashboard } from './Dashboard'

function setup(route = '/dashboard') {
  const user = userEvent.setup()
  const queryClient = new QueryClient({
    defaultOptions: { queries: { retry: false } },
  })

  function Wrapper({ children }: { children: React.ReactNode }) {
    return (
      <QueryClientProvider client={queryClient}>
        <MemoryRouter initialEntries={[route]}>
          {children}
        </MemoryRouter>
      </QueryClientProvider>
    )
  }

  render(<Dashboard />, { wrapper: Wrapper })
  return {
    user,
    queryClient,
    waitForLoadToFinish: () =>
      waitForElementToBeRemoved(() => screen.getByText(/loading/i)),
  }
}

describe('Dashboard', () => {
  describe('when user navigates to dashboard', () => {
    test('should show welcome message - for authenticated user', async () => {
      const { waitForLoadToFinish } = setup()

      await waitForLoadToFinish()

      expect(screen.getByText(/welcome/i)).toBeVisible()
    })
  })
})
```

## Loading Checkpoints

Asserting intermediate loading states makes tests more robust. Return helpers from `setup()` to decouple the loading assertion from the result assertion.

```tsx
import { render, screen } from '@testing-library/react'
import { waitForElementToBeRemoved } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { http, HttpResponse } from 'msw'
import { server } from '@/mocks/server'
import { ProfilePage } from './ProfilePage'

function setup() {
  const user = userEvent.setup()
  render(<ProfilePage />)
  return {
    user,
    waitForLoadToFinish: () =>
      waitForElementToBeRemoved(() => screen.getByText(/loading/i)),
  }
}

describe('ProfilePage', () => {
  describe('when profile loads successfully', () => {
    test('should display user name - after loading completes', async () => {
      server.use(
        http.get('/api/profile', () =>
          HttpResponse.json({ name: 'Fulano de Tal' }),
        ),
      )
      const { waitForLoadToFinish } = setup()

      await waitForLoadToFinish()

      expect(screen.getByText('Fulano de Tal')).toBeVisible()
    })
  })

  describe('while profile is loading', () => {
    test('should show spinner - before data arrives', async () => {
      server.use(
        http.get('/api/profile', () =>
          HttpResponse.json({ name: 'Fulano de Tal' }),
        ),
      )
      setup()

      // Loading state is visible immediately before MSW responds
      expect(screen.getByText(/loading/i)).toBeVisible()
    })
  })
})
```
