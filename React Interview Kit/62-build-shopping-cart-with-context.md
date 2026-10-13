---
title: "Build: Shopping Cart with Context"
source: https://www.geekyfrontend.in/learn/react-interview-kit/build-shopping-cart-with-context
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Build: Shopping Cart with Context

*3 min read · Updated 23 Apr 2026 Topic 62 of 62 Bookmark Mark done Problem · Topic 62 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/build-shopping-cart-with-context](https://www.geekyfrontend.in/learn/react-interview-kit/build-shopping-cart-with-context)

---

**Problem:** Build a shopping cart with add, remove, update quantity, and total calculation using Context API.

**What This Tests:** Context + useReducer, complex state transitions, derived values (total), component communication.

jsx

jsx

```
const CartContext = createContext();

function cartReducer(state, action) {
  switch (action.type) {
    case 'ADD_ITEM': {
      const existing = state.find(item => item.id === action.product.id);
      if (existing) {
        return state.map(item => item.id === action.product.id ? { ...item, quantity: item.quantity + 1 } : item);
      }
      return [...state, { ...action.product, quantity: 1 }];
    }
    case 'REMOVE_ITEM':
      return state.filter(item => item.id !== action.id);
    case 'UPDATE_QUANTITY':
      if (action.quantity <= 0) return state.filter(item => item.id !== action.id);
      return state.map(item => item.id === action.id ? { ...item, quantity: action.quantity } : item);
    case 'CLEAR':
      return [];
    default:
      return state;
  }
}

function CartProvider({ children }) {
  const [cart, dispatch] = useReducer(cartReducer, []);
  const total = cart.reduce((sum, item) => sum + item.price * item.quantity, 0);
  const itemCount = cart.reduce((sum, item) => sum + item.quantity, 0);

  return (
    <CartContext.Provider value={{ cart, dispatch, total, itemCount }}>
      {children}
    </CartContext.Provider>
  );
}

function useCart() {
  const context = useContext(CartContext);
  if (!context) throw new Error('useCart must be used inside CartProvider');
  return context;
}

// Product list
function ProductList() {
  const products = [
    { id: 1, name: 'React T-Shirt', price: 25 },
    { id: 2, name: 'JavaScript Mug', price: 15 },
    { id: 3, name: 'Node.js Sticker', price: 5 },
  ];
  const { dispatch } = useCart();

  return (
    <div>
      <h2>Products</h2>
      {products.map(product => (
        <div key={product.id} style={{ display: 'flex', justifyContent: 'space-between', padding: 8, borderBottom: '1px solid #eee' }}>
          <span>{product.name} - ${product.price}</span>
          <button onClick={() => dispatch({ type: 'ADD_ITEM', product })}>Add to Cart</button>
        </div>
      ))}
    </div>
  );
}

// Cart display
function Cart() {
  const { cart, dispatch, total, itemCount } = useCart();

  if (cart.length === 0) return <p>Your cart is empty.</p>;

  return (
    <div>
      <h2>Cart ({itemCount} items)</h2>
      {cart.map(item => (
        <div key={item.id} style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', padding: 8, borderBottom: '1px solid #eee' }}>
          <span>{item.name} - ${item.price}</span>
          <div>
            <button onClick={() => dispatch({ type: 'UPDATE_QUANTITY', id: item.id, quantity: item.quantity - 1 })}>-</button>
            <span style={{ margin: '0 8px' }}>{item.quantity}</span>
            <button onClick={() => dispatch({ type: 'UPDATE_QUANTITY', id: item.id, quantity: item.quantity + 1 })}>+</button>
            <button onClick={() => dispatch({ type: 'REMOVE_ITEM', id: item.id })} style={{ marginLeft: 8, color: 'red' }}>Remove</button>
          </div>
        </div>
      ))}
      <h3>Total: ${total.toFixed(2)}</h3>
      <button onClick={() => dispatch({ type: 'CLEAR' })}>Clear Cart</button>
    </div>
  );
}

// App
function App() {
  return (
    <CartProvider>
      <ProductList />
      <Cart />
    </CartProvider>
  );
}
```

**Key Concepts:** Context + useReducer for global state, add-or-increment logic (checking if item exists), derived values (total, itemCount), removing items when quantity reaches 0, custom useCart hook with safety check, Provider pattern, component separation (ProductList and Cart are independent).

---

## Learn More

- [Scaling Up with Reducer and Context react.dev](https://react.dev/learn/scaling-up-with-reducer-and-context)
- [Shopping cart with Context + useReducer YouTube](https://www.youtube.com/watch?v=uMBgUUPkgUY)
- [Shopping cart (Context API + useReducer) YouTube](https://www.youtube.com/watch?v=HptuMAUaNGk)
- [Context API shopping cart (hooks) YouTube](https://www.youtube.com/watch?v=hhAT0CJDWqM)
