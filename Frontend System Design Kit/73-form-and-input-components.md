---
title: "Form and input components"
source: https://www.geekyfrontend.in/learn/complete-frontend-kit/form-and-input-components
course: Frontend System Design Kit
tags: [geeky-frontend, frontend-system-design-kit]
---

# Form and input components

*5 min read · Updated 4 Jun 2026 Topic 73 of 77 Bookmark Mark done 24C · Topic 73 of 77*

**Source:** [https://www.geekyfrontend.in/learn/complete-frontend-kit/form-and-input-components](https://www.geekyfrontend.in/learn/complete-frontend-kit/form-and-input-components)

---

# 24C. Form & Input Components

**Problems:** 9 (Q 24.14 – Q 24.22)

---

## Q 24.14 Autocomplete / Typeahead Search

**The most frequently asked machine coding problem.**

jsx

```
function Autocomplete({ fetchSuggestions, onSelect }) {
  const [query, setQuery] = useState('');
  const [suggestions, setSuggestions] = useState([]);
  const [highlightedIndex, setHighlightedIndex] = useState(-1);
  const [isOpen, setIsOpen] = useState(false);
  const [loading, setLoading] = useState(false);
  const controllerRef = useRef(null);

  useEffect(() => {
    if (!query.trim()) { setSuggestions([]); return; }
    controllerRef.current?.abort();
    controllerRef.current = new AbortController();

    const timer = setTimeout(async () => {
      setLoading(true);
      try {
        const results = await fetchSuggestions(query, controllerRef.current.signal);
        setSuggestions(results);
        setIsOpen(true);
        setHighlightedIndex(-1);
      } catch (e) {
        if (e.name !== 'AbortError') setSuggestions([]);
      } finally { setLoading(false); }
    }, 300);

    return () => clearTimeout(timer);
  }, [query]);

  function handleKeyDown(e) {
    if (!isOpen) return;
    if (e.key === 'ArrowDown') {
      e.preventDefault();
      setHighlightedIndex(prev => Math.min(prev + 1, suggestions.length - 1));
    }
    if (e.key === 'ArrowUp') {
      e.preventDefault();
      setHighlightedIndex(prev => Math.max(prev - 1, 0));
    }
    if (e.key === 'Enter' && highlightedIndex >= 0) {
      onSelect(suggestions[highlightedIndex]);
      setIsOpen(false);
      setQuery(suggestions[highlightedIndex].name);
    }
    if (e.key === 'Escape') setIsOpen(false);
  }

  return (
    <div className="autocomplete" role="combobox" aria-expanded={isOpen}>
      <input value={query} onChange={(e) => setQuery(e.target.value)}
        onKeyDown={handleKeyDown} onFocus={() => suggestions.length && setIsOpen(true)}
        aria-autocomplete="list"
        aria-activedescendant={highlightedIndex >= 0 ? `suggestion-${highlightedIndex}` : undefined} />
      {loading && <span className="loading-indicator">Loading...</span>}
      {isOpen && suggestions.length > 0 && (
        <ul role="listbox">
          {suggestions.map((item, i) => (
            <li key={item.id} id={`suggestion-${i}`} role="option"
              aria-selected={i === highlightedIndex}
              className={i === highlightedIndex ? 'highlighted' : ''}
              onClick={() => { onSelect(item); setIsOpen(false); setQuery(item.name); }}>
              <HighlightMatch text={item.name} query={query} />
            </li>
          ))}
        </ul>
      )}
    </div>
  );
}

function HighlightMatch({ text, query }) {
  const parts = text.split(new RegExp(`(${query})`, 'gi'));
  return parts.map((part, i) =>
    part.toLowerCase() === query.toLowerCase() ? <mark key={i}>{part}</mark> : <span key={i}>{part}</span>
  );
}
```

**Edge cases:** Empty results ("No results found"), API errors (retry), very long text (truncate), click outside to close.

---

## Q 24.15 Multi-select Dropdown with Search

Multiple items selectable, selected shown as chips, search filters options, remove with ✕ or Backspace. `aria-multiselectable="true"` on listbox.

---

## Q 24.16 OTP Input Component

jsx

```
function OTPInput({ length = 6, onComplete }) {
  const [values, setValues] = useState(Array(length).fill(''));
  const inputsRef = useRef([]);

  function handleChange(index, value) {
    if (!/^\d*$/.test(value)) return;
    const next = [...values];
    next[index] = value.slice(-1);
    setValues(next);
    if (value && index < length - 1) inputsRef.current[index + 1].focus();
    if (next.every(v => v)) onComplete(next.join(''));
  }

  function handleKeyDown(index, e) {
    if (e.key === 'Backspace' && !values[index] && index > 0)
      inputsRef.current[index - 1].focus();
  }

  function handlePaste(e) {
    const pasted = e.clipboardData.getData('text').slice(0, length);
    if (!/^\d+$/.test(pasted)) return;
    const next = pasted.split('').concat(Array(length).fill('')).slice(0, length);
    setValues(next);
    inputsRef.current[Math.min(pasted.length, length - 1)].focus();
    if (next.every(v => v)) onComplete(next.join(''));
  }

  return (
    <div className="otp-input" onPaste={handlePaste}>
      {values.map((val, i) => (
        <input key={i} ref={el => inputsRef.current[i] = el} value={val}
          onChange={e => handleChange(i, e.target.value)} onKeyDown={e => handleKeyDown(i, e)}
          maxLength={1} inputMode="numeric" autoComplete={i === 0 ? 'one-time-code' : 'off'} />
      ))}
    </div>
  );
}
```

---

## Q 24.17 Multi-step Form with Validation

jsx

```
function MultiStepForm() {
  const [step, setStep] = useState(0);
  const [formData, setFormData] = useState({ name: '', email: '', address: '', payment: '' });

  const steps = [
    { component: PersonalInfo, validate: (d) => d.name && d.email },
    { component: AddressInfo, validate: (d) => d.address },
    { component: PaymentInfo, validate: (d) => d.payment },
    { component: ReviewStep, validate: () => true },
  ];

  function next() { if (steps[step].validate(formData)) setStep(s => s + 1); }

  const StepComponent = steps[step].component;

  return (
    <div>
      <ProgressBar current={step} total={steps.length} />
      <StepComponent data={formData} onChange={setFormData} />
      <div className="actions">
        {step > 0 && <button onClick={() => setStep(s => s - 1)}>Back</button>}
        {step < steps.length - 1
          ? <button onClick={next}>Next</button>
          : <button onClick={() => submitForm(formData)}>Submit</button>}
      </div>
    </div>
  );
}
```

---

## Q 24.18 – Q 24.22 (Date Picker, Color Picker, Range Slider, Rich Text Editor, File Uploader)

**Date Picker (24.18):** Calendar grid with month/year navigation. Keyboard: arrow keys between days, Enter to select. Hardest part: generating the calendar grid correctly (first day padding, days in month).

**Color Picker (24.19):** Hue slider + saturation/brightness 2D picker + hex/RGB inputs. Use HSL internally, convert to hex for output.

**Range Slider (24.20):** Single or dual knob. Track mouse/touch events, convert pixel → value. `role="slider"`, `aria-valuenow`, `aria-valuemin`, `aria-valuemax`.

**Rich Text Editor (24.21):** `contenteditable` div with `document.execCommand` for basic formatting. For production, use TipTap or Slate.

**File Uploader (24.22):**

jsx

```
function FileUploader({ maxSize = 5 * 1024 * 1024, accept = 'image/*' }) {
  const [files, setFiles] = useState([]);
  const [dragOver, setDragOver] = useState(false);

  function handleFiles(fileList) {
    const valid = Array.from(fileList).filter(f => {
      if (f.size > maxSize) { alert(`${f.name} exceeds limit`); return false; }
      return true;
    });
    setFiles(prev => [...prev, ...valid.map(f => ({
      file: f,
      preview: f.type.startsWith('image/') ? URL.createObjectURL(f) : null,
      progress: 0, status: 'pending',
    }))]);
  }

  return (
    <div className={`dropzone ${dragOver ? 'active' : ''}`}
      onDragOver={(e) => { e.preventDefault(); setDragOver(true); }}
      onDragLeave={() => setDragOver(false)}
      onDrop={(e) => { e.preventDefault(); setDragOver(false); handleFiles(e.dataTransfer.files); }}>
      <p>Drag files here or <label>browse<input type="file" hidden multiple accept={accept}
        onChange={(e) => handleFiles(e.target.files)} /></label></p>
      {files.map((f, i) => (
        <div key={i} className="file-preview">
          {f.preview && <img src={f.preview} alt="" width={60} height={60} />}
          <span>{f.file.name}</span>
          <progress value={f.progress} max={100} />
        </div>
      ))}
    </div>
  );
}
```

---

## Learn More

- [<input> (MDN / React) react.dev](https://react.dev/reference/react-dom/components/input)
- [React Hook Form react-hook-form.com](https://react-hook-form.com)
- [Reusable input field component YouTube](https://www.youtube.com/watch?v=HIFxC7Gkgfo)
- [Reusable form input UI component YouTube](https://www.youtube.com/watch?v=0uK9z3_WElY)
- [Reusable create/edit form (RHF + Zod) YouTube](https://www.youtube.com/watch?v=_XQ_9qaQsQU)
