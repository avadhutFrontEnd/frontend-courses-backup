---
title: "Linear data structures"
source: https://www.geekyfrontend.in/learn/complete-frontend-kit/linear-data-structures
course: Frontend System Design Kit
tags: [geeky-frontend, frontend-system-design-kit]
---

# Linear data structures

*7 min read · Updated 3 Jun 2026 Topic 55 of 77 Bookmark Mark done 22A · Topic 55 of 77*

**Source:** [https://www.geekyfrontend.in/learn/complete-frontend-kit/linear-data-structures](https://www.geekyfrontend.in/learn/complete-frontend-kit/linear-data-structures)

---

# 22A. Linear Data Structures

**Questions:** 7 **Difficulty:** Junior to Mid

---

## Q 22.1 Arrays — what are the time complexities and frontend use cases?

**Difficulty:** Junior **Asked at:** Every company

Arrays are the most used data structure in frontend. Every list, grid, table, and collection is an array.

**Time complexities:**

javascript

```
Operation          │ Time      │ Notes
  ───────────────────┼───────────┼──────────────────────────────────
  Access by index    │ O(1)      │ arr[5] — instant
  Push (end)         │ O(1)*     │ Amortized — occasional resize
  Pop (end)          │ O(1)      │
  Unshift (start)    │ O(n)      │ Shifts every element right
  Shift (start)      │ O(n)      │ Shifts every element left
  Insert (middle)    │ O(n)      │ splice — shifts elements
  Delete (middle)    │ O(n)      │ splice — shifts elements
  Search (unsorted)  │ O(n)      │ Linear scan
  Search (sorted)    │ O(log n)  │ Binary search
  Sort               │ O(n log n)│ Tim Sort in V8
  includes/indexOf   │ O(n)      │ Linear scan
  filter/map/reduce  │ O(n)      │ Visits every element
```

**Frontend-relevant patterns:**

javascript

```
// Immutable update (React state) — spread creates a new array
const newItems = [...items, newItem];                        // Add to end
const filtered = items.filter(item => item.id !== removeId); // Remove by id
const updated = items.map(item =>
  item.id === targetId ? { ...item, done: true } : item      // Update one item
);

// When arrays are the wrong choice:
// Frequent lookups by key → use Map or object
// Checking membership → use Set
// Need O(1) delete from middle → use linked list or Map
```

**Follow-up:** "When would you use a Map instead of an array?"

When you need O(1) lookup by key. An array of users where you frequently find by `userId` is O(n) per lookup. A `Map<string, User>` is O(1). Convert the array to a Map for repeated lookups, or use both (array for ordered rendering, Map for lookups).

---

## Q 22.2 Strings — what manipulation patterns matter for UI work?

**Difficulty:** Junior **Asked at:** Amazon, Flipkart, Atlassian

Strings in JavaScript are immutable — every manipulation creates a new string. This matters for performance when building large strings incrementally.

**Common frontend string operations:**

javascript

```
// Truncation with ellipsis (product descriptions, card summaries)
function truncate(str, maxLength) {
  if (str.length <= maxLength) return str;
  return str.slice(0, maxLength - 3) + '...';
}

// Slug generation (URLs from titles)
function slugify(text) {
  return text
    .toLowerCase()
    .replace(/[^\w\s-]/g, '')    // Remove special chars
    .replace(/\s+/g, '-')        // Spaces to hyphens
    .replace(/-+/g, '-')         // Collapse multiple hyphens
    .trim();
}
// "Hello World! 2026" → "hello-world-2026"

// Search highlighting (marking matched text)
function highlightMatch(text, query) {
  if (!query) return text;
  const regex = new RegExp(`(${escapeRegex(query)})`, 'gi');
  return text.replace(regex, '<mark>$1</mark>');
}

// Template literal for HTML generation (careful — XSS risk)
// Use textContent for user input, innerHTML only with sanitization
```

**Performance note:** String concatenation in a loop (`str += chunk`) is optimized in modern V8 — but for very large strings (100KB+), using an array and `.join('')` at the end is measurably faster.

---

## Q 22.3 Stacks — where do they appear in frontend?

**Difficulty:** Junior **Asked at:** Amazon, Flipkart, Google

A stack is LIFO (Last In, First Out). JavaScript arrays work as stacks with `push()` and `pop()`.

javascript

```
class Stack {
  #items = [];
  push(item) { this.#items.push(item); }
  pop() { return this.#items.pop(); }
  peek() { return this.#items[this.#items.length - 1]; }
  isEmpty() { return this.#items.length === 0; }
  get size() { return this.#items.length; }
}
```

**Frontend use cases:**

- **Undo/Redo** — undo stack holds past states, redo stack holds undone states (Chapter 9, Q9.11)
- **Browser history** — back button pops from history stack
- **Balanced parentheses** — validating code/math expressions
- **Function call stack** — how JavaScript executes nested function calls
- **DFS traversal** — iterative depth-first search uses an explicit stack

---

## Q 22.4 Queues — where do they appear in frontend?

**Difficulty:** Junior **Asked at:** Amazon, Flipkart

A queue is FIFO (First In, First Out). JavaScript doesn't have a built-in Queue class, but arrays work with `push()` + `shift()` (though `shift()` is O(n)).

javascript

```
// Efficient queue using object (O(1) for both enqueue and dequeue)
class Queue {
  #items = {};
  #head = 0;
  #tail = 0;

  enqueue(item) { this.#items[this.#tail++] = item; }
  dequeue() {
    if (this.isEmpty()) return undefined;
    const item = this.#items[this.#head];
    delete this.#items[this.#head++];
    return item;
  }
  peek() { return this.#items[this.#head]; }
  isEmpty() { return this.#tail === this.#head; }
  get size() { return this.#tail - this.#head; }
}
```

**Frontend use cases:**

- **Task queues** — process API calls in order, rate-limited notification delivery
- **BFS traversal** — breadth-first DOM traversal, level-order tree traversal
- **Message queues** — WebSocket message processing in order
- **Animation queues** — sequence animations one after another
- **Print queue** — process print requests FIFO

---

## Q 22.5 Linked Lists — when are they better than arrays?

**Difficulty:** Mid **Asked at:** Google, Amazon, Flipkart

Linked lists store elements as nodes where each node points to the next (and previous, in a doubly linked list). Arrays store elements contiguously in memory.

javascript

```
class ListNode {
  constructor(value) {
    this.value = value;
    this.next = null;
  }
}

class LinkedList {
  constructor() {
    this.head = null;
    this.tail = null;
    this.size = 0;
  }

  addToEnd(value) {
    const node = new ListNode(value);
    if (!this.head) {
      this.head = this.tail = node;
    } else {
      this.tail.next = node;
      this.tail = node;
    }
    this.size++;
  }

  removeFirst() {
    if (!this.head) return null;
    const value = this.head.value;
    this.head = this.head.next;
    if (!this.head) this.tail = null;
    this.size--;
    return value;
  }
}
```

**When linked lists beat arrays:** O(1) insertion/deletion at known positions (given a node reference). An LRU Cache combines a doubly linked list (for O(1) move-to-front) with a hash map (for O(1) lookup). This is the most common interview use case.

**Frontend relevance:** LRU caches for API responses, undo/redo history with limited size, playlist/carousel navigation (circular linked list), and text editor cursor movement (doubly linked list).

---

## Q 22.6 Doubly Linked Lists — why do LRU caches use them?

**Difficulty:** Mid **Asked at:** Google, Amazon, Flipkart

A doubly linked list has both `next` and `prev` pointers. This allows O(1) removal of any node (given a reference) because you can update both neighbors without traversing from the head.

javascript

```
class DoublyLinkedList {
  constructor() {
    // Sentinel nodes simplify edge cases (no null checks)
    this.head = { prev: null, next: null };
    this.tail = { prev: null, next: null };
    this.head.next = this.tail;
    this.tail.prev = this.head;
  }

  addToFront(node) {
    node.next = this.head.next;
    node.prev = this.head;
    this.head.next.prev = node;
    this.head.next = node;
  }

  remove(node) {
    node.prev.next = node.next;
    node.next.prev = node.prev;
  }

  removeLast() {
    if (this.tail.prev === this.head) return null;
    const node = this.tail.prev;
    this.remove(node);
    return node;
  }
}
```

**LRU Cache = Doubly Linked List + Hash Map:**

The list maintains access order (most recently used at front). The map provides O(1) key lookup to find the node. On access: find node via map → remove from current position → add to front. On eviction: remove from the tail (least recently used).

This is one of the most asked coding problems in interviews — see Chapter 25 (JS Problems SDE2).

---

## Q 22.7 Circular Buffers — what are they used for?

**Difficulty:** Mid **Asked at:** Google, Amazon

A circular buffer (ring buffer) is a fixed-size array where the end wraps around to the beginning. When full, new data overwrites the oldest data.

javascript

```
class CircularBuffer {
  constructor(capacity) {
    this.buffer = new Array(capacity);
    this.capacity = capacity;
    this.head = 0;
    this.size = 0;
  }

  push(item) {
    const index = (this.head + this.size) % this.capacity;
    this.buffer[index] = item;
    if (this.size === this.capacity) {
      this.head = (this.head + 1) % this.capacity;  // Overwrite oldest
    } else {
      this.size++;
    }
  }

  getAll() {
    const result = [];
    for (let i = 0; i < this.size; i++) {
      result.push(this.buffer[(this.head + i) % this.capacity]);
    }
    return result;
  }
}

// Keep last 100 log entries — older ones automatically discarded
const logBuffer = new CircularBuffer(100);
logBuffer.push({ timestamp: Date.now(), message: 'User clicked button' });
```

**Frontend use cases:** Log rotation (keep last N entries), streaming data visualization (show last 100 data points on a chart), undo history with limited depth, and keyboard input buffers.

---

## Learn More

- [Linked list (VisuAlgo) visualgo.net](https://visualgo.net/en/list)
- [Data structures (GeeksforGeeks) geeksforgeeks.org](https://www.geeksforgeeks.org/data-structures/)
- [Arrays, linked lists, stacks & queues YouTube](https://www.youtube.com/watch?v=nfOV4vwXjoc)
- [Introduction to linear data structures YouTube](https://www.youtube.com/watch?v=rRaP6YhwmKo)
- [Stacks, queues & linked lists YouTube](https://www.youtube.com/watch?v=9gTGH8GiXkY)
