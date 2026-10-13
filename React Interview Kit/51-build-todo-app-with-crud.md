---
title: "Build: Todo App with CRUD"
source: https://www.geekyfrontend.in/learn/react-interview-kit/build-todo-app-with-crud
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Build: Todo App with CRUD

*2 min read · Updated 23 Apr 2026 Topic 51 of 62 Bookmark Mark done Problem · Topic 51 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/build-todo-app-with-crud](https://www.geekyfrontend.in/learn/react-interview-kit/build-todo-app-with-crud)

---

**Problem:** Build a todo app with add, toggle complete, edit, and delete. Include a filter (all/active/completed).

**What This Tests:** Full CRUD operations, filtering, immutable state updates, useReducer.

jsx

jsx

```
function todoReducer(state, action) {
  switch (action.type) {
    case 'ADD':
      return [...state, { id: Date.now(), text: action.text, done: false }];
    case 'TOGGLE':
      return state.map(t => t.id === action.id ? { ...t, done: !t.done } : t);
    case 'DELETE':
      return state.filter(t => t.id !== action.id);
    case 'EDIT':
      return state.map(t => t.id === action.id ? { ...t, text: action.text } : t);
    default:
      return state;
  }
}

function TodoApp() {
  const [todos, dispatch] = useReducer(todoReducer, []);
  const [input, setInput] = useState('');
  const [filter, setFilter] = useState('all');
  const [editingId, setEditingId] = useState(null);
  const [editText, setEditText] = useState('');

  const handleAdd = () => {
    if (!input.trim()) return;
    dispatch({ type: 'ADD', text: input.trim() });
    setInput('');
  };

  const handleKeyDown = (e) => {
    if (e.key === 'Enter') handleAdd();
  };

  const startEdit = (todo) => {
    setEditingId(todo.id);
    setEditText(todo.text);
  };

  const saveEdit = (id) => {
    if (editText.trim()) {
      dispatch({ type: 'EDIT', id, text: editText.trim() });
    }
    setEditingId(null);
  };

  const filtered = todos.filter(todo => {
    if (filter === 'active') return !todo.done;
    if (filter === 'completed') return todo.done;
    return true;
  });

  return (
    <div>
      <div>
        <input value={input} onChange={e => setInput(e.target.value)} onKeyDown={handleKeyDown} placeholder="Add a todo..." />
        <button onClick={handleAdd} disabled={!input.trim()}>Add</button>
      </div>

      <div>
        {['all', 'active', 'completed'].map(f => (
          <button key={f} onClick={() => setFilter(f)} style={{ fontWeight: filter === f ? 'bold' : 'normal' }}>
            {f.charAt(0).toUpperCase() + f.slice(1)}
          </button>
        ))}
      </div>

      {filtered.length === 0 ? (
        <p>{filter === 'all' ? 'No todos yet. Add one above!' : `No ${filter} todos.`}</p>
      ) : (
        <ul>
          {filtered.map(todo => (
            <li key={todo.id}>
              {editingId === todo.id ? (
                <>
                  <input value={editText} onChange={e => setEditText(e.target.value)} onKeyDown={e => e.key === 'Enter' && saveEdit(todo.id)} autoFocus />
                  <button onClick={() => saveEdit(todo.id)}>Save</button>
                  <button onClick={() => setEditingId(null)}>Cancel</button>
                </>
              ) : (
                <>
                  <span onClick={() => dispatch({ type: 'TOGGLE', id: todo.id })} style={{ textDecoration: todo.done ? 'line-through' : 'none', cursor: 'pointer' }}>
                    {todo.text}
                  </span>
                  <button onClick={() => startEdit(todo)}>Edit</button>
                  <button onClick={() => dispatch({ type: 'DELETE', id: todo.id })}>Delete</button>
                </>
              )}
            </li>
          ))}
        </ul>
      )}

      <p>{todos.filter(t => !t.done).length} items remaining</p>
    </div>
  );
}
```

**Key Concepts:** useReducer for CRUD, inline editing with separate edit state, filtering with derived state, Enter key handling, empty state, disabled button for empty input.

---

## Learn More

- [Updating Arrays in State react.dev](https://react.dev/learn/updating-arrays-in-state)
- [React todo list (CRUD) YouTube](https://www.youtube.com/watch?v=Q3EnrxAmLdM)
- [Modern React CRUD todo app YouTube](https://www.youtube.com/watch?v=7u2Rv4HfCYQ)
- [Todo app from scratch (hooks) YouTube](https://www.youtube.com/watch?v=dD0MdMRVHoo)
