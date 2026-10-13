---
title: "Build: Modal / Dialog Component"
source: https://www.geekyfrontend.in/learn/react-interview-kit/build-modal-dialog-component
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Build: Modal / Dialog Component

*2 min read · Updated 23 Apr 2026 Topic 58 of 62 Bookmark Mark done Problem · Topic 58 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/build-modal-dialog-component](https://www.geekyfrontend.in/learn/react-interview-kit/build-modal-dialog-component)

---

**Problem:** Build a reusable modal with overlay, close on escape key, and close on outside click.

**What This Tests:** Portals, event listeners, cleanup, the children prop, focus management.

jsx

jsx

```
function Modal({ isOpen, onClose, title, children }) {
  const modalRef = useRef(null);

  useEffect(() => {
    const handleEscape = (e) => {
      if (e.key === 'Escape') onClose();
    };

    if (isOpen) {
      document.addEventListener('keydown', handleEscape);
      document.body.style.overflow = 'hidden'; // Prevent background scroll
    }

    return () => {
      document.removeEventListener('keydown', handleEscape);
      document.body.style.overflow = '';
    };
  }, [isOpen, onClose]);

  const handleOverlayClick = (e) => {
    if (e.target === e.currentTarget) onClose(); // Only close if clicking the overlay itself
  };

  if (!isOpen) return null;

  return ReactDOM.createPortal(
    <div onClick={handleOverlayClick} style={{
      position: 'fixed', top: 0, left: 0, right: 0, bottom: 0,
      backgroundColor: 'rgba(0,0,0,0.5)',
      display: 'flex', alignItems: 'center', justifyContent: 'center',
      zIndex: 1000,
    }}>
      <div ref={modalRef} style={{
        background: 'white', borderRadius: 8, padding: 24,
        minWidth: 320, maxWidth: 500, position: 'relative',
      }}>
        <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginBottom: 16 }}>
          <h2 style={{ margin: 0 }}>{title}</h2>
          <button onClick={onClose} style={{ border: 'none', background: 'none', fontSize: 20, cursor: 'pointer' }}>✕</button>
        </div>
        {children}
      </div>
    </div>,
    document.body
  );
}

// Usage
function App() {
  const [showModal, setShowModal] = useState(false);

  return (
    <div>
      <button onClick={() => setShowModal(true)}>Open Modal</button>
      <Modal isOpen={showModal} onClose={() => setShowModal(false)} title="Confirm Action">
        <p>Are you sure you want to proceed?</p>
        <button onClick={() => setShowModal(false)}>Cancel</button>
        <button onClick={() => { alert('Confirmed!'); setShowModal(false); }}>Confirm</button>
      </Modal>
    </div>
  );
}
```

**Key Concepts:** `createPortal` for rendering outside the DOM hierarchy, Escape key listener with cleanup, overlay click detection with `e.target === e.currentTarget`, scroll lock on body, reusable via children prop.

---

## Learn More

- [createPortal — for modals react.dev](https://react.dev/reference/react-dom/createPortal)
- [Reusable modal component with hooks YouTube](https://www.youtube.com/watch?v=46F4S-m-XEQ)
- [Accessible modals with <dialog> YouTube](https://www.youtube.com/watch?v=5zrwR4vqw5M)
- [Best way to add popup modals YouTube](https://www.youtube.com/watch?v=FSY2A0vzwko)
