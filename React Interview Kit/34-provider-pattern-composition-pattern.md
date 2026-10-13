---
title: "Provider Pattern & Composition Pattern"
source: https://www.geekyfrontend.in/learn/react-interview-kit/provider-pattern-composition-pattern
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Provider Pattern & Composition Pattern

*3 min read · Updated 24 Mar 2026 Topic 34 of 62 Bookmark Mark done 6 · Topic 34 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/provider-pattern-composition-pattern](https://www.geekyfrontend.in/learn/react-interview-kit/provider-pattern-composition-pattern)

---

# 6.5 - Provider Pattern & Composition Pattern

---

## 🎯 What Interviewers Actually Ask

| Question | Level | Frequency |
| --- | --- | --- |
| "What is the Provider pattern in React?" | 🟡🔴 | ★★★★☆ |
| "What is composition over inheritance?" | 🟡🔴 | ★★★★☆ |
| "How do you use the children prop for composition?" | 🟡 | ★★★☆☆ |

---

## Provider Pattern

The Provider pattern uses React's Context API to make data available to any component in a subtree without prop drilling. The "provider" wraps a section of the component tree and supplies a value. Any descendant can consume it.

This pattern is used by virtually every React library that needs to share state: React Redux (`<Provider store={store}>`), React Router (`<BrowserRouter>`), Theme providers, Auth providers, and internationalization (i18n) providers.

jsx

```
const ThemeContext = createContext('light');

function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  const toggleTheme = () => setTheme(t => t === 'light' ? 'dark' : 'light');

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) throw new Error('useTheme must be used inside ThemeProvider');
  return context;
}

// Wrap the app
<ThemeProvider>
  <App />
</ThemeProvider>

// Use anywhere inside
function Header() {
  const { theme, toggleTheme } = useTheme();
  return (
    <header className={theme}>
      <button onClick={toggleTheme}>Toggle Theme</button>
    </header>
  );
}
```

The custom `useTheme` hook adds a safety check and gives consumers a clean API. This pattern of Provider + custom hook is the standard approach in modern React.

---

## Composition Pattern

Composition in React means building complex UI by combining simpler components, using the `children` prop and named "slot" props instead of inheritance.

### Using `children` for flexible content

jsx

```
function Card({ children }) {
  return <div className="card">{children}</div>;
}

// Same Card, completely different content
<Card>
  <h2>User Profile</h2>
  <p>Alice, 28 years old</p>
</Card>

<Card>
  <img src="/product.jpg" alt="Product" />
  <h3>$29.99</h3>
  <button>Add to Cart</button>
</Card>
```

### Named slots for multi-section layouts

jsx

```
function Dialog({ title, content, actions }) {
  return (
    <div className="dialog-overlay">
      <div className="dialog">
        <div className="dialog-title">{title}</div>
        <div className="dialog-content">{content}</div>
        <div className="dialog-actions">{actions}</div>
      </div>
    </div>
  );
}

<Dialog
  title={<h2>Confirm Deletion</h2>}
  content={<p>This action cannot be undone.</p>}
  actions={
    <>
      <button onClick={onCancel}>Cancel</button>
      <button onClick={onConfirm}>Delete</button>
    </>
  }
/>
```

### Specialization through composition (not inheritance)

jsx

```
// Base component
function Button({ children, variant = 'default', ...props }) {
  return <button className={`btn btn-${variant}`} {...props}>{children}</button>;
}

// Specialized versions through composition, not inheritance
function PrimaryButton(props) {
  return <Button variant="primary" {...props} />;
}

function DangerButton(props) {
  return <Button variant="danger" {...props} />;
}

function IconButton({ icon, children, ...props }) {
  return <Button {...props}><span className="icon">{icon}</span> {children}</Button>;
}
```

This is React's alternative to class inheritance. The React team explicitly recommends composition over inheritance for all code reuse scenarios.

**Interview answer:** "The Provider pattern uses Context to share data across a component tree without prop drilling. I implement it as a Provider component paired with a custom hook (like ThemeProvider + useTheme) for a clean API. The Composition pattern builds complex UI by combining simpler components using the children prop and named slot props. Instead of creating Button subclasses through inheritance, I create specialized components like PrimaryButton and DangerButton that compose the base Button with different props. React explicitly recommends composition over inheritance."

---

> **Part of the React Interview Preparation Kit** — [Back to Course Roadmap →](https://claude.ai/00-course-roadmap.md)

---

## Learn More

- [Passing Data Deeply with Context react.dev](https://react.dev/learn/passing-data-deeply-with-context)
- [Passing Props (composition & children) react.dev](https://react.dev/learn/passing-props-to-a-component)
- [The Provider pattern YouTube](https://www.youtube.com/watch?v=xPlIaTJTsOw)
- [Composition pattern in React YouTube](https://www.youtube.com/watch?v=GBh59sRi8qQ)
- [Context API & Provider pattern YouTube](https://www.youtube.com/watch?v=Kdxkn7HM26s)
