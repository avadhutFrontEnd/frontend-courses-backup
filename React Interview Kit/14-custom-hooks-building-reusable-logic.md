---
title: "Custom Hooks: Building Reusable Logic"
source: https://www.geekyfrontend.in/learn/react-interview-kit/custom-hooks-building-reusable-logic
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Custom Hooks: Building Reusable Logic

*6 min read · Updated 24 Mar 2026 Topic 14 of 62 Bookmark Mark done 2 · Topic 14 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/custom-hooks-building-reusable-logic](https://www.geekyfrontend.in/learn/react-interview-kit/custom-hooks-building-reusable-logic)

---

# 2.4 - Custom Hooks: Building Reusable Logic

---

## 🎯 What Interviewers Actually Ask

| Question | Level | Frequency |
| --- | --- | --- |
| "What is a custom hook?" | 🟡 | ★★★★★ |
| "Build a useFetch / useDebounce / useLocalStorage hook" | 🟡🔴 | ★★★★★ |
| "What are the rules of hooks?" | 🟢🟡 | ★★★★☆ |
| "Why must custom hooks start with 'use'?" | 🟡 | ★★★☆☆ |
| "How is a custom hook different from a regular function?" | 🟡 | ★★★☆☆ |

---

## What Is a Custom Hook?

A custom hook is a JavaScript function whose name starts with `use` and that calls other hooks inside it. That's the entire definition. There's no special API, no registration, no magic. If a function starts with `use` and calls `useState`, `useEffect`, or any other hook, it's a custom hook.

The point is **reusability**. When you find the same useState + useEffect combination repeated across components, extract it into a custom hook.

---

## Rules of Hooks

These apply to all hooks, including custom ones:

1. **Only call hooks at the top level.** Not inside conditions, loops, or nested functions. React relies on call order to track which hook is which.
2. **Only call hooks from React functions.** Either from functional components or from other custom hooks. Not from regular JavaScript functions.
3. **Custom hooks must start with** `use`**.** This lets React's linter enforce the rules above.

jsx

```
// ❌ Hook inside a condition. Breaks hook ordering.
function Bad({ shouldFetch }) {
  if (shouldFetch) {
    const [data, setData] = useState(null);  // React can't track this
  }
}

// ✅ Always call hooks at the top. Use the condition inside.
function Good({ shouldFetch }) {
  const [data, setData] = useState(null);

  useEffect(() => {
    if (shouldFetch) {
      fetchData().then(setData);
    }
  }, [shouldFetch]);
}
```

---

## Five Custom Hooks Every React Developer Should Know

### 1. useLocalStorage

Syncs state with localStorage so it persists across page refreshes.

jsx

```
function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    try {
      const stored = localStorage.getItem(key);
      return stored !== null ? JSON.parse(stored) : initialValue;
    } catch {
      return initialValue;
    }
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
}

// Usage
function Settings() {
  const [theme, setTheme] = useLocalStorage('theme', 'light');
  return <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>{theme}</button>;
}
```

### 2. useDebounce

Delays updating a value until the user stops changing it. Essential for search inputs.

jsx

```
function useDebounce(value, delay = 500) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}

// Usage
function Search() {
  const [query, setQuery] = useState('');
  const debouncedQuery = useDebounce(query, 300);

  useEffect(() => {
    if (debouncedQuery) {
      searchAPI(debouncedQuery);
    }
  }, [debouncedQuery]);

  return <input value={query} onChange={e => setQuery(e.target.value)} />;
}
```

### 3. useFetch

Handles the loading/data/error lifecycle for API calls.

jsx

```
function useFetch(url) {
  const [state, setState] = useState({ data: null, loading: true, error: null });

  useEffect(() => {
    if (!url) return;
    let cancelled = false;

    setState({ data: null, loading: true, error: null });

    fetch(url)
      .then(res => {
        if (!res.ok) throw new Error(`HTTP ${res.status}`);
        return res.json();
      })
      .then(data => {
        if (!cancelled) setState({ data, loading: false, error: null });
      })
      .catch(error => {
        if (!cancelled) setState({ data: null, loading: false, error: error.message });
      });

    return () => { cancelled = true; };
  }, [url]);

  return state;
}

// Usage
function UserList() {
  const { data: users, loading, error } = useFetch('/api/users');

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}
```

### 4. useWindowSize

Tracks the browser window dimensions in real time.

jsx

```
function useWindowSize() {
  const [size, setSize] = useState({ width: window.innerWidth, height: window.innerHeight });

  useEffect(() => {
    const handleResize = () => setSize({ width: window.innerWidth, height: window.innerHeight });
    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);

  return size;
}

// Usage
function ResponsiveNav() {
  const { width } = useWindowSize();
  return width > 768 ? <DesktopNav /> : <MobileNav />;
}
```

### 5. useClickOutside

Detects clicks outside a referenced element. Used for closing dropdowns, modals, and popovers.

jsx

```
function useClickOutside(ref, callback) {
  useEffect(() => {
    const handleClick = (e) => {
      if (ref.current && !ref.current.contains(e.target)) {
        callback();
      }
    };

    document.addEventListener('mousedown', handleClick);
    return () => document.removeEventListener('mousedown', handleClick);
  }, [ref, callback]);
}

// Usage
function Dropdown() {
  const [open, setOpen] = useState(false);
  const ref = useRef(null);

  useClickOutside(ref, () => setOpen(false));

  return (
    <div ref={ref}>
      <button onClick={() => setOpen(!open)}>Menu</button>
      {open && <div className="dropdown-content">Options here</div>}
    </div>
  );
}
```

---

## How a Custom Hook Differs from a Regular Function

A regular utility function can do math, string manipulation, or data transformation. But it **cannot** call hooks. A custom hook can call `useState`, `useEffect`, `useRef`, and any other hook because React recognizes it as part of a component's lifecycle (via the `use` prefix).

jsx

```
// Regular function: no hooks allowed
function formatPrice(amount) {
  return `$${amount.toFixed(2)}`;
}

// Custom hook: can use hooks
function useFormattedPrice(amount) {
  const [formatted, setFormatted] = useState('');
  useEffect(() => {
    setFormatted(`$${amount.toFixed(2)}`);
  }, [amount]);
  return formatted;
}
// Note: this example is over-engineered. formatPrice as a regular function is better here.
// Only use a custom hook when you actually need hooks inside.
```

---

## ⚡ Common Mistakes

**Mistake 1: Making a custom hook for something that doesn't need hooks.** If your function doesn't call useState, useEffect, or any other hook, it's just a utility function. Name it without the `use` prefix.

**Mistake 2: Not handling cleanup.** If your hook sets up listeners, timers, or subscriptions, it must clean them up in the useEffect return function.

**Mistake 3: Breaking hook rules inside the custom hook.** The same rules apply: no hooks inside conditions or loops, even within a custom hook.

---

## 🗣️ Interview Answer

> "A custom hook is a function that starts with 'use' and calls other React hooks inside it. It lets you extract and reuse stateful logic across components without changing the component hierarchy. The most common ones I build are useFetch for API calls, useDebounce for search inputs, useLocalStorage for persisting state, and useClickOutside for dropdown menus. The key difference from a regular function is that a custom hook can call useState, useEffect, and other hooks, which a plain function cannot."

---

## ✅ Key Takeaways

- A custom hook is a function starting with `use` that calls other hooks.
- It extracts **reusable stateful logic**, not reusable UI (that's what components do).
- The same rules apply: top-level only, React functions only.
- Build hooks for repeated patterns: useFetch, useDebounce, useLocalStorage, useWindowSize, useClickOutside.
- Don't create a custom hook if you don't need hooks inside. Just use a regular function.
- Always handle **cleanup** for subscriptions, timers, and event listeners.

---

## Learn More

- [Reusing Logic with Custom Hooks react.dev](https://react.dev/learn/reusing-logic-with-custom-hooks)
- [Custom React hook in 12 minutes YouTube](https://www.youtube.com/watch?v=h2fh_pdISeI)
- [Making a custom hook YouTube](https://www.youtube.com/watch?v=Jl4q2cccwf0)
- [Custom hooks — deep dive YouTube](https://www.youtube.com/watch?v=l-s9MgoMwTI)
