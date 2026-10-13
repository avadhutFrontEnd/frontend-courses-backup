---
title: "SOLID Principles in React"
source: https://www.geekyfrontend.in/learn/react-interview-kit/solid-principles-in-react
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# SOLID Principles in React

*5 min read · Updated 24 Mar 2026 Topic 35 of 62 Bookmark Mark done 6 · Topic 35 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/solid-principles-in-react](https://www.geekyfrontend.in/learn/react-interview-kit/solid-principles-in-react)

---

# 6.6 - SOLID Principles in React

---

## 🎯 What Interviewers Actually Ask

| Question | Level | Frequency |
| --- | --- | --- |
| "What are SOLID principles?" | 🟡🔴 | ★★★★☆ |
| "How do you apply SOLID in React?" | 🔴 | ★★★★☆ |
| "Give an example of SRP in React components" | 🟡🔴 | ★★★☆☆ |

---

## What is SOLID?

SOLID is a set of five design principles from object-oriented programming that help create maintainable, scalable software. While React isn't strictly OOP, these principles map naturally to component-based architecture.

---

## S: Single Responsibility Principle

**Each component should do one thing well.**

A component that fetches data, handles form input, validates, and renders a complex UI is doing too much. Break it into focused parts.

jsx

```
// ❌ One component doing everything
function ProductPage() {
  const [product, setProduct] = useState(null);
  const [reviews, setReviews] = useState([]);
  const [cartCount, setCartCount] = useState(0);
  // ... fetch logic, form handling, validation, rendering all mixed together
}

// ✅ Each component has one responsibility
function ProductPage() {
  return (
    <div>
      <ProductInfo productId={id} />
      <ProductReviews productId={id} />
      <AddToCartButton productId={id} />
    </div>
  );
}
```

---

## O: Open/Closed Principle

**Components should be open for extension but closed for modification.**

Instead of modifying a component every time requirements change, design it to accept new behavior through props.

jsx

```
// ❌ Modifying the component every time a new variant is needed
function Alert({ type }) {
  if (type === 'success') return <div className="green">...</div>;
  if (type === 'error') return <div className="red">...</div>;
  if (type === 'warning') return <div className="yellow">...</div>;
  // Adding a new type means modifying this component
}

// ✅ Open for extension via props
function Alert({ icon, color, children }) {
  return (
    <div style={{ borderColor: color }}>
      {icon && <span>{icon}</span>}
      {children}
    </div>
  );
}

// New variants without touching the Alert component
<Alert color="green" icon="✓">Success!</Alert>
<Alert color="blue" icon="ℹ">Info message</Alert>
```

---

## L: Liskov Substitution Principle

**Components that share a common interface should be interchangeable.**

If you have a `Button`, a `PrimaryButton`, and a `DangerButton`, any of them should work wherever a button is expected. They should all accept `onClick`, `disabled`, `children`, and other standard button props.

jsx

```
// All button variants accept the same core props
function Button({ children, variant = 'default', ...props }) {
  return <button className={`btn btn-${variant}`} {...props}>{children}</button>;
}

function PrimaryButton(props) {
  return <Button variant="primary" {...props} />;
}

function DangerButton(props) {
  return <Button variant="danger" {...props} />;
}

// Any of these can be used interchangeably
<PrimaryButton onClick={save} disabled={loading}>Save</PrimaryButton>
<DangerButton onClick={remove}>Delete</DangerButton>
```

---

## I: Interface Segregation Principle

**Don't force components to accept props they don't use.**

Keep props minimal and specific. A component that takes 15 props is hard to understand and easy to misuse.

jsx

```
// ❌ Component receives far more data than it needs
function ProductCard({ product }) {
  // product has: id, name, price, description, images, reviews, 
  // inventory, supplier, warehouse, shipping...
  // But this card only shows name and price
  return <div>{product.name} - ${product.price}</div>;
}

// ✅ Only receive what you actually render
function ProductCard({ name, price }) {
  return <div>{name} - ${price}</div>;
}
```

---

## D: Dependency Inversion Principle

**Depend on abstractions (props, hooks), not concrete implementations.**

Components shouldn't hardcode how they get data. Pass the data or the data-fetching mechanism as a prop, so you can swap implementations easily (especially useful for testing).

jsx

```
// ❌ Hardcoded data fetching
function UserList() {
  const [users, setUsers] = useState([]);
  useEffect(() => {
    fetch('/api/users').then(r => r.json()).then(setUsers);
  }, []);
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}

// ✅ Data is passed in, component doesn't care where it came from
function UserList({ users }) {
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}

// OR use a custom hook that can be swapped for testing
function UserList() {
  const { users } = useUsers(); // The hook abstracts the data source
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}
```

---

## Quick Reference Table

| Principle | React Translation | One-liner |
| --- | --- | --- |
| **S** Single Responsibility | One component = one job | Split big components into focused ones |
| **O** Open/Closed | Extend via props, not modification | Use props for new variants, not if/else chains |
| **L** Liskov Substitution | Variants share the same API | PrimaryButton works anywhere Button works |
| **I** Interface Segregation | Don't pass unnecessary props | Components receive only what they render |
| **D** Dependency Inversion | Depend on props/hooks, not hardcoded logic | Pass data in, don't fetch inside rendering components |

---

## 🗣️ Interview Answer

> "SOLID principles from OOP map well to React's component model. Single Responsibility means each component does one thing. Open/Closed means I extend behavior through props instead of modifying components. Liskov Substitution means component variants like PrimaryButton and DangerButton share the same interface and are interchangeable. Interface Segregation means I keep props minimal and don't force components to receive data they don't use. Dependency Inversion means components receive data through props or hooks rather than hardcoding data fetching, which makes them testable and reusable."

---

## 📚 Module 6 Complete!

| # | Article | Core Concept |
| --- | --- | --- |
| 6.1 | Container/Presentational | Separate data concerns from visual concerns |
| 6.2 | Higher-Order Components | Wrap components to add behavior (legacy, replaced by hooks) |
| 6.3 | Render Props | Share behavior via function props (legacy, replaced by hooks) |
| 6.4 | Compound Components | Implicit state sharing between related components via Context |
| 6.5 | Provider & Composition | Context-based data sharing, composition over inheritance |
| 6.6 | SOLID Principles in React | Five OOP principles applied to component architecture |

---

> **Part of the React Interview Preparation Kit** — [Back to Course Roadmap →](https://claude.ai/00-course-roadmap.md)

---

## Learn More

- [SOLID principles (overview) wikipedia.org](https://en.wikipedia.org/wiki/SOLID)
- [Thinking in React react.dev](https://react.dev/learn/thinking-in-react)
- [React clean code — SOLID examples YouTube](https://www.youtube.com/watch?v=t_h_A6RkM7A)
- [The right way to write React (SOLID) YouTube](https://www.youtube.com/watch?v=MSq_DCRxOxw)
- [SOLID for interviews YouTube](https://www.youtube.com/watch?v=GFtKI2BQOJY)
