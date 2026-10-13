---
title: "React Router: Complete Guide"
source: https://www.geekyfrontend.in/learn/react-interview-kit/react-router-complete-guide
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# React Router: Complete Guide

*6 min read · Updated 25 Apr 2026 Topic 17 of 62 Bookmark Mark done 3 · Topic 17 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/react-router-complete-guide](https://www.geekyfrontend.in/learn/react-interview-kit/react-router-complete-guide)

---

# 3.1 - React Router: Complete Guide

---

## 🎯 What Interviewers Actually Ask

| Question | Level | Frequency |
| --- | --- | --- |
| "What is React Router? Why do we need it?" | 🟢 | ★★★★★ |
| "What is the difference between BrowserRouter and HashRouter?" | 🟡 | ★★★★☆ |
| "How do you get URL parameters in React?" | 🟢🟡 | ★★★★★ |
| "How do you navigate programmatically?" | 🟡 | ★★★★☆ |
| "What is the difference between Link and NavLink?" | 🟢 | ★★★☆☆ |
| "How do you handle 404 pages?" | 🟢 | ★★★☆☆ |
| "What are nested routes?" | 🟡 | ★★★☆☆ |

---

## Why React Router Exists

React is a single-page application (SPA) framework. When the user clicks a navigation link, you don't want the browser to reload the entire page from the server. You want to swap out components while keeping the URL in sync. React Router handles this mapping between URLs and components.

Without React Router, you'd have to manually listen to `window.location`, parse the URL yourself, and conditionally render components based on it. React Router does all of this with a clean, declarative API.

---

## Basic Setup (React Router v6)

React Router v6 is the current standard. If you see `Switch`, `component={}`, or `exact` in code, that's v5 syntax. Interviewers may ask about both, but always default to v6 in new code.

jsx

```
import { BrowserRouter, Routes, Route, Link } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/contact">Contact</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}
```

The `path="*"` route is a catch-all for 404 pages. It matches any URL that doesn't match the routes above it.

---

## BrowserRouter vs HashRouter

|  | BrowserRouter | HashRouter |
| --- | --- | --- |
| **URL format** | `/about` | `/#/about` |
| **Requires server config?** | Yes (all routes must return index.html) | No |
| **Looks cleaner?** | Yes | No (has `#` in URL) |
| **SEO friendly?** | Better | Worse |
| **Common use** | Most modern apps | Apps hosted on static file servers without server config |

Use `BrowserRouter` unless you're deploying to a static host that doesn't support URL rewrites (like GitHub Pages without configuration).

---

## Core Hooks

### useParams: Reading URL Parameters

jsx

```
// Route definition
<Route path="/users/:userId" element={<UserProfile />} />

// Component
function UserProfile() {
  const { userId } = useParams();
  return <h1>User ID: {userId}</h1>;
}

// URL: /users/42  -->  userId = "42"
```

Parameters are always strings. If you need a number, convert it: `Number(userId)`.

### useNavigate: Programmatic Navigation

jsx

```
function LoginForm() {
  const navigate = useNavigate();

  const handleSubmit = async (e) => {
    e.preventDefault();
    const success = await login(credentials);

    if (success) {
      navigate('/dashboard');           // Go to dashboard
      // navigate('/dashboard', { replace: true });  // Replace current history entry
      // navigate(-1);                  // Go back one page
    }
  };

  return <form onSubmit={handleSubmit}>...</form>;
}
```

### useSearchParams: Query String Parameters

jsx

```
// URL: /products?category=shoes&sort=price

function ProductList() {
  const [searchParams, setSearchParams] = useSearchParams();

  const category = searchParams.get('category');  // "shoes"
  const sort = searchParams.get('sort');          // "price"

  const updateSort = (newSort) => {
    setSearchParams({ category, sort: newSort });
  };

  return (
    <div>
      <p>Category: {category}, Sort: {sort}</p>
      <button onClick={() => updateSort('name')}>Sort by Name</button>
    </div>
  );
}
```

### useLocation: Current Location Object

jsx

```
function Breadcrumb() {
  const location = useLocation();
  // location.pathname = "/users/42"
  // location.search = "?tab=posts"
  // location.state = { from: 'dashboard' }  (passed via navigate)

  return <p>You are at: {location.pathname}</p>;
}
```

---

## Nested Routes and Outlet

Nested routes let you build layouts where some parts of the page change and other parts stay constant.

jsx

```
function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Layout />}>
          <Route index element={<Home />} />
          <Route path="about" element={<About />} />
          <Route path="users" element={<Users />}>
            <Route path=":userId" element={<UserProfile />} />
          </Route>
        </Route>
      </Routes>
    </BrowserRouter>
  );
}

function Layout() {
  return (
    <div>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/users">Users</Link>
      </nav>

      {/* Child routes render here */}
      <Outlet />

      <footer>Footer stays on every page</footer>
    </div>
  );
}
```

`<Outlet />` is the placeholder where child route components render. The Layout component wraps all pages, so the nav and footer persist while the main content swaps.

---

## Link vs NavLink

`Link` is for basic navigation. `NavLink` is for navigation links that need to show an "active" state.

jsx

```
// Link: simple navigation
<Link to="/about">About</Link>

// NavLink: adds "active" class automatically when the route matches
<NavLink
  to="/about"
  className={({ isActive }) => isActive ? 'nav-link active' : 'nav-link'}
>
  About
</NavLink>
```

Use NavLink for navigation menus and sidebars where highlighting the current page matters.

---

## v5 vs v6 Quick Reference

Interviewers sometimes ask about migration or show v5 code and ask you to update it.

| Feature | v5 | v6 |
| --- | --- | --- |
| Route component | `<Route component={Home} />` | `<Route element={<Home />} />` |
| Wrapping routes | `<Switch>` | `<Routes>` |
| Exact match | `<Route exact path="/">` | Exact by default |
| Redirect | `<Redirect to="/login" />` | `<Navigate to="/login" replace />` |
| Access params | `props.match.params.id` | `useParams()` |
| Programmatic nav | `useHistory().push('/path')` | `useNavigate()('/path')` |

---

## ⚡ Common Mistakes

**Mistake 1: Putting Routes outside BrowserRouter.** Everything that uses React Router hooks or components must be inside a `<BrowserRouter>`.

**Mistake 2: Using** `<a href>` **instead of** `<Link to>`**.** An `<a>` tag causes a full page reload. `<Link>` performs client-side navigation without reloading.

**Mistake 3: Forgetting the catch-all route.** Without `path="*"`, unmatched URLs show a blank page instead of a 404 component.

**Mistake 4: Not handling trailing slashes.** `/about` and `/about/` can be different routes. Be consistent.

---

## 🔥 Output Question

jsx

```
<Routes>
  <Route path="/users" element={<Users />} />
  <Route path="/users/:id" element={<UserDetail />} />
  <Route path="*" element={<NotFound />} />
</Routes>
```

**What renders at URL** `/users/42`**?**

`<UserDetail />` renders. React Router v6 picks the most specific match. `/users/:id` is more specific than `/users` for the URL `/users/42`. No `exact` keyword needed in v6.

---

## 🗣️ Interview Answer

> "React Router is a client-side routing library for React SPAs. It maps URLs to components without full page reloads. In v6, you define routes with `<Routes>` and `<Route>`, use `useParams` for URL parameters, `useNavigate` for programmatic navigation, and `useSearchParams` for query strings. Nested routes with `<Outlet>` let you build persistent layouts. BrowserRouter uses clean URLs and needs server-side URL rewriting, while HashRouter uses the hash fragment and works on any static host."

---

## ✅ Key Takeaways

- React Router maps URLs to components for SPA navigation.
- **BrowserRouter** for clean URLs (needs server config). **HashRouter** for static hosts.
- `<Link>` for navigation (not `<a>`). `<NavLink>` for active-state styling.
- `useParams` for URL params, `useNavigate` for programmatic nav, `useSearchParams` for query strings.
- `<Outlet />` renders child routes inside a layout component.
- `path="*"` catches unmatched URLs for 404 pages.
- v6 uses `element={}` (not `component={}`), `<Routes>` (not `<Switch>`), and matching is exact by default.

---

## Learn More

- [React Router — Routing reactrouter.com](https://reactrouter.com/start/library/routing)
- [React Router — home & docs reactrouter.com](https://reactrouter.com)
- [React Router v6 in 45 minutes YouTube](https://www.youtube.com/watch?v=Ul3y1LXxzdU)
- [A complete guide to routing in React YouTube](https://www.youtube.com/watch?v=OspIifDD0pk)
- [React Router 6 for beginners YouTube](https://www.youtube.com/watch?v=59IXY5IDrBA)
