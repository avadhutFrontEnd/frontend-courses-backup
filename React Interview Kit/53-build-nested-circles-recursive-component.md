---
title: "Build: Nested Circles (Recursive Component)"
source: https://www.geekyfrontend.in/learn/react-interview-kit/build-nested-circles-recursive-component
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Build: Nested Circles (Recursive Component)

*1 min read · Updated 23 Apr 2026 Topic 53 of 62 Bookmark Mark done Problem · Topic 53 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/build-nested-circles-recursive-component](https://www.geekyfrontend.in/learn/react-interview-kit/build-nested-circles-recursive-component)

---

**Problem:** Given a number input, render that many nested circles using recursion.

**What This Tests:** Recursion in React, dynamic styling, component composition.

jsx

jsx

```
function Circle({ depth, maxDepth }) {
  if (depth > maxDepth) return null;

  const size = (maxDepth - depth + 1) * 80;
  const colors = ['#FF6B6B', '#4ECDC4', '#45B7D1', '#96E6A1', '#DDA0DD', '#FFD93D'];

  return (
    <div
      style={{
        width: size,
        height: size,
        borderRadius: '50%',
        border: `2px solid ${colors[depth % colors.length]}`,
        display: 'flex',
        alignItems: 'center',
        justifyContent: 'center',
      }}
    >
      <Circle depth={depth + 1} maxDepth={maxDepth} />
    </div>
  );
}

function NestedCircles() {
  const [count, setCount] = useState(3);

  return (
    <div style={{ display: 'flex', flexDirection: 'column', alignItems: 'center' }}>
      <input
        type="number"
        min={1}
        max={10}
        value={count}
        onChange={e => setCount(Number(e.target.value))}
      />
      <div style={{ marginTop: 20 }}>
        <Circle depth={1} maxDepth={count} />
      </div>
    </div>
  );
}
```

**Key Concepts:** Recursive component rendering, base case (depth > maxDepth returns null), dynamic sizing and coloring, controlled number input with min/max.

---

## Learn More

- [Rendering Lists (recursion) react.dev](https://react.dev/learn/rendering-lists)
- [Recursive component — how & why YouTube](https://www.youtube.com/watch?v=FR07idGIMAE)
- [Build a recursive React component YouTube](https://www.youtube.com/watch?v=6UU2Ey4KZr8)
- [Recursive components in React YouTube](https://www.youtube.com/watch?v=ZGqdrNZroYg)
