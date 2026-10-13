---
title: "String vs StringBuilder vs StringBuffer"
source: https://www.geekyfrontend.in/learn/java-interview-kit/stringbuilder-vs-stringbuffer
course: Java Interview Kit
tags: [geeky-frontend, java-interview-kit]
---

# String vs StringBuilder vs StringBuffer

*8 min read · Updated 28 Jul 2026 Topic 14 of 65 Bookmark Mark done Why it matters This is asked in almost every Java interview and it has an easy answer everyone gives · Topic 14 of 65*

**Source:** [https://www.geekyfrontend.in/learn/java-interview-kit/stringbuilder-vs-stringbuffer](https://www.geekyfrontend.in/learn/java-interview-kit/stringbuilder-vs-stringbuffer)

---

## Why it matters

This is asked in almost every Java interview and it has an easy answer everyone gives ("StringBuilder is faster, StringBuffer is synchronised") and a harder one that shows you understand *why*. It also has a genuinely important practical consequence: string concatenation in a loop is one of the most common real performance bugs in enterprise Java, and it turns a linear job into a quadratic one.

## The concept, simply

|  | `String` | `StringBuilder` | `StringBuffer` |
| --- | --- | --- | --- |
| Mutable | No | Yes | Yes |
| Thread-safe | Yes (immutable) | **No** | Yes (synchronised) |
| Speed | Slow for repeated edits | **Fastest** | Slower than Builder |
| Since | 1.0 | Java 5 | 1.0 |
| Use when | Fixed text, keys, sharing | **Almost always** | Practically never |

The honest summary: use `String` for values, `StringBuilder` when you are building text, and `StringBuffer` essentially never.

## How it works

The performance difference is not a constant factor — it is a change in complexity class:

Concatenating in a loop: every iteration allocates and copies the whole string

✘ String s = s + item; inside a loop

"a"

"ab"

"abc"

"abcd"
Each line is a NEW object. The previous one becomes garbage
immediately, and every character is copied again.
n iterations → O(n²) copying

✔ StringBuilder sb; sb.append(item);

one mutable char buffer

a
b
c
d

spare capacity

Appends write into the existing buffer. It doubles only
when full, so growth is amortised.

toString() — one String, built once
n iterations → O(n) copying

Concatenation in a loop copies every character accumulated so far, on every iteration. A StringBuilder writes into one buffer that doubles only when full.

With `String`, iteration *i* must allocate a new object and copy all *i* characters accumulated so far. Summing over *n* iterations gives O(n²) copying, plus *n* objects of garbage. With `StringBuilder`, each append writes into an existing `byte[]`; when it fills, the array doubles, so the total copying across all growth is amortised O(n).

**Why `StringBuffer` is obsolete** is the part worth articulating. It synchronises every method — `append`, `insert`, `length`, all of them. That costs something even uncontended, but the deeper problem is that per-method synchronisation is almost never the synchronisation you actually need. A builder used by one thread does not need it; a builder genuinely shared across threads needs the whole *sequence* of appends to be atomic, which locking each call individually does not provide. So `StringBuffer` pays for safety in the case where it is unnecessary and fails to deliver it in the case where it would matter. `StringBuilder` was added in Java 5 as the unsynchronised version, and in practice a builder is a local variable — the textbook case for needing no synchronisation at all.

## Code examples

The bug, and its fix:

java

```
// ✘ O(n²). At 100,000 items this can take minutes.
String result = "";
for (String item : items) {
    result += item + ",";        // new String allocated and fully copied, every time
}

// ✔ O(n). Milliseconds for the same input.
StringBuilder sb = new StringBuilder();
for (String item : items) {
    sb.append(item).append(',');
}
String result = sb.toString();

// ✔ Cleaner still when you are joining — no manual trailing-comma handling:
String result = String.join(",", items);
String result = items.stream().collect(Collectors.joining(", ", "[", "]"));
```

Where `+` is perfectly fine — and the distinction matters, because "never use +" is wrong advice:

java

```
// ✔ Fine. A single expression: javac compiles it to an invokedynamic call
//   that sizes the result and fills it in one pass (Java 9+).
String message = "User " + name + " logged in at " + timestamp;

// ✔ Fine. Compile-time constant — folded by javac into a single literal.
String url = "https://" + HOST + "/api";     // when HOST is a static final constant

// ✘ Not fine. A LOOP: each iteration is a separate expression that must
//   produce a complete new String, so the optimisation cannot help.
for (...) { s += x; }
```

Presizing, the small optimisation worth knowing when the length is predictable:

java

```
StringBuilder sb = new StringBuilder(1024);   // avoids repeated array growth
// Default capacity is 16 characters; each overflow allocates a new array of
// roughly double the size and copies. Presizing skips that entirely when you
// have a reasonable estimate.

// Reusing one builder across iterations, when that is the shape of the work:
StringBuilder line = new StringBuilder(256);
for (Record r : records) {
    line.setLength(0);                        // clear, keep the capacity
    line.append(r.id()).append('\t').append(r.name());
    writer.write(line.toString());
}
```

And the reason `StringBuilder` should stay local:

java

```
// StringBuilder is NOT thread-safe. Shared across threads, appends interleave
// and can corrupt the internal length/array pair — you can even get
// ArrayIndexOutOfBoundsException from inside append().
//
// The fix is not StringBuffer. It is: give each thread its own builder,
// or collect the pieces and join once.
String result = items.parallelStream().collect(Collectors.joining(","));
```

## Under the hood

Both builders extend the package-private `AbstractStringBuilder` and share their implementation entirely; `StringBuffer` simply overrides each method with a `synchronized` version that calls `super`. That is the whole difference — same algorithm, one lock.

Both also got **compact strings** in Java 9, storing a `byte[]` with a coder flag rather than a `char[]`. There is a subtlety here worth knowing: if a builder holding Latin-1 content receives a character that does not fit, it must *inflate* — reallocate as UTF-16 and copy everything. Appending one emoji to a large ASCII builder is therefore unexpectedly expensive. Rarely decisive, but a genuinely modern detail.

The **Java 9 `invokedynamic` change** to string concatenation is the piece that dates most interview answers. Older material says every `+` compiles into a `StringBuilder` — true up to Java 8. Since Java 9, `javac` emits an `invokedynamic` bound to `StringConcatFactory`, which generates a specialised method at run time that computes the exact length and fills the result in a single pass. It is faster than the old builder-based expansion, and it is why "the compiler turns + into StringBuilder anyway" is no longer accurate. The loop case is unaffected, because the problem there is the repeated production of complete intermediate strings, not the code generated for any one expression.

## Common mistakes

- **Concatenating with `+=` in a loop.** The headline bug. O(n²).
- **Concluding that `+` is always bad.** A single expression is fine and more readable than a builder.
- **Reaching for `StringBuffer` "to be safe".** It pays for a lock you do not need and does not give atomicity across a sequence anyway.
- **Sharing one `StringBuilder` across threads.** It is not thread-safe; give each thread its own or join at the end.
- **Building a string just to log it.** `log.debug("x=" + x)` concatenates even when debug is off. Use the parameterised form `log.debug("x={}", x)`.
- **Hand-rolling a join.** `String.join` or `Collectors.joining` handle separators and edge cases for you.
- **Ignoring initial capacity for large builds.** The default is 16 characters.

## Interview lens

> **Q: "Difference between `String`, `StringBuilder` and `StringBuffer`?"**  
> A: `String` is immutable, so every edit allocates a new object. `StringBuilder` is a mutable buffer and is not synchronised — the default choice. `StringBuffer` is the same thing with every method synchronised; it predates `StringBuilder` and is effectively legacy, because a builder is almost always a local variable that no other thread can see.

> **Q: "Why is concatenation in a loop bad?"**  
> A: Because strings are immutable, iteration *i* allocates a new object and copies all the characters accumulated so far, making the whole loop O(n²) in copying and producing *n* objects of garbage. A `StringBuilder` appends into one buffer that doubles when full, so the total work is amortised O(n).

> **Q: "Does the compiler not optimise `+` into a `StringBuilder` anyway?"**  
> A: It did up to Java 8. Since Java 9 it compiles to an `invokedynamic` call into `StringConcatFactory`, which generates a method that sizes the result exactly and fills it in one pass — better than the old expansion. Either way it only helps a single expression. In a loop each iteration is its own expression producing a complete new string, so no code generation can rescue it.

> **Q: "When would you actually use `StringBuffer`?"**  
> A: Realistically, only when maintaining old code that already uses it. If I genuinely had a buffer shared across threads I would not want per-method locking anyway — I would need the whole append sequence to be atomic, which `StringBuffer` does not give me. Far better to give each thread its own builder and join the results.

> **Q: "How would you join a list into a comma-separated string?"**  
> A: `String.join(",", list)` for the simple case, or `Collectors.joining(", ", "[", "]")` in a stream when I need a prefix and suffix. Both use a builder internally and handle the trailing-separator problem that hand-rolled loops always get wrong.

> **Q: "What is `StringBuilder`'s default capacity?"**  
> A: 16 characters, or the initial string's length plus 16. When it fills, the array grows to roughly double and everything is copied — so for a large known build, passing an initial capacity avoids several rounds of that.

## Cheat-card

- `String` immutable · `StringBuilder` mutable, **not** synchronised · `StringBuffer` mutable, synchronised, legacy.
- **Default to `StringBuilder`**; a builder is almost always a local variable.
- Loop with `+=` → **O(n²)**. Builder → amortised O(n).
- A **single** `+` expression is fine — Java 9+ compiles it via `invokedynamic`.
- Joining? `String.join` / `Collectors.joining`.
- Default capacity **16** — presize for large builds; `setLength(0)` to reuse.
- Logging: `log.debug("x={}", x)`, never `"x=" + x`.
- Both builders share `AbstractStringBuilder`; `StringBuffer` only adds `synchronized`.

---

## Learn More

- [StringBuilder — API docs docs.oracle.com](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/StringBuilder.html)
- [Compact string concatenation (JEP 280) openjdk.org](https://openjdk.org/jeps/280)
- [String vs StringBuilder vs StringBuffer | What is Immutability? | Java String Operations | Geekific Geekific](https://www.youtube.com/watch?v=_drNcLWgUlE)
- [String vs StringBuilder vs StringBuffer in Java | Immutability & Thread Safety Explained The Curious Coder](https://www.youtube.com/watch?v=SY-h-gwCF-U)
