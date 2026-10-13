---
title: "Scenario: 'How Do You Manage State in a Large React App?'"
source: https://www.geekyfrontend.in/learn/react-interview-kit/scenario-how-do-you-manage-state-in-a-large-react-app
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Scenario: "How Do You Manage State in a Large React App?"

*2 min read · Updated 24 Mar 2026 Topic 45 of 62 Bookmark Mark done 10 · Topic 45 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/scenario-how-do-you-manage-state-in-a-large-react-app](https://www.geekyfrontend.in/learn/react-interview-kit/scenario-how-do-you-manage-state-in-a-large-react-app)

---

# 10.3 - Scenario: "How Do You Manage State in a Large React App?"

---

## 🎯 Why This Question Exists

This tests whether you can think strategically about state architecture, not just pick a library.

---

## The Framework: State Layering

Rather than choosing "Context or Redux," describe layers:

### Layer 1: Local component state (useState, useReducer)

Form inputs, modals, tooltips, hover states, tab selection. Most state in any app belongs here. Rule: if only one component or its immediate children use it, keep it local.

### Layer 2: Shared UI state (lift state up or Context)

Sidebar collapsed/expanded, active theme, authenticated user. Use Context for values that change infrequently (theme, auth, locale).

### Layer 3: Complex global state (Redux Toolkit or Zustand)

Shopping cart with add/remove/update/clear across many pages. Notification system with multiple sources. Only use when state is truly global, transitions are complex, and you need middleware or devtools.

### Layer 4: Server/async state (React Query or RTK Query)

API responses that need caching, refetching, pagination, optimistic updates. Separating server state from client state is the biggest architectural improvement you can make.

---

## 🗣️ Sample Answer

> "I think of state in layers. Most state is local, using useState for simple values and useReducer for complex ones. For app-wide low-frequency values like theme and auth, I use Context with a custom hook. If the app has complex state with many transitions, I bring in Redux Toolkit. But the biggest win is separating server state from client state. I use React Query for all API data because it handles caching, background refetching, and loading states automatically. This layered approach keeps each type of state managed by the right tool."

---

## Learn More

- [Managing State react.dev](https://react.dev/learn/managing-state)
- [Choosing the State Structure react.dev](https://react.dev/learn/choosing-the-state-structure)
- [React state management YouTube](https://www.youtube.com/watch?v=-bEzt5ISACA)
- [State management: the beginner’s guide YouTube](https://www.youtube.com/watch?v=U-uD-DNsKgU)
- [The state of state management in React YouTube](https://www.youtube.com/watch?v=BhQYZmaxTCM)
