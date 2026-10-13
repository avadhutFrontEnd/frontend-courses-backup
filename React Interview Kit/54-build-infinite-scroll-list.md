---
title: "Build: Infinite Scroll List"
source: https://www.geekyfrontend.in/learn/react-interview-kit/build-infinite-scroll-list
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Build: Infinite Scroll List

*2 min read · Updated 23 Apr 2026 Topic 54 of 62 Bookmark Mark done Problem · Topic 54 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/build-infinite-scroll-list](https://www.geekyfrontend.in/learn/react-interview-kit/build-infinite-scroll-list)

---

**Problem:** Build a list that loads more items as the user scrolls to the bottom.

**What This Tests:** Intersection Observer, pagination, useCallback, useRef, loading states.

jsx

jsx

```
function InfiniteScrollList() {
  const [items, setItems] = useState([]);
  const [page, setPage] = useState(1);
  const [loading, setLoading] = useState(false);
  const [hasMore, setHasMore] = useState(true);
  const observerRef = useRef(null);

  const loadMore = useCallback(async () => {
    if (loading || !hasMore) return;
    setLoading(true);

    // Simulate API call
    await new Promise(resolve => setTimeout(resolve, 800));
    const newItems = Array.from({ length: 20 }, (_, i) => ({
      id: (page - 1) * 20 + i + 1,
      title: `Item ${(page - 1) * 20 + i + 1}`,
    }));

    setItems(prev => [...prev, ...newItems]);
    setPage(prev => prev + 1);
    setHasMore(page < 5); // Stop after 5 pages
    setLoading(false);
  }, [page, loading, hasMore]);

  const lastItemRef = useCallback(
    (node) => {
      if (loading) return;
      if (observerRef.current) observerRef.current.disconnect();

      observerRef.current = new IntersectionObserver(([entry]) => {
        if (entry.isIntersecting && hasMore) {
          loadMore();
        }
      });

      if (node) observerRef.current.observe(node);
    },
    [loading, hasMore, loadMore]
  );

  useEffect(() => { loadMore(); }, []);

  return (
    <div style={{ height: 500, overflow: 'auto' }}>
      {items.map((item, index) => (
        <div
          key={item.id}
          ref={index === items.length - 1 ? lastItemRef : null}
          style={{ padding: 16, borderBottom: '1px solid #eee' }}
        >
          {item.title}
        </div>
      ))}
      {loading && <p style={{ textAlign: 'center', padding: 16 }}>Loading more...</p>}
      {!hasMore && <p style={{ textAlign: 'center', padding: 16 }}>No more items.</p>}
    </div>
  );
}
```

**Key Concepts:** Intersection Observer API, callback ref pattern, pagination state, preventing duplicate fetches with loading guard, end-of-data handling.

---

## Learn More

- [Intersection Observer API (MDN) MDN](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)
- [Easiest way to infinite scroll YouTube](https://www.youtube.com/watch?v=nR85ayDEVBc)
- [Infinite scrolling — machine coding round YouTube](https://www.youtube.com/watch?v=MsHbwbeJR8g)
- [Infinite scroll — full tutorial YouTube](https://www.youtube.com/watch?v=JWlOcDus_rs)
