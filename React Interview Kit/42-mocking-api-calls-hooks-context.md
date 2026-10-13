---
title: "Mocking API Calls, Hooks & Context"
source: https://www.geekyfrontend.in/learn/react-interview-kit/mocking-api-calls-hooks-context
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Mocking API Calls, Hooks & Context

*4 min read · Updated 24 Mar 2026 Topic 42 of 62 Bookmark Mark done 9 · Topic 42 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/mocking-api-calls-hooks-context](https://www.geekyfrontend.in/learn/react-interview-kit/mocking-api-calls-hooks-context)

---

# 9.3 - Mocking API Calls, Hooks & Context

---

## 🎯 What Interviewers Actually Ask

| Question | Level | Frequency |
| --- | --- | --- |
| "How do you mock API calls in tests?" | 🟡🔴 | ★★★★☆ |
| "How do you test a component that uses Context?" | 🔴 | ★★★☆☆ |
| "How do you test custom hooks?" | 🔴 | ★★★☆☆ |

---

## Mocking API Calls with Jest

The simplest approach is to mock the `fetch` function globally.

jsx

jsx

```
beforeEach(() => {
  global.fetch = jest.fn();
});

afterEach(() => {
  jest.restoreAllMocks();
});

test('displays users from API', async () => {
  global.fetch.mockResolvedValueOnce({
    ok: true,
    json: async () => [
      { id: 1, name: 'Alice' },
      { id: 2, name: 'Bob' },
    ],
  });

  render(<UserList />);

  expect(await screen.findByText('Alice')).toBeInTheDocument();
  expect(screen.getByText('Bob')).toBeInTheDocument();
  expect(global.fetch).toHaveBeenCalledWith('/api/users');
});

test('displays error message on API failure', async () => {
  global.fetch.mockRejectedValueOnce(new Error('Network error'));

  render(<UserList />);

  expect(await screen.findByText(/error/i)).toBeInTheDocument();
});
```

### Mock Service Worker (MSW) for realistic mocking

MSW intercepts requests at the network level, making components behave exactly as in production.

jsx

jsx

```
import { rest } from 'msw';
import { setupServer } from 'msw/node';

const server = setupServer(
  rest.get('/api/users', (req, res, ctx) => {
    return res(ctx.json([
      { id: 1, name: 'Alice' },
      { id: 2, name: 'Bob' },
    ]));
  })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

test('loads users', async () => {
  render(<UserList />);
  expect(await screen.findByText('Alice')).toBeInTheDocument();
});

test('handles server error', async () => {
  server.use(
    rest.get('/api/users', (req, res, ctx) => {
      return res(ctx.status(500));
    })
  );

  render(<UserList />);
  expect(await screen.findByText(/error/i)).toBeInTheDocument();
});
```

---

## Testing Components with Context

Wrap the component in the Provider during the test, supplying the values you want.

jsx

jsx

```
function renderWithAuth(ui, { user = null } = {}) {
  return render(
    <AuthContext.Provider value={{ user, login: jest.fn(), logout: jest.fn() }}>
      {ui}
    </AuthContext.Provider>
  );
}

test('shows welcome message when logged in', () => {
  renderWithAuth(<Header />, { user: { name: 'Alice' } });
  expect(screen.getByText('Welcome, Alice')).toBeInTheDocument();
});

test('shows login button when not logged in', () => {
  renderWithAuth(<Header />);
  expect(screen.getByRole('button', { name: /login/i })).toBeInTheDocument();
});
```

Creating a custom `renderWith...` helper is the standard pattern. It keeps tests DRY and readable.

---

## Testing Custom Hooks

Use `renderHook` from `@testing-library/react` to test hooks outside of a component.

jsx

jsx

```
import { renderHook, act } from '@testing-library/react';
import { useCounter } from './useCounter';

test('increments the counter', () => {
  const { result } = renderHook(() => useCounter());

  expect(result.current.count).toBe(0);

  act(() => {
    result.current.increment();
  });

  expect(result.current.count).toBe(1);
});

test('starts with initial value', () => {
  const { result } = renderHook(() => useCounter(10));
  expect(result.current.count).toBe(10);
});
```

Wrap state-changing actions in `act()` to ensure React processes the update before your assertions run.

---

## What to Test (and What Not To)

| Test this ✅ | Skip this ❌ |
| --- | --- |
| User-facing behavior | Internal state values |
| Rendered output | Implementation details (which hook was called) |
| Form validation messages | CSS classes or styles (unless visually critical) |
| API loading/error/success states | Third-party library internals |
| Keyboard and mouse interactions | Whether a console.log fired |
| Conditional rendering | Snapshot of every single component |

---

## 🗣️ Interview Answer

> "For API calls, I mock fetch directly for simple cases or use MSW for more realistic network-level mocking. For components that depend on Context, I create custom render helpers that wrap the component in the necessary Providers. For custom hooks, I use renderHook from RTL. I prioritize testing the happy path, error states, and edge cases like empty lists and loading states."

---

## ✅ Key Takeaways for Module 9

- **Testing pyramid:** Many unit tests, fewer integration tests, very few E2E tests.
- **RTL philosophy:** Test what users see and do, not implementation details.
- **Query priority:** `getByRole` first, `getByTestId` last. Use `queryBy` for "not found" assertions, `findBy` for async.
- **userEvent** is more realistic than `fireEvent`.
- **Mock API calls** with `jest.fn()` on fetch, or MSW for network-level interception.
- **Test Context** by wrapping components in Providers inside custom render helpers.
- **Test hooks** with `renderHook` and wrap state changes in `act()`.
- **Snapshots** should be used sparingly, only for stable, static components.

---

## Learn More

- [Mock Service Worker (MSW) mswjs.io](https://mswjs.io)
- [React Testing Library — intro testing-library.com](https://testing-library.com/docs/react-testing-library/intro)
- [Mock API calls with MSW YouTube](https://www.youtube.com/watch?v=oMv2eAGWtZU)
- [Mock a REST API with Jest & RTL YouTube](https://www.youtube.com/watch?v=k0LPNKWCxx0)
- [Mocking API calls (Jest & MSW) YouTube](https://www.youtube.com/watch?v=P36kXz9ivMw)
