---
title: "Render Props Pattern"
source: https://www.geekyfrontend.in/learn/react-interview-kit/render-props-pattern
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Render Props Pattern

*3 min read · Updated 24 Mar 2026 Topic 32 of 62 Bookmark Mark done 6 · Topic 32 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/render-props-pattern](https://www.geekyfrontend.in/learn/react-interview-kit/render-props-pattern)

---

# 6.3 - Render Props Pattern

---

## 🎯 What Interviewers Actually Ask

| Question | Level | Frequency |
| --- | --- | --- |
| "What is the Render Props pattern?" | 🟡🔴 | ★★★☆☆ |
| "How does it compare to HOCs and hooks?" | 🟡🔴 | ★★★★☆ |
| "Give a practical example" | 🟡🔴 | ★★★☆☆ |

---

## The Pattern

Render Props is a technique where a component receives a function as a prop (or as `children`) and calls it to determine what to render. The component provides data or behavior, and the consuming code decides how to display it.

jsx

```
// The component shares mouse position data through a render prop
function MouseTracker({ render }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handleMove = (e) => setPosition({ x: e.clientX, y: e.clientY });
    window.addEventListener('mousemove', handleMove);
    return () => window.removeEventListener('mousemove', handleMove);
  }, []);

  return render(position);
}

// The consumer decides what to render with that data
function App() {
  return (
    <MouseTracker
      render={({ x, y }) => <p>Mouse is at ({x}, {y})</p>}
    />
  );
}
```

### Using `children` as a function (common variation)

jsx

```
function MouseTracker({ children }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  // ... same event listener logic ...
  return children(position);
}

// Cleaner JSX syntax
<MouseTracker>
  {({ x, y }) => <p>Mouse is at ({x}, {y})</p>}
</MouseTracker>
```

## When It Was Useful

Before hooks, render props were the main alternative to HOCs for sharing behavior. Components like `<Query>` in Apollo Client v2 and `<Formik>` used this pattern extensively.

## The Modern Alternative

Custom hooks replaced render props for most use cases. The logic stays the same, but the ergonomics are better.

jsx

```
// Render props (pre-hooks)
<MouseTracker>
  {({ x, y }) => <p>({x}, {y})</p>}
</MouseTracker>

// Custom hook (modern)
function App() {
  const { x, y } = useMouse();
  return <p>({x}, {y})</p>;
}
```

## When Render Props Still Make Sense

1. **Library components** that need to provide flexible rendering to consumers (like virtualized list renderers).
2. **Component-level inversion of control** where a parent provides the rendering logic to a generic child.

**Interview answer:** "Render Props is a pattern where a component calls a function prop to determine what to render. The component provides data or behavior, and the consumer controls the visual output. It was a popular alternative to HOCs before hooks, used by libraries like Apollo and Formik. Today, custom hooks are preferred for most cases because they're simpler and don't create extra nesting. But render props still have a place in library APIs where you need to let consumers control the rendering of individual items."

---

> **Part of the React Interview Preparation Kit** — [Back to Course Roadmap →](https://claude.ai/00-course-roadmap.md)

---

## Learn More

- [Render Props (docs) reactjs.org](https://legacy.reactjs.org/docs/render-props.html)
- [When to use the render props pattern YouTube](https://www.youtube.com/watch?v=SePeEFeFby8)
- [Render props made simple YouTube](https://www.youtube.com/watch?v=3IdCQ7QAs38)
- [Render prop pattern in React YouTube](https://www.youtube.com/watch?v=uCDm0tIEG6U)
