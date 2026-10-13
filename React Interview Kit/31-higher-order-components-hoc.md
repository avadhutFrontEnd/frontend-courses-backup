---
title: "Higher-Order Components (HOC)"
source: https://www.geekyfrontend.in/learn/react-interview-kit/higher-order-components-hoc
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Higher-Order Components (HOC)

*3 min read · Updated 24 Mar 2026 Topic 31 of 62 Bookmark Mark done 6 · Topic 31 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/higher-order-components-hoc](https://www.geekyfrontend.in/learn/react-interview-kit/higher-order-components-hoc)

---

# 6.2 - Higher-Order Components (HOC)

---

## 🎯 What Interviewers Actually Ask

| Question | Level | Frequency |
| --- | --- | --- |
| "What is a Higher-Order Component?" | 🟡🔴 | ★★★★☆ |
| "Give a real-world example of an HOC" | 🟡🔴 | ★★★★☆ |
| "What are the drawbacks of HOCs?" | 🔴 | ★★★☆☆ |
| "HOC vs Custom Hooks: which do you prefer?" | 🟡🔴 | ★★★★☆ |

---

## The Pattern

A Higher-Order Component is a function that takes a component and returns a new component with enhanced behavior. It's a pattern, not a React API. The concept comes from higher-order functions in JavaScript (functions that take or return other functions).

jsx

```
// The HOC
function withAuth(WrappedComponent) {
  return function AuthenticatedComponent(props) {
    const { user } = useAuth();

    if (!user) {
      return <Navigate to="/login" />;
    }

    return <WrappedComponent {...props} user={user} />;
  };
}

// Usage: wrap any component to add auth protection
const ProtectedDashboard = withAuth(Dashboard);
const ProtectedSettings = withAuth(Settings);
```

The HOC doesn't modify the original component. It wraps it in a new component that adds functionality (in this case, authentication checking).

## Real-World HOC Examples

**withLoading:** Shows a spinner while data loads

jsx

```
function withLoading(WrappedComponent) {
  return function LoadingComponent({ isLoading, ...props }) {
    if (isLoading) return <Spinner />;
    return <WrappedComponent {...props} />;
  };
}

const UserListWithLoading = withLoading(UserList);

// Usage
<UserListWithLoading isLoading={loading} users={users} />
```

**withStyles:** Injects common styles

jsx

```
function withStyles(WrappedComponent) {
  return function StyledComponent(props) {
    const style = { padding: '16px', margin: '8px', borderRadius: '8px' };
    return <WrappedComponent style={style} {...props} />;
  };
}
```

## Drawbacks of HOCs

1. **Wrapper hell.** Nesting multiple HOCs creates deeply nested component trees that are hard to debug: `withAuth(withLoading(withStyles(MyComponent)))`.
2. **Prop collisions.** If two HOCs inject a prop with the same name, one overwrites the other silently.
3. **Difficult to trace.** In React DevTools, you see layers of anonymous wrapper components instead of your actual component.
4. **Static composition only.** You can't use HOCs inside a render method or conditionally. They must be applied at the module level.

## HOC vs Custom Hooks

Custom hooks solve the same problem (sharing logic between components) without the drawbacks. In modern React, hooks are the preferred approach.

| Feature | HOC | Custom Hook |
| --- | --- | --- |
| How it works | Wraps the component | Called inside the component |
| Wrapper hell | Yes, nesting gets messy | No wrappers, flat structure |
| Prop collisions | Possible | Not possible (returns explicit values) |
| DevTools readability | Poor (anonymous wrappers) | Clean (hooks show as hooks) |
| Conditional usage | Cannot use conditionally | Same restriction (hooks rules apply) |
| Modern preference | Legacy pattern | Preferred approach |

jsx

```
// HOC approach (legacy)
const EnhancedComponent = withAuth(withLoading(MyComponent));

// Custom hook approach (modern)
function MyComponent() {
  const { user } = useAuth();
  const { data, loading } = useFetch('/api/data');

  if (!user) return <Navigate to="/login" />;
  if (loading) return <Spinner />;
  return <div>{data}</div>;
}
```

**Interview answer:** "HOCs are functions that take a component and return a new one with added behavior. They were the primary way to share logic before hooks. Real-world examples include withAuth for route protection and withLoading for loading states. The main drawbacks are wrapper hell, prop collisions, and poor DevTools readability. In modern React, I prefer custom hooks because they achieve the same code reuse without wrapping. But I understand HOCs because many codebases still use them, and libraries like React Redux's `connect()` are HOCs."

---

> **Part of the React Interview Preparation Kit** — [Back to Course Roadmap →](https://claude.ai/00-course-roadmap.md)

---

## Learn More

- [Higher-Order Components (docs) reactjs.org](https://legacy.reactjs.org/docs/higher-order-components.html)
- [HOC in 30 minutes for beginners YouTube](https://www.youtube.com/watch?v=tsCoBd7xSK8)
- [Learn HOC in 10 minutes YouTube](https://www.youtube.com/watch?v=J5P0q7EROfw)
- [HOC as a design pattern YouTube](https://www.youtube.com/watch?v=ZeNHbHtKNFo)
