---
title: "Quick revision sheets"
source: https://www.geekyfrontend.in/learn/complete-frontend-kit/quick-revision-sheets
course: Frontend System Design Kit
tags: [geeky-frontend, frontend-system-design-kit]
---

# Quick revision sheets

*6 min read · Updated 4 Jun 2026 Topic 77 of 77 Bookmark Mark done 25B · Topic 77 of 77*

**Source:** [https://www.geekyfrontend.in/learn/complete-frontend-kit/quick-revision-sheets](https://www.geekyfrontend.in/learn/complete-frontend-kit/quick-revision-sheets)

---

# 25B. Quick Revision Sheets

**Items:** 11 **Purpose:** One-page cheat sheets for the night before an interview.

---

## 25.6 JavaScript Cheat Sheet

javascript

```
SCOPE & CLOSURES
  • var = function-scoped, let/const = block-scoped
  • Closures: function + lexical environment. Inner fn accesses outer vars after outer returns
  • Temporal Dead Zone: let/const exist but can't be accessed before declaration

  THIS KEYWORD
  • Default: window (strict: undefined)
  • Object method: the object
  • Arrow fn: inherits from enclosing scope
  • new: the new instance
  • call/apply/bind: explicitly set

  ASYNC
  • Event loop: Call stack → Microtask queue (Promises) → Macrotask queue (setTimeout)
  • Microtasks always run before macrotasks
  • Promise.all: fails if ANY rejects  |  Promise.allSettled: never fails
  • Promise.race: first to settle      |  Promise.any: first to fulfill

  GOTCHAS
  • 0.1 + 0.2 !== 0.3 (floating point)
  • typeof null === "object" (JS bug)
  • [] == false → true, but [] is truthy
  • NaN !== NaN (use Number.isNaN)
```

---

## 25.7 React Cheat Sheet

javascript

```
HOOKS QUICK REFERENCE
  useState     → local state, [value, setter]
  useEffect    → side effects, cleanup, deps array
  useRef       → mutable value, persists across renders, no re-render
  useMemo      → cache computation, recompute on deps change
  useCallback  → cache function reference, useful with React.memo
  useReducer   → complex state with dispatch
  useContext   → read context, all consumers re-render on change
  useTransition→ mark update as non-urgent

  RENDERING RULES
  • Re-render: state change, parent re-render, context change
  • Prevent: React.memo, useMemo/useCallback
  • Keys: stable + unique. Never index for dynamic lists

  PATTERNS
  • Controlled: value + onChange (React owns state)
  • Uncontrolled: ref + defaultValue (DOM owns state)
  • Custom hooks: reusable logic, must start with "use"
```

---

## 25.8 CSS Cheat Sheet

javascript

```
FLEXBOX
  justify-content: center | space-between | space-around  (main axis)
  align-items: center | stretch | flex-start               (cross axis)
  flex: 1 (grow equally) | flex: none (don't grow/shrink)
  gap: 16px

  GRID
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr))  ← responsive
  grid-template-areas: "header header" "sidebar main" "footer footer"

  SPECIFICITY (high → low)
  !important > inline > #id > .class/:pseudo > element
  :where() = 0  |  :is() = highest arg  |  :has() = arg specificity

  MODERN CSS
  container queries, @layer, :has(), clamp(), dvh/svh/lvh, nesting

  PERFORMANCE
  transform + opacity = GPU (fast) | width/height/top = layout (slow)
  content-visibility: auto → skip offscreen rendering
```

---

## 25.9 TypeScript Cheat Sheet

javascript

```
ESSENTIALS
  type vs interface: type for unions, interface for object shapes + extends
  Enums → use string unions: type Status = "loading" | "success" | "error"
  as const → readonly + literal types  |  satisfies → validate without widening

  TYPE NARROWING
  typeof, instanceof, "prop" in obj, discriminated unions

  UTILITY TYPES
  Partial<T>  Required<T>  Pick<T,K>  Omit<T,K>  Record<K,V>
  Readonly<T>  ReturnType<typeof fn>  Parameters<typeof fn>

  REACT + TS
  Props: interface ButtonProps { label: string; onClick: () => void }
  Events: React.ChangeEvent<HTMLInputElement>
  Refs: useRef<HTMLInputElement>(null)
```

---

## 25.10 Data Structures — Complexity Summary

javascript

```
Structure    │ Access │ Search │ Insert │ Delete │ Frontend Use
  ─────────────┼────────┼────────┼────────┼────────┼─────────────
  Array        │ O(1)   │ O(n)   │ O(n)*  │ O(n)*  │ Lists, grids
  Map/Object   │ O(1)   │ O(1)   │ O(1)   │ O(1)   │ Lookups, cache
  Set          │ -      │ O(1)   │ O(1)   │ O(1)   │ Dedup, membership
  Stack        │ O(n)   │ O(n)   │ O(1)   │ O(1)   │ Undo/redo
  Queue        │ O(n)   │ O(n)   │ O(1)   │ O(1)   │ Task scheduling
  Linked List  │ O(n)   │ O(n)   │ O(1)** │ O(1)** │ LRU cache
  BST          │O(logn) │O(logn) │O(logn) │O(logn) │ Sorted data
  Heap         │ O(1)   │ O(n)   │O(logn) │O(logn) │ Top-K, priority
  Trie         │ O(m)   │ O(m)   │ O(m)   │ O(m)   │ Autocomplete

  * push/pop O(1), shift/splice O(n)  ** with node reference
```

---

## 25.11 Algorithm Patterns

javascript

```
Pattern            │ Recognize When...                    │ Example
  ───────────────────┼──────────────────────────────────────┼────────────────
  Two Pointer        │ Sorted array, pair finding           │ Two Sum sorted
  Sliding Window     │ Contiguous subarray/substring        │ Max sum K
  Binary Search      │ Sorted data, min/max optimization    │ Rotated array
  BFS                │ Shortest path, level-order            │ Rotten oranges
  DFS                │ All paths, tree traversal             │ Number of islands
  Dynamic Programming│ Overlapping subproblems               │ Coin change
  Greedy             │ Local optimal = global optimal        │ Activity selection
  Monotonic Stack    │ Next greater/smaller element          │ Daily temperatures
  Topological Sort   │ Dependency ordering                   │ Course schedule
  Union-Find         │ Connected components                  │ Number of provinces
```

---

## 25.12 Design Patterns — One-liner Each

javascript

```
Module     → Encapsulate code, expose public API
  Singleton  → One instance (ES Module = natural singleton)
  Factory    → Create objects without specifying exact class
  Observer   → Notify dependents when state changes (DOM events)
  Pub/Sub    → Decoupled communication via message broker
  Strategy   → Swap algorithms at runtime (validation, sorting)
  Command    → Encapsulate actions for undo/redo
  Decorator  → Add behavior without modifying original (HOCs)
  Proxy      → Intercept access (validation, Vue reactivity)
  State      → Change behavior based on state (XState)
  Composite  → Tree structure, uniform interface (React tree)
```

---

## 25.13 System Design — RADIO Template

javascript

```
R — REQUIREMENTS (5 min)
      Functional: what the user does
      Non-functional: scale, performance, offline, a11y

  A — ARCHITECTURE (10 min)
      Component tree, page structure, routing
      Draw a diagram

  D — DATA MODEL (10 min)
      State shape, API contracts, local vs server vs URL state

  I — INTERFACE / API (5 min)
      Component props, REST/GraphQL/WS endpoints

  O — OPTIMIZATION (10 min)
      Performance, accessibility, error handling, offline
      End with TRADE-OFFS: "I chose X over Y because..."
```

---

## 25.14 Web Security — Attack + Prevention

javascript

```
Attack       │ Prevention
  ─────────────┼─────────────────────────────────────
  XSS          │ Output encoding, CSP, HttpOnly cookies, DOMPurify
  CSRF         │ SameSite cookies, CSRF tokens, check Origin header
  Clickjacking │ frame-ancestors CSP, X-Frame-Options
  Supply Chain │ Lockfiles, npm audit, exact versions, SRI

  MUST-SET HEADERS
  Content-Security-Policy, Strict-Transport-Security,
  X-Content-Type-Options, Referrer-Policy, Permissions-Policy
```

---

## 25.15 Performance Checklist

javascript

```
LOADING
  [ ] Critical CSS inlined           [ ] Scripts deferred
  [ ] Fonts preloaded + swap         [ ] Hero image fetchpriority="high"
  [ ] Images: WebP/AVIF, srcset, lazy [ ] Code split per route
  [ ] Third-party scripts async

  RUNTIME
  [ ] No layout thrashing            [ ] Animations: transform/opacity only
  [ ] Large lists virtualized        [ ] React.memo on expensive components
  [ ] content-visibility: auto

  NETWORK
  [ ] HTTP/2+, Brotli                [ ] Cache-Control: immutable for hashed
  [ ] preconnect to API/CDN          [ ] AbortController for stale requests

  MONITORING
  [ ] Core Web Vitals tracked        [ ] Lighthouse CI in pipeline
  [ ] Bundle size budget enforced    [ ] Error tracking (Sentry)
```

---

## 25.16 HTTP Reference Card

javascript

```
METHODS
  GET (read) | POST (create) | PUT (replace) | PATCH (partial) | DELETE (remove)

  STATUS CODES
  200 OK          │ 400 Bad Request     │ 500 Internal Server Error
  201 Created     │ 401 Unauthorized    │ 502 Bad Gateway
  204 No Content  │ 403 Forbidden       │ 503 Service Unavailable
  301 Moved       │ 404 Not Found       │
  304 Not Modified│ 429 Too Many Requests│

  KEY HEADERS
  Content-Type: application/json     │ Cache-Control: public, max-age=31536000
  Authorization: Bearer <token>      │ Set-Cookie: session=x; HttpOnly; Secure
  Accept-Encoding: gzip, br         │ Access-Control-Allow-Origin: https://...
```

---

## Learn More

- [JS interview questions (repo) github.com](https://github.com/sudheerj/javascript-interview-questions)
- [The Modern JavaScript Tutorial javascript.info](https://javascript.info)
- [JavaScript revision & interview cheat sheet YouTube](https://www.youtube.com/watch?v=xejr5Uv_sAQ)
- [The only JavaScript cheat sheet you need YouTube](https://www.youtube.com/watch?v=qxICCY9eNwk)
- [DSA cheat sheet for coding interviews YouTube](https://www.youtube.com/watch?v=Zed1QNWVQx8)
