---
title: "Container/Presentational Pattern"
source: https://www.geekyfrontend.in/learn/react-interview-kit/container-presentational-pattern
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Container/Presentational Pattern

*2 min read · Updated 24 Mar 2026 Topic 30 of 62 Bookmark Mark done 6 · Topic 30 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/container-presentational-pattern](https://www.geekyfrontend.in/learn/react-interview-kit/container-presentational-pattern)

---

# 6.1 - Container/Presentational Pattern

---

## 🎯 What Interviewers Actually Ask

| Question | Level | Frequency |
| --- | --- | --- |
| "What is the Container/Presentational pattern?" | 🟡 | ★★★★☆ |
| "What is the difference between smart and dumb components?" | 🟡 | ★★★☆☆ |
| "Is this pattern still relevant with hooks?" | 🟡🔴 | ★★★☆☆ |

---

## The Pattern

The idea is simple: separate **what data to show** from **how to show it**.

**Container components** (smart) handle data fetching, state management, and business logic. They don't render any UI of their own. They pass data down as props.

**Presentational components** (dumb) receive data via props and just render it. They have no idea where the data comes from. They're pure visual components.

jsx

```
// Container: handles data, no UI
function UserListContainer() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch('/api/users')
      .then(res => res.json())
      .then(data => { setUsers(data); setLoading(false); });
  }, []);

  if (loading) return <Spinner />;
  return <UserList users={users} />;
}

// Presentational: renders UI, no logic
function UserList({ users }) {
  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>
          <img src={user.avatar} alt={user.name} />
          <span>{user.name}</span>
        </li>
      ))}
    </ul>
  );
}
```

## Is It Still Relevant?

With hooks, you can put data fetching logic directly in a component or extract it into a custom hook (like `useFetch`). This blurs the line between containers and presentational components.

The pattern itself is less strict than it used to be, but the **principle behind it remains essential**: separate data concerns from visual concerns. Whether you enforce it through separate components or through custom hooks, the goal is the same.

**Interview answer:** "The Container/Presentational pattern separates data logic from UI rendering. Containers fetch data and manage state, presentational components just display what they receive via props. With hooks, I often achieve the same separation by extracting logic into custom hooks instead of separate container components. The underlying principle of separating concerns is still the best practice."

---

## Learn More

- [Thinking in React (split UI) react.dev](https://react.dev/learn/thinking-in-react)
- [Presentational & container components YouTube](https://www.youtube.com/watch?v=NazjKgJp7sQ)
- [Container & presentational — beginner friendly YouTube](https://www.youtube.com/watch?v=L_5pikTGYPA)
- [Container/presentational — step by step YouTube](https://www.youtube.com/watch?v=P6lEYiXq_v4)
