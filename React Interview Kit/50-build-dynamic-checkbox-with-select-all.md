---
title: "Build: Dynamic Checkbox with Select All"
source: https://www.geekyfrontend.in/learn/react-interview-kit/build-dynamic-checkbox-with-select-all
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Build: Dynamic Checkbox with Select All

*1 min read · Updated 23 Apr 2026 Topic 50 of 62 Bookmark Mark done Problem · Topic 50 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/build-dynamic-checkbox-with-select-all](https://www.geekyfrontend.in/learn/react-interview-kit/build-dynamic-checkbox-with-select-all)

---

**Problem:** Display checkboxes with a Select All button. Show selected count and names. Disable the button when all are selected.

**What This Tests:** Array state management, derived state, controlled checkboxes.

jsx

jsx

```
function CheckboxGroup() {
  const [items, setItems] = useState([
    { id: 1, label: 'React', checked: false },
    { id: 2, label: 'Vue', checked: false },
    { id: 3, label: 'Angular', checked: false },
    { id: 4, label: 'Svelte', checked: false },
  ]);

  const allChecked = items.every(item => item.checked);
  const selectedItems = items.filter(item => item.checked);

  const toggleItem = (id) => {
    setItems(prev => prev.map(item =>
      item.id === id ? { ...item, checked: !item.checked } : item
    ));
  };

  const toggleAll = () => {
    const newState = !allChecked;
    setItems(prev => prev.map(item => ({ ...item, checked: newState })));
  };

  return (
    <div>
      {items.map(item => (
        <label key={item.id} style={{ display: 'block' }}>
          <input type="checkbox" checked={item.checked} onChange={() => toggleItem(item.id)} />
          {item.label}
        </label>
      ))}
      <button onClick={toggleAll} disabled={allChecked}>
        {allChecked ? 'All Selected' : 'Select All'}
      </button>
      <p>Selected ({selectedItems.length}): {selectedItems.map(i => i.label).join(', ')}</p>
    </div>
  );
}
```

**Key Concepts:** Derived state (allChecked, selectedItems computed from items, not stored separately), immutable array updates with `.map()`, controlled checkboxes.

---

## Learn More

- [<input> — checkboxes react.dev](https://react.dev/reference/react-dom/components/input)
- [Multiple + select-all checkboxes YouTube](https://www.youtube.com/watch?v=0mJiHsljqsM)
- [Working with checkboxes in React YouTube](https://www.youtube.com/watch?v=WwBu3oykkBs)
- [Select-all checkbox YouTube](https://www.youtube.com/watch?v=mGV9r0wgCrI)
