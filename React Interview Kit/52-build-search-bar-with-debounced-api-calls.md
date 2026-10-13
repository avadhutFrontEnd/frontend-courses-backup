---
title: "Build: Search Bar with Debounced API Calls"
source: https://www.geekyfrontend.in/learn/react-interview-kit/build-search-bar-with-debounced-api-calls
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Build: Search Bar with Debounced API Calls

*2 min read · Updated 23 Apr 2026 Topic 52 of 62 Bookmark Mark done Problem · Topic 52 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/build-search-bar-with-debounced-api-calls](https://www.geekyfrontend.in/learn/react-interview-kit/build-search-bar-with-debounced-api-calls)

---

**Problem:** Build a search input that calls an API after the user stops typing (debounced). Show loading and results.

**What This Tests:** Custom hooks (useDebounce), useEffect with dependencies, loading/error states, API integration.

jsx

jsx

```
function useDebounce(value, delay = 500) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}

function SearchBar() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);
  const debouncedQuery = useDebounce(query, 300);

  useEffect(() => {
    if (!debouncedQuery.trim()) {
      setResults([]);
      return;
    }

    let cancelled = false;
    setLoading(true);

    fetch(`https://api.github.com/search/users?q=${debouncedQuery}`)
      .then(res => res.json())
      .then(data => {
        if (!cancelled) {
          setResults(data.items || []);
          setLoading(false);
        }
      })
      .catch(() => {
        if (!cancelled) setLoading(false);
      });

    return () => { cancelled = true; };
  }, [debouncedQuery]);

  return (
    <div>
      <input value={query} onChange={e => setQuery(e.target.value)} placeholder="Search GitHub users..." />

      {loading && <p>Searching...</p>}

      {!loading && results.length > 0 && (
        <ul>
          {results.slice(0, 10).map(user => (
            <li key={user.id}>
              <img src={user.avatar_url} alt={user.login} width={32} height={32} />
              {user.login}
            </li>
          ))}
        </ul>
      )}

      {!loading && debouncedQuery && results.length === 0 && <p>No results found.</p>}
    </div>
  );
}
```

**Key Concepts:** Custom useDebounce hook, race condition prevention with cancelled flag, conditional rendering for loading/empty/results states, limiting displayed results.

---

## Learn More

- [Debouncing & Throttling explained css-tricks.com](https://css-tricks.com/debouncing-throttling-explained-examples/)
- [Debounced search input + API call YouTube](https://www.youtube.com/watch?v=MHm-2YmWEek)
- [Debounce input — stop spamming your API YouTube](https://www.youtube.com/watch?v=n52A60Z7Ha0)
- [Debounced input with a custom hook YouTube](https://www.youtube.com/watch?v=Wf7INnelvkI)
