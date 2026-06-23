# Appearance and Disappearance

> Based on the [Testing Library guide](https://testing-library.com/docs/guide-disappearance/).

## Waiting for Appearance

### 1. `findBy*` Queries

```tsx
const movie = await screen.findByText('the lion king')
```

Always `await` — `findBy` returns a Promise that resolves when the element appears.

### 2. `waitFor` + assertion

```tsx
await waitFor(() => {
  expect(screen.getByText('the lion king')).toBeInTheDocument()
})
```

Use when you need to assert multiple things or the query is not a simple find.

## Waiting for Disappearance

### 1. `waitForElementToBeRemoved` (preferred)

```tsx
await waitForElementToBeRemoved(() => screen.queryByText('the mummy'))
```

Uses `MutationObserver` under the hood — more efficient than polling. Always pass a `queryBy*` callback (not `getBy*`), since `getBy` throws when the element isn't found.

### 2. `waitFor` + assertion

```tsx
await waitFor(() => {
  expect(screen.queryByText('i, robot')).not.toBeInTheDocument()
})
```

Fallback when `waitForElementToBeRemoved` doesn't fit. Uses polling.

## Asserting Elements Are Not Present

Use `queryBy*` (returns `null`) or `queryAllBy*` (returns array) to assert absence:

```tsx
const submitButton = screen.queryByText('submit')
expect(submitButton).toBeNull()

// or
const submitButtons = screen.queryAllByText('submit')
expect(submitButtons).toHaveLength(0)

// or (preferred — better error messages)
expect(screen.queryByText('submit')).not.toBeInTheDocument()
```

`not.toBeInTheDocument()` requires `@testing-library/jest-dom` and gives more meaningful diffs than `toBeNull()`.

## Key Differences

| Method | Detection | Use Case |
|--------|-----------|----------|
| `findBy*` | Polling | Wait for element to appear |
| `waitForElementToBeRemoved` | MutationObserver | Wait for element to be removed (efficient) |
| `waitFor` + assertion | Polling | Custom async assertions |
| `queryBy*` + assertion | Synchronous | Assert element is not present |
