---
title: "Build: Star Rating Component"
source: https://www.geekyfrontend.in/learn/react-interview-kit/build-star-rating-component
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Build: Star Rating Component

*1 min read · Updated 23 Apr 2026 Topic 56 of 62 Bookmark Mark done Problem · Topic 56 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/build-star-rating-component](https://www.geekyfrontend.in/learn/react-interview-kit/build-star-rating-component)

---

**Problem:** Build a star rating component with hover preview and click to select.

**What This Tests:** Controlled component, hover state management, dynamic styling.

jsx

jsx

```
function StarRating({ maxStars = 5, onRate }) {
  const [rating, setRating] = useState(0);
  const [hoverRating, setHoverRating] = useState(0);

  const handleClick = (value) => {
    setRating(value);
    if (onRate) onRate(value);
  };

  return (
    <div style={{ display: 'flex', gap: 4, cursor: 'pointer' }}>
      {Array.from({ length: maxStars }, (_, i) => i + 1).map(star => (
        <span
          key={star}
          onClick={() => handleClick(star)}
          onMouseEnter={() => setHoverRating(star)}
          onMouseLeave={() => setHoverRating(0)}
          style={{ fontSize: 32, color: star <= (hoverRating || rating) ? '#FFD700' : '#DDD' }}
        >
          ★
        </span>
      ))}
      <span style={{ marginLeft: 8 }}>{rating} / {maxStars}</span>
    </div>
  );
}
```

**Key Concepts:** Hover state separate from selected state, `hoverRating || rating` for preview fallback, callback prop for parent communication, `Array.from` for dynamic star count.

---

## Learn More

- [State: A Component’s Memory react.dev](https://react.dev/learn/state-a-components-memory)
- [Star rating component — step by step YouTube](https://www.youtube.com/watch?v=MTR0WFKWH78)
- [Star rating (interview question) YouTube](https://www.youtube.com/watch?v=3X8aebi9eA0)
- [Create a star rating component YouTube](https://www.youtube.com/watch?v=9sSBMF8K7sY)
