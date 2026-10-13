---
title: "Build: Pagination Component"
source: https://www.geekyfrontend.in/learn/react-interview-kit/build-pagination-component
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Build: Pagination Component

*2 min read · Updated 23 Apr 2026 Topic 61 of 62 Bookmark Mark done Problem · Topic 61 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/build-pagination-component](https://www.geekyfrontend.in/learn/react-interview-kit/build-pagination-component)

---

**Problem:** Build a pagination component with page numbers, previous/next buttons, and dynamic data display.

**What This Tests:** Pagination math, derived state, controlled current page, edge case handling.

jsx

jsx

```
function Pagination({ totalItems, itemsPerPage = 10, currentPage, onPageChange }) {
  const totalPages = Math.ceil(totalItems / itemsPerPage);

  const getPageNumbers = () => {
    const pages = [];
    const start = Math.max(1, currentPage - 2);
    const end = Math.min(totalPages, currentPage + 2);

    if (start > 1) { pages.push(1); if (start > 2) pages.push('...'); }
    for (let i = start; i <= end; i++) pages.push(i);
    if (end < totalPages) { if (end < totalPages - 1) pages.push('...'); pages.push(totalPages); }

    return pages;
  };

  return (
    <div style={{ display: 'flex', gap: 4, alignItems: 'center' }}>
      <button onClick={() => onPageChange(currentPage - 1)} disabled={currentPage === 1}>Prev</button>

      {getPageNumbers().map((page, i) =>
        page === '...' ? (
          <span key={`dots-${i}`}>...</span>
        ) : (
          <button key={page} onClick={() => onPageChange(page)} style={{ fontWeight: page === currentPage ? 'bold' : 'normal', backgroundColor: page === currentPage ? '#4CAF50' : 'white', color: page === currentPage ? 'white' : 'black' }}>
            {page}
          </button>
        )
      )}

      <button onClick={() => onPageChange(currentPage + 1)} disabled={currentPage === totalPages}>Next</button>

      <span style={{ marginLeft: 8, color: '#666' }}>Page {currentPage} of {totalPages}</span>
    </div>
  );
}

// Usage with data
function UserTable() {
  const [page, setPage] = useState(1);
  const allUsers = Array.from({ length: 95 }, (_, i) => ({ id: i + 1, name: `User ${i + 1}` }));
  const perPage = 10;

  const paginatedUsers = allUsers.slice((page - 1) * perPage, page * perPage);

  return (
    <div>
      <ul>{paginatedUsers.map(u => <li key={u.id}>{u.name}</li>)}</ul>
      <Pagination totalItems={allUsers.length} itemsPerPage={perPage} currentPage={page} onPageChange={setPage} />
    </div>
  );
}
```

**Key Concepts:** Pagination math (totalPages, slice calculation), sliding window for page numbers with ellipsis, disabled prev/next at boundaries, active page highlighting, reusable controlled component

---

## Learn More

- [Rendering Lists react.dev](https://react.dev/learn/rendering-lists)
- [Make pagination in React YouTube](https://www.youtube.com/watch?v=ovG1swpy_RE)
- [Pagination without any libraries YouTube](https://www.youtube.com/watch?v=jmNHcW_oszg)
- [Pagination from scratch YouTube](https://www.youtube.com/watch?v=6DtBw3PaeHs)
