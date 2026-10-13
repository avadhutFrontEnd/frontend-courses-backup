---
title: "Core hooks"
source: https://www.geekyfrontend.in/learn/complete-frontend-kit/core-hooks
course: Frontend System Design Kit
tags: [geeky-frontend, frontend-system-design-kit]
---

# Core hooks

*9 min read · Updated 19 May 2026 Topic 44 of 77 Bookmark Mark done 16A · Topic 44 of 77*

**Source:** [https://www.geekyfrontend.in/learn/complete-frontend-kit/core-hooks](https://www.geekyfrontend.in/learn/complete-frontend-kit/core-hooks)

---

# 16A. Core Hooks

**Questions:** 7 **Difficulty:** Junior to Mid **Companies:** Every company

---

## Q 16.1 What are the gotchas with useState? Explain lazy initialization, batching, and functional updates.

**Difficulty:** Junior **Asked at:** Every company

**Lazy initialization — avoid expensive computation on every render:**

jsx

```
// BAD: computeInitialState() runs on EVERY render, not just the first
const [state, setState] = useState(computeExpensiveInitialState());

// GOOD: Pass a function — only runs on the first render
const [state, setState] = useState(() => computeExpensiveInitialState());
```

The function form (`() => value`) is only called during the initial render. The direct form (`value`) is evaluated on every render even though the result is thrown away after the first.

**Functional updates — avoid stale state in closures:**

jsx

```
// BUG: If clicked rapidly, count may not increment correctly
function Counter() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);  // 'count' is captured from this render's closure
    setCount(count + 1);  // Same 'count' — both set to the same value
    // Result: count increases by 1, not 2
  }
}

// FIX: Use functional update — always gets the latest state
function Counter() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(prev => prev + 1);  // prev is always current
    setCount(prev => prev + 1);  // prev is now prev+1
    // Result: count increases by 2
  }
}
```

**Batching — multiple setState calls = one re-render:**

React 18 batches all state updates (even inside promises, setTimeout, and event handlers) into a single re-render. This is automatic — you don't need to do anything.

jsx

```
function handleClick() {
  setName('Ankit');     // Doesn't re-render yet
  setAge(28);           // Doesn't re-render yet
  setLoading(false);    // Doesn't re-render yet
  // React batches all three → ONE re-render
}

// Even in async code (React 18+)
async function fetchData() {
  const data = await api.getData();
  setData(data);        // Batched
  setLoading(false);    // Batched → one re-render
}
```

**Objects and arrays — state is immutable:**

jsx

```
// BUG: Mutating state directly — React doesn't detect the change
const [user, setUser] = useState({ name: 'Ankit', age: 28 });
user.age = 29;        // WRONG — mutating existing object
setUser(user);        // React sees same reference → no re-render

// FIX: Create a new object
setUser({ ...user, age: 29 });        // New object → re-render
setUser(prev => ({ ...prev, age: 29 })); // Functional update version
```

---

## Q 16.2 What are the common pitfalls of useEffect? Explain dependency arrays, cleanup, and race conditions.

**Difficulty:** Mid **Asked at:** Every company

**Missing dependencies — the stale closure bug:**

jsx

```
// BUG: count is stale — always logs the initial value
useEffect(() => {
  const id = setInterval(() => {
    console.log(count);  // Always 0 — captured from initial render
  }, 1000);
  return () => clearInterval(id);
}, []);  // Empty deps → effect never re-runs, never gets new 'count'

// FIX: Include count in dependencies
useEffect(() => {
  const id = setInterval(() => {
    console.log(count);  // Always current
  }, 1000);
  return () => clearInterval(id);
}, [count]);  // Re-runs when count changes

// BETTER FIX: Use functional setState to avoid the dependency entirely
useEffect(() => {
  const id = setInterval(() => {
    setCount(prev => prev + 1);  // No dependency on count
  }, 1000);
  return () => clearInterval(id);
}, []);  // Truly stable — runs once
```

**Cleanup — preventing memory leaks and stale updates:**

jsx

```
useEffect(() => {
  const controller = new AbortController();

  fetch(`/api/users/${userId}`, { signal: controller.signal })
    .then(r => r.json())
    .then(setUser)
    .catch(err => {
      if (err.name !== 'AbortError') setError(err);
    });

  // Cleanup: cancel the request if userId changes before response arrives
  return () => controller.abort();
}, [userId]);
```

Without cleanup, if `userId` changes from "1" to "2" quickly, the response for "1" might arrive after "2" and overwrite the correct data with stale data.

**Race conditions with async effects:**

jsx

```
// BUG: Race condition — stale response overwrites fresh one
useEffect(() => {
  fetchUser(userId).then(setUser);
  // If userId changes: 1→2, response for 1 might arrive after response for 2
}, [userId]);

// FIX: Track whether the effect is still "current"
useEffect(() => {
  let cancelled = false;

  fetchUser(userId).then(data => {
    if (!cancelled) setUser(data);  // Only update if this effect is still active
  });

  return () => { cancelled = true; };
}, [userId]);
```

**When NOT to use useEffect:**

- Transforming data for rendering → use `useMemo` instead
- Handling user events → use event handlers, not effects
- Initializing state from props → use lazy initialization in `useState`
- Subscribing to external stores → use `useSyncExternalStore`

---

## Q 16.3 What are the performance problems with useContext?

**Difficulty:** Mid **Asked at:** Google, Flipkart, Razorpay

Covered in depth in Chapter 10 (Q10.2). Summary: every consumer re-renders when the context value changes, even if they only use a small piece of it. Fixes: split contexts by change frequency, memoize the provider value, and use a state management library (Zustand) when context performance becomes a problem.

---

## Q 16.4 When do you use useReducer over useState?

**Difficulty:** Mid **Asked at:** Amazon, Flipkart, Razorpay, Atlassian

Use `useReducer` when state updates depend on the previous state AND the logic is complex enough that multiple `setState` calls become confusing.

jsx

```
// useState approach — getting messy with complex state
function useForm() {
  const [values, setValues] = useState({});
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});
  const [isSubmitting, setIsSubmitting] = useState(false);
  // 4 separate setState calls for one logical operation (submit)
}

// useReducer approach — all state transitions in one place
const initialState = { values: {}, errors: {}, touched: {}, isSubmitting: false };

function formReducer(state, action) {
  switch (action.type) {
    case 'SET_FIELD':
      return {
        ...state,
        values: { ...state.values, [action.field]: action.value },
        touched: { ...state.touched, [action.field]: true },
      };
    case 'SET_ERRORS':
      return { ...state, errors: action.errors };
    case 'SUBMIT_START':
      return { ...state, isSubmitting: true, errors: {} };
    case 'SUBMIT_SUCCESS':
      return { ...initialState };
    case 'SUBMIT_FAILURE':
      return { ...state, isSubmitting: false, errors: action.errors };
    default:
      return state;
  }
}

function useForm() {
  const [state, dispatch] = useReducer(formReducer, initialState);
  // One dispatch per action — all related state changes happen together
}
```

**useReducer advantages:** Related state updates are grouped in one action. State transitions are explicit and testable (pure function). Complex logic lives outside the component. Easier to debug (log every action to see state changes).

**When useState is simpler:** Independent pieces of state (a toggle, an input value, a counter). If your state is a single primitive or a simple object with independent fields, useReducer adds unnecessary structure.

---

## Q 16.5 When does useCallback actually help? What's the common misuse?

**Difficulty:** Mid **Asked at:** Google, Amazon, Flipkart, Razorpay

`useCallback` memoizes a function so it keeps the same reference across renders. It's useful in exactly one scenario: when passing a function to a memoized child component.

jsx

```
// useCallback is USEFUL here — prevents ExpensiveList from re-rendering
const handleItemClick = useCallback((id) => {
  setSelectedId(id);
}, []);

return <ExpensiveList items={items} onItemClick={handleItemClick} />;
// ExpensiveList is React.memo'd — stable onItemClick reference means
// it only re-renders when 'items' changes
```

jsx

```
// useCallback is USELESS here — the child isn't memoized
const handleClick = useCallback(() => {
  setCount(c => c + 1);
}, []);

return <button onClick={handleClick}>Click</button>;
// <button> re-renders when its parent re-renders regardless
// useCallback just adds overhead for no benefit
```

**The rule:** `useCallback` without `React.memo` on the child is almost always wasted. The function reference stays stable, but the child re-renders anyway because it's not memoized. You're paying the cost of memoization (dependency comparison) without the benefit.

**When it matters beyond memo:**

- Dependencies of other hooks (`useEffect` with a function dependency)
- Functions passed to custom hooks that use referential equality
- Functions stored in refs for event handlers

---

## Q 16.6 When should you use useMemo? When does it hurt more than help?

**Difficulty:** Mid **Asked at:** Google, Amazon, Flipkart

`useMemo` caches the result of an expensive computation and only recalculates when dependencies change.

jsx

```
// USEFUL: Expensive filtering/sorting that shouldn't run every render
const filteredProducts = useMemo(() => {
  return products
    .filter(p => p.category === selectedCategory)
    .sort((a, b) => a.price - b.price);
}, [products, selectedCategory]);
```

jsx

```
// WASTEFUL: Simple computation that's faster to re-compute than to memoize
const fullName = useMemo(() => {
  return `${firstName} ${lastName}`;
}, [firstName, lastName]);
// String concatenation takes nanoseconds. Memoization overhead > computation cost.
```

**When useMemo helps:** Filtering/sorting large arrays (1000+ items), complex calculations (statistical aggregation, tree traversal), creating objects/arrays passed as props to memoized children (referential equality).

**When useMemo hurts:** Simple calculations, small data sets, objects that are recreated every render regardless (inside non-memoized components), and premature optimization before measuring.

**The guideline:** Profile first. If a computation takes <1ms, memoizing it adds complexity without measurable benefit. If it takes >10ms and runs on every render, `useMemo` is justified.

---

## Q 16.7 What are the use cases for useRef beyond DOM access?

**Difficulty:** Mid **Asked at:** Amazon, Flipkart, Atlassian, Swiggy

Most people know `useRef` for DOM element access. But its core purpose is holding a mutable value that persists across renders without causing re-renders.

**DOM access (the obvious use):**

jsx

```
const inputRef = useRef(null);
const focusInput = () => inputRef.current?.focus();
return <input ref={inputRef} />;
```

**Storing the previous value:**

jsx

```
function usePrevious(value) {
  const ref = useRef();
  useEffect(() => {
    ref.current = value;
  });
  return ref.current;  // Returns the previous render's value
}

const prevCount = usePrevious(count);
// On render where count = 5, prevCount = 4
```

**Instance variables (mutable state that doesn't trigger re-render):**

jsx

```
function Timer() {
  const intervalRef = useRef(null);

  function start() {
    intervalRef.current = setInterval(() => { /* ... */ }, 1000);
  }

  function stop() {
    clearInterval(intervalRef.current);
  }
  // Storing the interval ID in ref — changing it doesn't re-render
}
```

**Tracking if component is mounted (for async operations):**

jsx

```
function useIsMounted() {
  const mounted = useRef(false);
  useEffect(() => {
    mounted.current = true;
    return () => { mounted.current = false; };
  }, []);
  return mounted;
}

// In an async function
const isMounted = useIsMounted();
const data = await fetchData();
if (isMounted.current) setData(data);  // Only update if still mounted
```

**The mental model:** `useState` = value that, when changed, triggers re-render. `useRef` = value that persists across renders but changing it does NOT trigger re-render. Both survive re-renders. The difference is reactivity.

---

## Learn More

- [Built-in React hooks (reference) react.dev](https://react.dev/reference/react/hooks)
- [8 React hooks explained YouTube](https://www.youtube.com/watch?v=DfTTn6gKPG4)
- [Master useState, useEffect & more YouTube](https://www.youtube.com/watch?v=2Vwy12xK2kM)
- [All React hooks in 12 minutes YouTube](https://www.youtube.com/watch?v=LOH1l-MP_9k)
