---
title: "Code Splitting & Lazy Loading Routes"
source: https://www.geekyfrontend.in/learn/react-interview-kit/code-splitting-lazy-loading-routes
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Code Splitting & Lazy Loading Routes

*5 min read · Updated 24 Mar 2026 Topic 19 of 62 Bookmark Mark done 3 · Topic 19 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/code-splitting-lazy-loading-routes](https://www.geekyfrontend.in/learn/react-interview-kit/code-splitting-lazy-loading-routes)

---

# 3.3 - Code Splitting & Lazy Loading Routes

---

## 🎯 What Interviewers Actually Ask

| Question | Level | Frequency |
| --- | --- | --- |
| "What is code splitting?" | 🟡 | ★★★★☆ |
| "What is lazy loading in React?" | 🟡 | ★★★★☆ |
| "What is React.lazy and Suspense?" | 🟡 | ★★★★☆ |
| "How do you implement route-based code splitting?" | 🟡🔴 | ★★★☆☆ |
| "What is the difference between lazy loading and code splitting?" | 🟡 | ★★★☆☆ |

---

## The Problem: Bundle Size

When you build a React app, the bundler (Webpack, Vite) combines all your JavaScript into one or a few large files. For a small app, this is fine. For a large app with dozens of pages, the user downloads the code for every page upfront, even pages they may never visit.

**Code splitting** breaks your bundle into smaller chunks. **Lazy loading** loads those chunks only when they're needed (usually when the user navigates to that route).

They're related but distinct concepts. Code splitting is the technique of breaking the bundle apart. Lazy loading is the strategy of loading those pieces on demand.

---

## React.lazy and Suspense

`React.lazy` lets you define a component that is loaded dynamically. It takes a function that returns a dynamic `import()`.

`Suspense` wraps lazy components and shows a fallback UI while the component is loading.

jsx

```
import { lazy, Suspense } from 'react';

// Instead of: import Dashboard from './Dashboard';
const Dashboard = lazy(() => import('./Dashboard'));
const Settings = lazy(() => import('./Settings'));
const Analytics = lazy(() => import('./Analytics'));

function App() {
  return (
    <BrowserRouter>
      <Suspense fallback={<div>Loading...</div>}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/dashboard" element={<Dashboard />} />
          <Route path="/settings" element={<Settings />} />
          <Route path="/analytics" element={<Analytics />} />
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
}
```

When the user navigates to `/dashboard`, React downloads the Dashboard chunk, shows "Loading..." while it downloads, then renders the component. The Settings and Analytics code is never downloaded until the user visits those pages.

---

## Where to Place Suspense

You can wrap individual routes or the entire Routes block. Each approach has tradeoffs:

jsx

```
// Option A: Single Suspense around all routes
// Simpler, but shows the same loading state for every route transition
<Suspense fallback={<PageLoader />}>
  <Routes>
    <Route path="/dashboard" element={<Dashboard />} />
    <Route path="/settings" element={<Settings />} />
  </Routes>
</Suspense>

// Option B: Suspense per route
// More code, but each route can have its own loading UI
<Routes>
  <Route
    path="/dashboard"
    element={
      <Suspense fallback={<DashboardSkeleton />}>
        <Dashboard />
      </Suspense>
    }
  />
  <Route
    path="/settings"
    element={
      <Suspense fallback={<SettingsSkeleton />}>
        <Settings />
      </Suspense>
    }
  />
</Routes>
```

Option B with skeleton screens gives a much better user experience because each page shows a loading state that matches its layout.

---

## Combining with Protected Routes

Lazy loading and auth guards work together naturally:

jsx

```
const Dashboard = lazy(() => import('./pages/Dashboard'));
const AdminPanel = lazy(() => import('./pages/AdminPanel'));

function App() {
  return (
    <BrowserRouter>
      <AuthProvider>
        <Suspense fallback={<PageLoader />}>
          <Routes>
            <Route path="/login" element={<LoginPage />} />
            <Route
              path="/dashboard"
              element={
                <ProtectedRoute>
                  <Dashboard />
                </ProtectedRoute>
              }
            />
            <Route
              path="/admin"
              element={
                <RoleRoute allowedRoles={['admin']}>
                  <AdminPanel />
                </RoleRoute>
              }
            />
          </Routes>
        </Suspense>
      </AuthProvider>
    </BrowserRouter>
  );
}
```

The AdminPanel code is never even downloaded unless the user navigates to `/admin` AND passes the role check.

---

## Named Exports and Lazy

`React.lazy` only works with **default exports**. If a module uses named exports, you need a wrapper:

jsx

```
// ❌ Won't work with named export
const MyComponent = lazy(() => import('./MyComponent'));
// Error if MyComponent uses: export function MyComponent()

// ✅ Wrap named export in a default export re-assignment
const MyComponent = lazy(() =>
  import('./MyComponent').then(module => ({ default: module.MyComponent }))
);
```

---

## ⚡ Common Mistakes

**Mistake 1: Lazy loading everything.** Only lazy load routes (page-level components). Don't lazy load small, frequently used components like buttons or inputs. The overhead of loading them on demand is worse than including them in the main bundle.

**Mistake 2: Forgetting Suspense.** If a lazy component renders without a Suspense boundary above it, React throws an error. Always wrap lazy components in Suspense.

**Mistake 3: No error handling for failed loads.** If the network fails while loading a chunk, the user sees an uncaught error. Wrap the Suspense in an Error Boundary for production apps.

jsx

```
<ErrorBoundary fallback={<p>Failed to load page. Please refresh.</p>}>
  <Suspense fallback={<PageLoader />}>
    <Routes>...</Routes>
  </Suspense>
</ErrorBoundary>
```

---

## 🗣️ Interview Answer

> "Code splitting breaks the JavaScript bundle into smaller chunks. Lazy loading loads those chunks on demand. In React, I use `React.lazy` with dynamic `import()` to define components that load only when their route is visited. `Suspense` provides a fallback UI while the chunk downloads. I apply this at the route level, wrapping each page in a Suspense with a skeleton loader. For production, I combine it with Error Boundaries to handle network failures gracefully. The key rule is to only lazy load page-level components, not small reusable ones."

---

## ✅ Key Takeaways

- **Code splitting** breaks the bundle into smaller files. **Lazy loading** loads them on demand.
- `React.lazy(() => import('./Page'))` creates a dynamically loaded component.
- `Suspense` provides a fallback while the lazy component loads. Required for every lazy component.
- Apply lazy loading at the **route/page level**, not on small components.
- `React.lazy` only supports **default exports**. Wrap named exports if needed.
- Combine with **Error Boundaries** to handle chunk loading failures.
- Combine with **Protected Routes** so unauthorized users never download restricted code.

---

## 📚 Module 3 Complete!

| # | Article | Core Concept |
| --- | --- | --- |
| 3.1 | React Router | URL mapping, hooks, nested routes, v5 vs v6 |
| 3.2 | Protected Routes | Auth guards, role-based access, redirect flow |
| 3.3 | Code Splitting & Lazy Loading | React.lazy, Suspense, route-level splitting |

---

## Learn More

- [lazy — API reference react.dev](https://react.dev/reference/react/lazy)
- [Suspense — API reference react.dev](https://react.dev/reference/react/Suspense)
- [Lazy loading & code splitting with React Router YouTube](https://www.youtube.com/watch?v=LTrlE-f8nPI)
- [Code splitting in React — performance YouTube](https://www.youtube.com/watch?v=IBrmsyy9R94)
- [Route-based code splitting & lazy loading YouTube](https://www.youtube.com/watch?v=lkOWOYPCbxA)
