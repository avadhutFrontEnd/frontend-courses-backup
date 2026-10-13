---
title: "Custom hooks and rules"
source: https://www.geekyfrontend.in/learn/complete-frontend-kit/custom-hooks-and-rules
course: Frontend System Design Kit
tags: [geeky-frontend, frontend-system-design-kit]
---

# Custom hooks and rules

*4 min read · Updated 19 May 2026 Topic 47 of 77 Bookmark Mark done 16D · Topic 47 of 77*

**Source:** [https://www.geekyfrontend.in/learn/complete-frontend-kit/custom-hooks-and-rules](https://www.geekyfrontend.in/learn/complete-frontend-kit/custom-hooks-and-rules)

---

# 16D. Custom Hooks & Rules

**Questions:** 2 **Difficulty:** Mid to Senior **Companies:** Every company

---

## Q 16.19 How do you build custom hooks? What are the patterns for composition and testing?

**Difficulty:** Mid **Asked at:** Every company

Custom hooks extract reusable logic from components. They're regular functions that call other hooks — the `use` prefix tells React to enforce hooks rules.

**Pattern 1: Encapsulating stateful logic**

jsx

```
function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    try {
      const stored = localStorage.getItem(key);
      return stored ? JSON.parse(stored) : initialValue;
    } catch {
      return initialValue;
    }
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
}

// Usage — same API as useState
const [theme, setTheme] = useLocalStorage('theme', 'light');
```

**Pattern 2: Composing multiple hooks**

jsx

```
function useAsync(asyncFn, deps = []) {
  const [state, setState] = useState({
    data: null,
    error: null,
    loading: true,
  });

  useEffect(() => {
    let cancelled = false;
    setState(prev => ({ ...prev, loading: true, error: null }));

    asyncFn()
      .then(data => {
        if (!cancelled) setState({ data, error: null, loading: false });
      })
      .catch(error => {
        if (!cancelled) setState({ data: null, error, loading: false });
      });

    return () => { cancelled = true; };
  }, deps);

  return state;
}

// Composed into a more specific hook
function useUser(userId) {
  return useAsync(() => fetchUser(userId), [userId]);
}

// Usage
const { data: user, loading, error } = useUser('123');
```

**Pattern 3: Event-based hooks**

jsx

```
function useWindowSize() {
  const [size, setSize] = useState({ width: 0, height: 0 });

  useEffect(() => {
    function handleResize() {
      setSize({ width: window.innerWidth, height: window.innerHeight });
    }

    handleResize();  // Set initial value
    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);

  return size;
}

function useMediaQuery(query) {
  const [matches, setMatches] = useState(false);

  useEffect(() => {
    const mediaQuery = window.matchMedia(query);
    setMatches(mediaQuery.matches);

    const handler = (e) => setMatches(e.matches);
    mediaQuery.addEventListener('change', handler);
    return () => mediaQuery.removeEventListener('change', handler);
  }, [query]);

  return matches;
}

// Usage
const isMobile = useMediaQuery('(max-width: 768px)');
```

**Testing custom hooks** is covered in Chapter 11 (Q11.12). Use `renderHook` from React Testing Library, test state transitions with `act()`, and test async hooks with `waitFor`.

**Custom hook design principles:**

- Return the minimum needed (don't expose internal state the consumer doesn't need)
- Accept configuration as parameters (make the hook flexible)
- Follow the `use` naming convention (required for hooks rules enforcement)
- Handle cleanup in useEffect returns (prevent leaks)
- Document the hook's API with TypeScript types

---

## Q 16.20 Why do the Rules of Hooks exist? What breaks without them?

**Difficulty:** Mid **Asked at:** Google, Amazon, Flipkart

The two rules: hooks must be called at the top level (not inside conditions, loops, or nested functions), and hooks must be called from React functions (components or custom hooks).

**Why top-level only:**

React identifies hooks by their call order. On every render, React walks through the hooks in the same order and matches each hook call to its stored state. If you call hooks conditionally, the order changes between renders, and React pairs hooks with the wrong state.

jsx

```
// BROKEN: Hook call order changes between renders
function Profile({ showAge }) {
  const [name, setName] = useState('');      // Hook 1

  if (showAge) {
    const [age, setAge] = useState(0);       // Hook 2 (sometimes)
  }

  const theme = useContext(ThemeContext);     // Hook 2 or 3 (shifts!)
  // When showAge changes, theme gets paired with age's state slot → crash
}
```

javascript

```
Render 1 (showAge = true):          Render 2 (showAge = false):
  Hook 1 → useState('') ✓            Hook 1 → useState('') ✓
  Hook 2 → useState(0) ✓             Hook 2 → useContext(Theme) ✗ MISMATCH!
  Hook 3 → useContext(Theme) ✓        React expected useState(0) here
```

**The fix — move the condition inside the hook's usage, not around the hook call:**

jsx

```
function Profile({ showAge }) {
  const [name, setName] = useState('');
  const [age, setAge] = useState(0);         // Always called
  const theme = useContext(ThemeContext);

  // Use the value conditionally, not the hook
  return (
    <div>
      <p>{name}</p>
      {showAge && <p>Age: {age}</p>}
    </div>
  );
}
```

**Why React functions only:**

Hooks need access to React's internal fiber tree to store and retrieve state. Regular JavaScript functions don't have this connection. Calling `useState` outside a component would have nowhere to store the state.

**How ESLint catches violations:**

json

```
{
  "plugins": ["react-hooks"],
  "rules": {
    "react-hooks/rules-of-hooks": "error",
    "react-hooks/exhaustive-deps": "warn"
  }
}
```

`react-hooks/rules-of-hooks` catches conditional hook calls and hooks outside components. `react-hooks/exhaustive-deps` catches missing dependencies in useEffect/useMemo/useCallback. Both should be enabled on every React project.

**The exception:** `use()` (Q16.18) can be called conditionally. It's the only hook that breaks this rule, and it was specifically designed for it.

---

## Learn More

- [Rules of Hooks react.dev](https://react.dev/reference/rules/rules-of-hooks)
- [Reusing logic with custom hooks react.dev](https://react.dev/learn/reusing-logic-with-custom-hooks)
- [Rules of hooks for beginners YouTube](https://www.youtube.com/watch?v=_KMCd9Vdfy0)
- [Custom hooks every dev should know YouTube](https://www.youtube.com/watch?v=397259iiZ6M)
- [Custom hooks and rules YouTube](https://www.youtube.com/watch?v=2Xz_gW0sO-g)
