---
title: "Loading, Error & Empty States"
source: https://www.geekyfrontend.in/learn/react-interview-kit/loading-error-empty-states
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Loading, Error & Empty States

*3 min read · Updated 24 Mar 2026 Topic 37 of 62 Bookmark Mark done 8 · Topic 37 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/loading-error-empty-states](https://www.geekyfrontend.in/learn/react-interview-kit/loading-error-empty-states)

---

# 8.2 - Loading, Error & Empty States

---

## 🎯 What Interviewers Actually Ask

| Question | Level | Frequency |
| --- | --- | --- |
| "How do you handle loading and error states?" | 🟢🟡 | ★★★★★ |
| "What is a skeleton screen?" | 🟡 | ★★★☆☆ |
| "How do you handle empty states?" | 🟡 | ★★★☆☆ |

---

## The Four States of a Data Component

Every component that fetches data cycles through these states. Missing any one of them creates a broken user experience.

jsx

```
function UserList() {
  const [users, setUsers] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
 
  useEffect(() => {
    let cancelled = false;
    setLoading(true);
    setError(null);
 
    fetch('/api/users')
      .then(res => {
        if (!res.ok) throw new Error(`Server responded with ${res.status}`);
        return res.json();
      })
      .then(data => { if (!cancelled) { setUsers(data); setLoading(false); } })
      .catch(err => { if (!cancelled) { setError(err.message); setLoading(false); } });
 
    return () => { cancelled = true; };
  }, []);
 
  // The cascade order matters: loading, then error, then empty, then data
  if (loading) return <UserListSkeleton />;
  if (error) return <ErrorCard message={error} onRetry={() => window.location.reload()} />;
  if (!users || users.length === 0) return <EmptyState message="No users found." icon="👥" />;
 
  return (
    <ul>
      {users.map(user => <li key={user.id}>{user.name}</li>)}
    </ul>
  );
}
```

### Why this order?

Check **loading first** so you never show stale data during a refetch. Check **error next** because an error with stale data showing is confusing. Check **empty** before data because an empty array is technically "successful" but needs a different UI.

### Skeleton screens vs spinners

**Spinners** say "wait, something is happening." The user stares at a spinning circle with no context.

**Skeleton screens** show the shape of the content that's loading. The user's brain starts processing the layout immediately, which makes the load feel 30-40% faster (perception, not actual speed). Every modern app (YouTube, Facebook, LinkedIn) uses skeletons.

---

## The Reusable Pattern

Extract the loading/error/data logic into a custom hook (covered in 2.4) or use React Query (8.1). The component should only care about rendering.

jsx

```
// With React Query: zero boilerplate for loading/error
function UserList() {
  const { data: users, isLoading, error } = useQuery({
    queryKey: ['users'],
    queryFn: () => fetch('/api/users').then(r => r.json()),
  });
 
  if (isLoading) return <UserListSkeleton />;
  if (error) return <ErrorCard message={error.message} />;
  if (users.length === 0) return <EmptyState message="No users yet." />;
 
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}
```

---

## ✅ Key Takeaways

- Every data component needs **four states**: loading, error, empty, success.
- Check in order: **loading, error, empty, then data.**
- Use **skeleton screens** over spinners for perceived speed.
- Include a **retry mechanism** on error states.
- **Empty states** should be helpful ("No results found. Try adjusting your filters."), not just blank.

---

## Learn More

- [Suspense — API reference react.dev](https://react.dev/reference/react/Suspense)
- [TanStack Query — request states github.com](https://github.com/TanStack/query)
- [Loading, error, empty & success states YouTube](https://www.youtube.com/watch?v=_0iFVd_bC9w)
- [A better way to do loading states YouTube](https://www.youtube.com/watch?v=V0VfR0eaz98)
- [Rethinking isLoading in data fetching YouTube](https://www.youtube.com/watch?v=o4bcGpAtbgE)
