---
title: "Compound Component Pattern"
source: https://www.geekyfrontend.in/learn/react-interview-kit/compound-component-pattern
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Compound Component Pattern

*2 min read · Updated 24 Mar 2026 Topic 33 of 62 Bookmark Mark done 6 · Topic 33 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/compound-component-pattern](https://www.geekyfrontend.in/learn/react-interview-kit/compound-component-pattern)

---

# 6.4 - Compound Component Pattern

---

## 🎯 What Interviewers Actually Ask

| Question | Level | Frequency |
| --- | --- | --- |
| "What is the Compound Component pattern?" | 🔴 | ★★★☆☆ |
| "How do compound components share state?" | 🔴 | ★★★☆☆ |
| "Give an example of compound components" | 🔴 | ★★★☆☆ |

---

## The Pattern

Compound components are a set of components that work together to accomplish a single task, sharing implicit state between them. The parent component manages the state, and children access it through Context, without the consumer needing to wire up props manually.

Think of `<select>` and `<option>` in HTML. They're separate elements but work together implicitly. The `<select>` manages which option is selected, and each `<option>` participates without explicit prop passing.

## Building a Compound Tab Component

jsx

```
const TabsContext = createContext();

function Tabs({ children, defaultIndex = 0 }) {
  const [activeIndex, setActiveIndex] = useState(defaultIndex);

  return (
    <TabsContext.Provider value={{ activeIndex, setActiveIndex }}>
      <div className="tabs">{children}</div>
    </TabsContext.Provider>
  );
}

function TabList({ children }) {
  return <div className="tab-list" role="tablist">{children}</div>;
}

function Tab({ children, index }) {
  const { activeIndex, setActiveIndex } = useContext(TabsContext);

  return (
    <button
      role="tab"
      className={activeIndex === index ? 'tab active' : 'tab'}
      onClick={() => setActiveIndex(index)}
    >
      {children}
    </button>
  );
}

function TabPanels({ children }) {
  const { activeIndex } = useContext(TabsContext);
  return <div className="tab-panels">{children[activeIndex]}</div>;
}

function TabPanel({ children }) {
  return <div role="tabpanel">{children}</div>;
}
```

### The beauty is in the usage

jsx

```
<Tabs defaultIndex={0}>
  <TabList>
    <Tab index={0}>Profile</Tab>
    <Tab index={1}>Settings</Tab>
    <Tab index={2}>Billing</Tab>
  </TabList>

  <TabPanels>
    <TabPanel><ProfilePage /></TabPanel>
    <TabPanel><SettingsPage /></TabPanel>
    <TabPanel><BillingPage /></TabPanel>
  </TabPanels>
</Tabs>
```

The consumer doesn't pass `activeIndex` or `setActiveIndex` anywhere. The components communicate implicitly through Context. This is what makes compound components feel natural and declarative.

## Where You See This Pattern

Libraries that use compound components include: Headless UI (`<Menu>`, `<Listbox>`), Reach UI, Radix UI, and Chakra UI's `<Accordion>`, `<Tabs>`, `<Menu>`.

**Interview answer:** "Compound components are a group of components that share implicit state through Context. The parent manages the state, and children consume it without the user needing to pass props explicitly. It's like how HTML's `<select>` and `<option>` work together. I've used this pattern to build tabs, accordions, and dropdown menus where the API should be declarative and composable. Libraries like Headless UI and Radix use this pattern extensively."

---

> **Part of the React Interview Preparation Kit** — [Back to Course Roadmap →](https://claude.ai/00-course-roadmap.md)

---

## Learn More

- [Compound Pattern (patterns.dev) patterns.dev](https://www.patterns.dev/react/compound-pattern)
- [Compound components pattern YouTube](https://www.youtube.com/watch?v=PPOKvugfi98)
- [The magic of compound components YouTube](https://www.youtube.com/watch?v=WSM2MpJIwFg)
- [Compound components (design patterns) YouTube](https://www.youtube.com/watch?v=N_WgBU3S9W8)
