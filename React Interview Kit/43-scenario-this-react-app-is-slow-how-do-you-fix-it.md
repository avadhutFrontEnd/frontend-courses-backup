---
title: "Scenario: 'This React App is Slow. How Do You Fix It?'"
source: https://www.geekyfrontend.in/learn/react-interview-kit/scenario-this-react-app-is-slow-how-do-you-fix-it
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Scenario: "This React App is Slow. How Do You Fix It?"

*3 min read · Updated 24 Mar 2026 Topic 43 of 62 Bookmark Mark done 10 · Topic 43 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/scenario-this-react-app-is-slow-how-do-you-fix-it](https://www.geekyfrontend.in/learn/react-interview-kit/scenario-this-react-app-is-slow-how-do-you-fix-it)

---

# 10.1 - Scenario: "This React App is Slow. How Do You Fix It?"

---

## 🎯 Why This Question Exists

This is the most common senior-level React interview question. It tests whether you can diagnose and fix performance problems systematically, not just recite optimization buzzwords.

---

## The Framework for Answering

Never jump to solutions. Walk through a structured diagnosis.

### Step 1: Identify the Symptom

Ask the interviewer clarifying questions:

- "Is it slow on initial load, or slow during interactions?"
- "Is it a specific page or the whole app?"
- "Are there any large lists or heavy computations?"
- "Is it slow on all devices or just low-end ones?"

**Slow initial load** and **slow interactions** have completely different root causes.

### Step 2: Measure

Before touching any code, profile the app:

- **React DevTools Profiler** to find which components re-render unnecessarily and how long they take.
- **Chrome Performance tab** to find long JavaScript tasks blocking the main thread.
- **Lighthouse** to measure Core Web Vitals (LCP, INP, CLS).
- **Network tab** to check bundle size, image sizes, and API response times.
- **Bundle analyzer** to see what's inside the JavaScript bundle.

### Step 3: Diagnose and Fix

Based on what the profiling reveals, apply the appropriate fix:

| Symptom | Likely Cause | Fix |
| --- | --- | --- |
| Slow initial load, large JS bundle | Too much code loaded upfront | Code splitting with React.lazy, remove unused dependencies, tree shaking |
| Slow initial load, large images | Unoptimized images | WebP/AVIF format, lazy loading (`loading="lazy"`), responsive srcset |
| Slow initial load, many API calls | Waterfall requests on mount | Combine APIs, use SSR/SSG for initial data, cache with React Query |
| Slow interactions, entire page re-renders | State too high in the tree | Move state down, split components |
| Slow interactions, child components re-render with same props | Missing memoization | React.memo on expensive children, useMemo/useCallback for stable props |
| Slow interactions, huge list rendering | Thousands of DOM nodes | Virtualize with react-window |
| Slow typing in search input | API call on every keystroke | Debounce the input (useDebounce hook) |
| Layout shifting on load | Missing image dimensions, late-loaded content | Set explicit width/height, use skeleton screens |

### Step 4: Verify

After applying fixes, re-run profiling to confirm the improvement. Measure before and after.

---

## 🗣️ Sample Answer

> "My approach would be to first understand the symptom: is it a slow initial load or slow interactions? Then I'd profile the app. For initial load, I'd check bundle size with a bundle analyzer and look for opportunities to code-split, lazy-load routes, and replace heavy dependencies. For interaction slowness, I'd use the React Profiler to find components that re-render unnecessarily. My first fix would be restructuring: moving state down and splitting components. If that's not enough, I'd apply React.memo to expensive children. For long lists, I'd virtualize. For search inputs, I'd debounce. And I'd always measure before and after to confirm the improvement."

---

## Learn More

- [memo — API reference react.dev](https://react.dev/reference/react/memo)
- [useMemo — API reference react.dev](https://react.dev/reference/react/useMemo)
- [React performance: fix a slow app YouTube](https://www.youtube.com/watch?v=AZ9i3eoyrnE)
- [React performance optimization patterns YouTube](https://www.youtube.com/watch?v=keTcXT145CI)
- [Best ways to optimize modern React YouTube](https://www.youtube.com/watch?v=LuQ8VWh6eYE)
