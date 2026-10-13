---
title: "Build: Accordion / FAQ Component"
source: https://www.geekyfrontend.in/learn/react-interview-kit/build-accordion-faq-component
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Build: Accordion / FAQ Component

*2 min read · Updated 23 Apr 2026 Topic 57 of 62 Bookmark Mark done Problem · Topic 57 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/build-accordion-faq-component](https://www.geekyfrontend.in/learn/react-interview-kit/build-accordion-faq-component)

---

**Problem:** Build an accordion where clicking a question reveals the answer. Only one item should be open at a time.

**What This Tests:** Conditional rendering, single-open logic, component composition.

jsx

jsx

```
function Accordion({ items }) {
  const [openIndex, setOpenIndex] = useState(null);

  const toggle = (index) => {
    setOpenIndex(prev => prev === index ? null : index);
  };

  return (
    <div>
      {items.map((item, index) => (
        <div key={index} style={{ borderBottom: '1px solid #eee', padding: '12px 0' }}>
          <button
            onClick={() => toggle(index)}
            style={{ width: '100%', textAlign: 'left', background: 'none', border: 'none', fontSize: 16, cursor: 'pointer', fontWeight: 'bold', padding: 8 }}
          >
            {openIndex === index ? '▼' : '▶'} {item.question}
          </button>
          {openIndex === index && (
            <div style={{ padding: '8px 8px 8px 24px', color: '#555' }}>
              {item.answer}
            </div>
          )}
        </div>
      ))}
    </div>
  );
}

// Usage
const faqData = [
  { question: 'What is React?', answer: 'A JavaScript library for building UIs.' },
  { question: 'What is JSX?', answer: 'A syntax extension that looks like HTML in JavaScript.' },
  { question: 'What are hooks?', answer: 'Functions that let you use state in functional components.' },
];

<Accordion items={faqData} />
```

**Key Concepts:** Single-open logic with `openIndex === index ? null : index`, data-driven rendering from an items array, conditional content display.

**Extension:** Allow multiple items open simultaneously (use a Set instead of a single index).

---

## Learn More

- [Conditional Rendering react.dev](https://react.dev/learn/conditional-rendering)
- [Build an accordion from scratch YouTube](https://www.youtube.com/watch?v=Y6AfeQIgtVw)
- [Accordion — machine coding YouTube](https://www.youtube.com/watch?v=D8CY_Lhv6rg)
- [React accordion from scratch YouTube](https://www.youtube.com/watch?v=bGpZrr32ECw)
