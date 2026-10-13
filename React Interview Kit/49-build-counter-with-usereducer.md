---
title: "Build: Counter with useReducer"
source: https://www.geekyfrontend.in/learn/react-interview-kit/build-counter-with-usereducer
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Build: Counter with useReducer

*1 min read · Updated 23 Apr 2026 Topic 49 of 62 Bookmark Mark done Problem · Topic 49 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/build-counter-with-usereducer](https://www.geekyfrontend.in/learn/react-interview-kit/build-counter-with-usereducer)

---

**Problem:** Build a counter component with increment, decrement, reset, and set-to-value functionality using useReducer.

**What This Tests:** useReducer pattern, action types, controlled inputs, dispatch.

jsx

jsx

```
function counterReducer(state, action) {
  switch (action.type) {
    case 'INCREMENT': return { count: state.count + 1 };
    case 'DECREMENT': return { count: Math.max(0, state.count - 1) };
    case 'RESET': return { count: 0 };
    case 'SET': return { count: Number(action.payload) || 0 };
    default: return state;
  }
}

function Counter() {
  const [state, dispatch] = useReducer(counterReducer, { count: 0 });
  const [inputValue, setInputValue] = useState('');

  const handleSet = () => {
    dispatch({ type: 'SET', payload: inputValue });
    setInputValue('');
  };

  return (
    <div>
      <h2>Count: {state.count}</h2>
      <button onClick={() => dispatch({ type: 'DECREMENT' })}>-</button>
      <button onClick={() => dispatch({ type: 'RESET' })}>Reset</button>
      <button onClick={() => dispatch({ type: 'INCREMENT' })}>+</button>
      <div>
        <input
          type="number"
          value={inputValue}
          onChange={(e) => setInputValue(e.target.value)}
          placeholder="Set value"
        />
        <button onClick={handleSet}>Set</button>
      </div>
    </div>
  );
}
```

**Key Concepts:** useReducer for structured state, preventing negative values with `Math.max`, controlled input for set-value, separating UI state (inputValue) from domain state (count).

**Extension:** Add step size (increment by 5, 10), add undo/redo with action history.

---

## Learn More

- [useReducer — API reference react.dev](https://react.dev/reference/react/useReducer)
- [Counter with useReducer YouTube](https://www.youtube.com/watch?v=5nB4HfTjPtQ)
- [useReducer counter example YouTube](https://www.youtube.com/watch?v=_56V7csSBI8)
- [useReducer vs useState (counter) YouTube](https://www.youtube.com/watch?v=8mIz-KJE9YE)
