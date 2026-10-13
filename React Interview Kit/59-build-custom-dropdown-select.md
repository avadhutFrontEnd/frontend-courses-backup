---
title: "Build: Custom Dropdown / Select"
source: https://www.geekyfrontend.in/learn/react-interview-kit/build-custom-dropdown-select
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Build: Custom Dropdown / Select

*2 min read · Updated 23 Apr 2026 Topic 59 of 62 Bookmark Mark done Problem · Topic 59 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/build-custom-dropdown-select](https://www.geekyfrontend.in/learn/react-interview-kit/build-custom-dropdown-select)

---

**Problem:** Build a custom dropdown with keyboard navigation, outside click to close, and accessible markup.

**What This Tests:** Refs, keyboard events, click-outside pattern, controlled component, accessibility.

jsx

jsx

```
function Dropdown({ options, value, onChange, placeholder = 'Select...' }) {
  const [isOpen, setIsOpen] = useState(false);
  const [highlightedIndex, setHighlightedIndex] = useState(-1);
  const containerRef = useRef(null);

  useEffect(() => {
    const handleClickOutside = (e) => {
      if (containerRef.current && !containerRef.current.contains(e.target)) {
        setIsOpen(false);
      }
    };
    document.addEventListener('mousedown', handleClickOutside);
    return () => document.removeEventListener('mousedown', handleClickOutside);
  }, []);

  const handleKeyDown = (e) => {
    if (!isOpen && (e.key === 'Enter' || e.key === 'ArrowDown')) {
      setIsOpen(true);
      return;
    }
    if (e.key === 'Escape') { setIsOpen(false); return; }
    if (e.key === 'ArrowDown') { setHighlightedIndex(prev => Math.min(prev + 1, options.length - 1)); }
    if (e.key === 'ArrowUp') { setHighlightedIndex(prev => Math.max(prev - 1, 0)); }
    if (e.key === 'Enter' && highlightedIndex >= 0) {
      onChange(options[highlightedIndex]);
      setIsOpen(false);
    }
  };

  const selectedLabel = options.find(o => o.value === value)?.label || placeholder;

  return (
    <div ref={containerRef} onKeyDown={handleKeyDown} tabIndex={0} style={{ position: 'relative', width: 200 }}>
      <div onClick={() => setIsOpen(!isOpen)} style={{
        padding: '8px 12px', border: '1px solid #ccc', borderRadius: 4, cursor: 'pointer',
        display: 'flex', justifyContent: 'space-between',
      }}>
        <span>{selectedLabel}</span>
        <span>{isOpen ? '▲' : '▼'}</span>
      </div>

      {isOpen && (
        <ul style={{
          position: 'absolute', top: '100%', left: 0, right: 0,
          border: '1px solid #ccc', borderRadius: 4, background: 'white',
          listStyle: 'none', margin: 0, padding: 0, maxHeight: 200, overflow: 'auto',
          zIndex: 10,
        }}>
          {options.map((option, index) => (
            <li
              key={option.value}
              onClick={() => { onChange(option); setIsOpen(false); }}
              onMouseEnter={() => setHighlightedIndex(index)}
              style={{
                padding: '8px 12px', cursor: 'pointer',
                backgroundColor: index === highlightedIndex ? '#f0f0f0' : option.value === value ? '#e8f4fd' : 'white',
              }}
            >
              {option.label}
            </li>
          ))}
        </ul>
      )}
    </div>
  );
}

// Usage
const options = [
  { value: 'react', label: 'React' },
  { value: 'vue', label: 'Vue' },
  { value: 'angular', label: 'Angular' },
];

function App() {
  const [selected, setSelected] = useState('');
  return <Dropdown options={options} value={selected} onChange={(opt) => setSelected(opt.value)} />;
}
```

**Key Concepts:** Click-outside with ref and document listener, keyboard navigation (Arrow Up/Down, Enter, Escape), highlighted index tracking, controlled value, tabIndex for focus management.

---

## Learn More

- [Manipulating the DOM with Refs (click-outside) react.dev](https://react.dev/learn/manipulating-the-dom-with-refs)
- [Build a custom select/dropdown YouTube](https://www.youtube.com/watch?v=n_VdjuKvQ_Y)
- [Custom dropdown menu component YouTube](https://www.youtube.com/watch?v=t8JK5bVoVBw)
- [React custom dropdown menu YouTube](https://www.youtube.com/watch?v=Lg5kr69CzpE)
