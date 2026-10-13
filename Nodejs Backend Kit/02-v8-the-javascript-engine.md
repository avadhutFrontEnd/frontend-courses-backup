---
title: "V8: How Node Runs Your JavaScript"
source: written for this kit (not from geekyfrontend.in)
course: Node.js Backend Kit
tags: [geeky-frontend, nodejs-backend-kit]
---

# V8: How Node Runs Your JavaScript

*16 min read · Updated 7 Oct 2026 · Topic 2 of 74 · added to this kit, not part of the original course*

**Source:** written for this kit — examples verified on Node v24.19.0

---

## Why it matters

Node does not execute JavaScript itself. It hands your source text to V8, and V8 decides how fast it runs, how much memory it takes, and when the process dies with `FATAL ERROR: Reached heap limit`. Several everyday production problems are V8 problems:

- A service that was fast in a benchmark gets several times slower on one property access when real traffic sends objects of many different shapes (Example 4).
- A container with a 512 MB memory limit gets killed by the kernel, or a Node process aborts with "JavaScript heap out of memory", because nobody set the heap limit to match (Examples 6 and 9).
- p99 latency spikes line up with garbage-collection pauses (Example 5).
- Memory dashboards show `rss` far above `heapUsed`, and nobody knows which number to alert on (Example 8).

You do not need to read V8's source to handle these. You need a working model of four things: the compile pipeline and its tiers, hidden classes and inline caches, the garbage collector, and the memory numbers V8 and Node report.

## The concept, simply

**V8 is the C++ library, from Google's Chromium project, that parses JavaScript, compiles it to bytecode and then to machine code in several tiers, runs it, and manages the memory of every JavaScript object through a generational garbage collector.** Node embeds one V8 *isolate* (one heap, one set of compiled code) for the main thread, and one more for each worker thread. `process.versions.v8` tells you which V8 you have: on Node v24.19.0 it is `13.6.233.17-node.51` (V8 13.6 plus Node's patches).

Its real parts:

1. **Parser** turns source text into an abstract syntax tree (AST). It parses lazily: a function's body is only pre-parsed (checked for syntax errors) until the function is first called.
2. **Ignition**, the interpreter, compiles the AST to compact bytecode and runs it. While running, it records **type feedback** in a per-function *feedback vector*: which object shapes reached each property access, which types reached each `+`.
3. **Sparkplug**, the baseline compiler, turns bytecode into machine code by a direct translation, without optimizing. Fast to produce, faster to run than the interpreter.
4. **Maglev**, the mid-tier optimizing compiler, uses the feedback to produce specialised machine code quickly.
5. **TurboFan**, the top-tier optimizing compiler, spends more compile time for the fastest code. Since about 2022 V8 has been replacing TurboFan's internal "Sea of Nodes" graph with a control-flow-graph IR called **Turboshaft**; V8's March 2025 post says the whole JavaScript backend of TurboFan now uses Turboshaft. In Node 24, `--turboshaft` ("enable TurboFan's Turboshaft phases for JS") is on by default.
6. **Deoptimizer** throws optimized code away when one of its assumptions breaks, and resumes the function in a lower tier.
7. **Heap and garbage collector** ("Orinoco"): a young generation collected by a **scavenger**, an old generation collected by **mark-sweep-compact**, with incremental, concurrent and parallel helpers so the main thread pauses less.

You can confirm every tier name in your own binary:

bash

```
$ node --v8-options | grep -E '^\s*--(sparkplug|maglev|turbofan|turboshaft) ' -A1
  --maglev (enable the maglev optimizing compiler)
        type: bool  default: --maglev
--
  --sparkplug (enable Sparkplug baseline compiler)
        type: bool  default: --sparkplug
--
  --turbofan (use the Turbofan optimizing compiler)
        type: bool  default: --turbofan
--
  --turboshaft (enable TurboFan's Turboshaft phases for JS)
        type: bool  default: --turboshaft
```

## How it works

**The life of one function, step by step:**

1. **Pre-parse.** When your file loads, V8 checks the syntax of `square` but does not compile its body.
2. **First call: full parse and bytecode.** Ignition compiles the body to bytecode and runs it. The feedback vector is allocated lazily, not on the very first call (`--lazy-feedback-allocation`, on by default).
3. **Collect feedback.** Each run records what it saw: "`n * n` saw small integers", "`o.x` saw shape M1".
4. **Baseline (Sparkplug).** Once the function has run a bit, Sparkplug compiles its bytecode to machine code, in batches and on a background thread.
5. **Hot and stable: Maglev.** When the function has used up its interrupt budget and its feedback has stopped changing, V8 logs "marking … for optimization to MAGLEV … reason: hot and stable" and compiles it concurrently. Code compiled by Maglev or TurboFan contains **checks**: "is this still a small integer?", "is this object still shape M1?".
6. **Still hot: TurboFan.** If the function keeps running, it is marked again and compiled by TurboFan (`TURBOFAN_JS` in the logs), which inlines callees, removes redundant checks and allocates registers more carefully. A long-running loop can switch to optimized code in the middle of the loop: on-stack replacement (OSR).
7. **A check fails: deoptimize.** If a string reaches code that assumed integers, the check fails, V8 rebuilds the interpreter frame from the optimized frame ("bailout … deopt-eager, reason: not a Smi") and continues in Ignition. The function collects new, broader feedback and may be optimized again later.

text

```
 source ──► parser ──► AST ──► Ignition bytecode ─────────┐ runs + records feedback
                                    │                     │
                                    ▼                     │
                          Sparkplug (baseline machine code, no optimization)
                                    │ hot and stable
                                    ▼
                          Maglev (fast optimizing, uses feedback)
                                    │ still hot
                                    ▼
                          TurboFan (+ Turboshaft backend, slowest compile, fastest code)

        Maglev / TurboFan code ── a speculation check fails ──► deoptimize ──► back to Ignition
```

The `invocation-count-for-maglev` (default 400) and `invocation-count-for-turbofan` (default 3000) flags hint at the scale. V8 actually decides from a budget based on bytecode executed, so short functions called in a loop tier up within milliseconds.

**Hidden classes (shapes, "maps") and inline caches.** JavaScript objects look like dictionaries, but V8 does not store most of them as hash tables. Each object points to a **hidden class**, which V8 calls a *map*: a description of the property names, their order and where each value sits in the object. Objects created the same way share one map, so the property *names* are stored once, and each object only holds the values.

1. `{}` starts with an empty map M0.
2. Adding `x` creates (or reuses) a **transition** M0 → M1 "has x at slot 0".
3. Adding `y` gives M1 → M2 "x at slot 0, y at slot 1".
4. Another object built in the same order follows the same transitions and ends at M2. One built as `{ y, x }` ends at a different map.
5. `delete` of a property that is not the last one added, or adding very many properties, can switch the object to **dictionary mode** (a hash table), which is slower to read.

Every property access site in your code (`o.x` at line 7) has an **inline cache** (IC) slot in the feedback vector. The IC remembers the maps it has seen and where `x` was for each:

- **Monomorphic**: one map. The machine code is "compare map with M2; if equal, load slot 0". One compare and one load.
- **Polymorphic**: 2 to 4 maps (`--max-valid-polymorphic-map-count=4` in V8 13.6). A short list of compares.
- **Megamorphic**: more than 4. The IC gives up on a per-site list and looks the property up in a global stub cache keyed by map and name. Much slower than one compare, and optimizing compilers cannot inline it as well.

**Garbage collection.** V8 frees memory that is no longer reachable from *roots* (the stack, globals, handles that Node holds). It splits the heap by object age, because most objects die young (a temporary in a loop, a parsed request body).

text

```
 V8 heap of one isolate
 ┌──────────────── young generation ────────────────┐ ┌──────────── old generation ────────────┐
 │  semi-space A (objects being allocated)          │ │ old space   (survivors, long-lived)    │
 │  semi-space B (empty; copy target)               │ │ code space  (compiled machine code)    │
 │  new large-object space                          │ │ large-object space (very big objects)  │
 └──────────────────────────────────────────────────┘ └────────────────────────────────────────┘
   Scavenge: copy live objects A→B (or promote to old space)     Mark-Compact: mark live objects,
   cost ∝ live objects, a few ms                                 sweep dead ones, compact pages
```

1. **Allocate.** New objects go into the young generation, by bumping a pointer. Very cheap.
2. **Scavenge** (minor GC). When the active semi-space is full ("allocation failure" in `--trace-gc`), V8 copies the *live* objects into the other semi-space, or promotes objects that already survived one scavenge into old space, then swaps the two. Dead objects are never touched, so the cost depends on the survivors, not on the garbage. Several threads copy in parallel (`--parallel-scavenge`).
3. **Promote.** Survivors move to the old generation.
4. **Mark** (major GC). When old space grows past a limit, V8 marks every object reachable from the roots. Marking is **incremental** (done on the main thread in small steps between your JavaScript) and **concurrent** (done on the V8 worker threads while your JavaScript runs). Both are on by default (`--incremental-marking`, `--concurrent-marking`).
5. **Sweep and compact.** Unmarked memory goes back to free lists (sweeping runs concurrently, `--concurrent-sweeping`), and fragmented pages are compacted by moving objects together. The final pause, which finishes marking and does compaction, is the "Mark-Compact … ms" in the log.

**Heap limits.** The heap cannot grow without bound. `heap_size_limit` = old-generation limit + young-generation size, and the young generation is 3 × the maximum semi-space size (the Node CLI docs cite V8's `YoungGenerationSizeFromSemiSpaceSize`). By default V8 picks the old-generation limit from the machine's memory. `--max-old-space-size=<MiB>` sets the old-generation limit, `--max-semi-space-size=<MiB>` sets the semi-space size, and `--max-old-space-size-percentage=<n>` sets the old-generation limit as a percentage of system memory. When a full GC cannot get the old generation under its limit, V8 aborts the process: there is no exception to catch.

**Startup snapshots.** A V8 snapshot is a serialized heap. Node's own bootstrap state is already restored from a snapshot built into the binary. `node --snapshot-blob app.blob --build-snapshot entry.js` runs `entry.js` once, then serializes the resulting heap to `app.blob`. Later, `node --snapshot-blob app.blob` deserializes that heap instead of running the setup code again, and calls the function you registered with `v8.startupSnapshot.setDeserializeMainFunction()`. The `v8.startupSnapshot` API was added in v18.6.0 and marked stable in v24.0.0; `--build-snapshot` was added in v18.8.0 and stopped being experimental in v24.13.1. The `--snapshot-blob` flag page still carries "Stability: 1 - Experimental".

## Code examples

**Example 1 — watching one function climb the tiers.**

javascript

```
// hot.js: run with  node --trace-baseline --trace-opt hot.js 2>&1 | grep -E 'square|done'
function square(n) { return n * n; }
let s = 0;
for (let i = 0; i < 3_000_000; i++) s += square(i & 1023);
console.log('done', s);
```

bash

```
$ node --trace-baseline --trace-opt hot.js 2>&1 | grep -E 'square|done'
[Baseline batch compilation] Enqueued SFI square with estimated size 42 (current budget: 42/4096)
[Baseline batch compilation] Enqueued SFI square with estimated size 42 (current budget: 567/4096)
[marking 0x2e09cace4fa9 <JSFunction square (sfi = 0x2a272b3231a9)> for optimization to MAGLEV, ConcurrencyMode::kConcurrent, reason: hot and stable]
[compiling method 0x2e09cace4fa9 <JSFunction square (sfi = 0x2a272b3231a9)> (target MAGLEV), mode: ConcurrencyMode::kConcurrent]
[completed compiling 0x2e09cace4fa9 <JSFunction square (sfi = 0x2a272b3231a9)> (target MAGLEV) - took 0.000, 0.038, 0.001 ms]
[Concurrent Sparkplug Off Thread] Function 0x2a272b3231a9 <SharedFunctionInfo square> installed
[marking 0x2e09cace4fa9 <JSFunction square (sfi = 0x2a272b3231a9)> for optimization to TURBOFAN_JS, ConcurrencyMode::kConcurrent, reason: hot and stable]
[compiling method 0x2e09cace4fa9 <JSFunction square (sfi = 0x2a272b3231a9)> (target TURBOFAN_JS), mode: ConcurrencyMode::kConcurrent]
[completed compiling 0x2e09cace4fa9 <JSFunction square (sfi = 0x2a272b3231a9)> (target TURBOFAN_JS) - took 0.003, 0.391, 0.009 ms]
[completed optimizing 0x2e09cace4fa9 <JSFunction square (sfi = 0x2a272b3231a9)> (target TURBOFAN_JS)]
done 1046910851360
```

All four tiers appear: the function started in Ignition (nothing is logged for that), was queued for Sparkplug, marked for Maglev, and then for TurboFan. Sparkplug's "installed" line lands after Maglev's because both compile on background threads, and the log records when each finished, not a strict sequence. The addresses change on every run. With `--no-maglev`, the same run goes straight from baseline to `TURBOFAN_JS`.

**Example 2 — a deoptimization, caught in the act.**

javascript

```
// deopt.js: run with  node --trace-opt --trace-deopt deopt.js
function add(a, b) { return a + b; }

let r = 0;
for (let i = 0; i < 200_000; i++) r = add(i, 1);   // only small integers: V8 speculates "int + int"
console.log('ints done', r);

r = add('x', 1);                                    // a string breaks the speculation
console.log('string done', r);
```

bash

```
$ node --trace-opt --trace-deopt deopt.js | grep -E 'JSFunction add |done'
[marking 0x3cdd3e225081 <JSFunction add (sfi = 0x1cc3a6da3351)> for optimization to MAGLEV, ConcurrencyMode::kConcurrent, reason: hot and stable]
[compiling method 0x3cdd3e225081 <JSFunction add (sfi = 0x1cc3a6da3351)> (target MAGLEV), mode: ConcurrencyMode::kConcurrent]
[completed compiling 0x3cdd3e225081 <JSFunction add (sfi = 0x1cc3a6da3351)> (target MAGLEV) - took 0.001, 0.067, 0.001 ms]
ints done 200000
[bailout (kind: deopt-eager, reason: not a Smi): begin. deoptimizing 0x3cdd3e225081 <JSFunction add (sfi = 0x1cc3a6da3351)>, 0x276a8f141da9 <Code MAGLEV>, opt id 3, bytecode offset 2, deopt exit 0, FP to SP delta 24, caller SP 0x7ffee64efca0, pc 0x700bf800373b]
string done x1
```

A *Smi* is V8's "small integer", stored directly in the pointer instead of as a heap object. Maglev compiled `a + b` as integer addition guarded by "is `a` a Smi?". The string failed that check, so V8 dropped the Maglev code (`deopt-eager`: the check failed right there) and finished the call in the interpreter. The result is still correct (`x1`); only speed is lost. Tiering is concurrent, so runs differ: in six re-runs the bailout hit `<Code MAGLEV>` five times and `<Code TURBOFAN_JS>` once (that time `add` had already been compiled by TurboFan), always with "reason: not a Smi". One deopt is harmless. A function that deopts, reoptimizes and deopts again on every request is a problem, and this trace is how you find it.

**Example 3 — hidden classes made visible.** `--allow-natives-syntax` enables V8's internal `%` functions. They are for experiments only; never ship them.

javascript

```
// shapes.js: run with  node --allow-natives-syntax shapes.js
// %HaveSameMap and %HasFastProperties are V8 debug intrinsics: never ship them.
const a = { x: 1, y: 2 };
const b = { x: 3, y: 4 };
const c = { y: 5, x: 6 };            // same keys, different order
const d = { x: 7 };  d.y = 8;        // y added later, same order as a
class Point { constructor(x, y) { this.x = x; this.y = y; } }

console.log('a vs b (same literal shape):    ', %HaveSameMap(a, b));
console.log('a vs c (keys in other order):   ', %HaveSameMap(a, c));
console.log('a vs d (y added after creation):', %HaveSameMap(a, d));
console.log('Point vs Point:                 ', %HaveSameMap(new Point(1, 2), new Point(3, 4)));
console.log('a vs new Point (literal vs class):', %HaveSameMap(a, new Point(1, 2)));

const e = { x: 1, y: 2, z: 3 };
console.log('e fast properties before delete:', %HasFastProperties(e));
delete e.y;                           // deleting a non-last property
console.log('e fast properties after delete: ', %HasFastProperties(e));
const f = { x: 1, y: 2, z: 3 };
f.y = undefined;                      // alternative: keep the shape
console.log('f after f.y = undefined:        ', %HasFastProperties(f));

// Real output (Node v24.19.0):
//   a vs b (same literal shape):     true
//   a vs c (keys in other order):    false
//   a vs d (y added after creation): false
//   Point vs Point:                  true
//   a vs new Point (literal vs class): false
//   e fast properties before delete: true
//   e fast properties after delete:  false
//   f after f.y = undefined:         true
```

`a` and `d` have the same keys in the same order and still get different maps: V8 creates an object literal with a map prepared for that literal, while `d` started from the `{ x }` literal's map and took a transition. Two objects are only guaranteed to share a map if they are created by the same code path, which is the reason to build objects of one kind in one place (a constructor, a class, or one factory function). The `delete` turned `e` into a dictionary-mode object; assigning `undefined` kept `f` on its fast map.

**Example 4 — benchmark: how many shapes reach one property access.** Method: one call site `arr[i].x` reads 1,024 objects; the objects get 1, 2, 4, 5 or 32 different maps by adding one extra property whose name depends on `i % SHAPES`. Each case runs in its own process (so no case inherits another's IC feedback), warms up with 2,000 passes over the array, then times 20,000 passes (20.5 M reads) with `process.hrtime.bigint()`. A checksum proves the reads were not optimized away. A runner repeats each case 10 times and reports min, median and max.

javascript

```
// shapes-bench.js: cost of o.x when the objects reaching one call site have N shapes
// Usage: node shapes-bench.js <shapes>   (each case runs in its own process,
// so no case inherits another case's inline-cache feedback)
const SHAPES = Number(process.argv[2] ?? 1);
const N = 1024;          // objects in the array
const WARMUP = 2_000;    // passes over the array before timing (lets V8 tier up)
const PASSES = 20_000;   // timed passes: 20,000 x 1,024 = 20.5 M property reads

// Object i gets property x, plus one extra property whose NAME depends on
// i % SHAPES. Different names = different hidden classes (shapes).
const objs = [];
for (let i = 0; i < N; i++) {
  const o = { x: i };
  o['p' + (i % SHAPES)] = 0;
  objs.push(o);
}

function sumX(arr) {          // the one call site: arr[i].x
  let s = 0;
  for (let i = 0; i < arr.length; i++) s += arr[i].x;
  return s;
}

let sink = 0;
for (let p = 0; p < WARMUP; p++) sink += sumX(objs);

const t0 = process.hrtime.bigint();
for (let p = 0; p < PASSES; p++) sink += sumX(objs);
const ns = Number(process.hrtime.bigint() - t0);

const expected = (WARMUP + PASSES) * (N * (N - 1)) / 2;   // proves the reads happened
if (sink !== expected) throw new Error('wrong sum');
console.log((ns / (PASSES * N)).toFixed(2));             // ns per property read
```

javascript

```
// run-bench.js: run each case 10 times in fresh processes, report min / median / max
const { execFileSync } = require('node:child_process');
const RUNS = 10;
for (const shapes of [1, 2, 4, 5, 32]) {
  const xs = [];
  for (let r = 0; r < RUNS; r++) {
    xs.push(Number(execFileSync(process.execPath, ['shapes-bench.js', String(shapes)], { cwd: __dirname })));
  }
  xs.sort((a, b) => a - b);
  const median = (xs[4] + xs[5]) / 2;
  console.log(`${String(shapes).padStart(2)} shape(s): median ${median.toFixed(2)} ns/read  ` +
    `(min ${xs[0].toFixed(2)}, max ${xs[RUNS - 1].toFixed(2)}, ${RUNS} runs)`);
}

// Real output (Node v24.19.0, Linux x64, 8 logical CPUs; first of three sessions):
//    1 shape(s): median 1.56 ns/read  (min 1.33, max 2.00, 10 runs)
//    2 shape(s): median 2.92 ns/read  (min 2.15, max 3.48, 10 runs)
//    4 shape(s): median 3.23 ns/read  (min 2.80, max 4.80, 10 runs)
//    5 shape(s): median 6.46 ns/read  (min 5.53, max 7.76, 10 runs)
//   32 shape(s): median 6.99 ns/read  (min 5.91, max 7.92, 10 runs)
```

Medians from four separate sessions of `node run-bench.js` (40 processes per case in total; session 4 is the re-run of the block as saved in this lesson, on a quieter machine):

| Shapes at the call site | IC state | Session 1 | Session 2 | Session 3 | Session 4 |
| --- | --- | --- | --- | --- | --- |
| 1 | monomorphic | 1.56 ns | 1.37 ns | 1.85 ns | 1.17 ns |
| 2 | polymorphic | 2.92 ns | 2.81 ns | 2.92 ns | 1.79 ns |
| 4 | polymorphic | 3.23 ns | 3.15 ns | 2.88 ns | 2.17 ns |
| 5 | megamorphic | 6.46 ns | 7.04 ns | 6.48 ns | 4.85 ns |
| 32 | megamorphic | 6.99 ns | 7.20 ns | 5.99 ns | 4.89 ns |

What the numbers support: the jump is between 4 and 5 shapes, exactly where the IC leaves the polymorphic state (`--max-valid-polymorphic-map-count=4`), and in every session the 5-shape median is 2.0–2.3× the 4-shape median and 3.5–5.1× the 1-shape median. Past 5 shapes the cost barely grows. What they do not support: precise ratios. The machine was shared with other jobs, in a noisy session the max was up to 70% above the min (4 shapes, session 1), and session medians for the same case differ by up to 2 ns (5 shapes: 4.85 to 7.04 ns) depending on what else the machine was doing. Treat this as "a few nanoseconds per access, at a call site that runs millions of times", not as a law. A real request handler does far more than read one property, so measure your own hot path before rewriting code for shapes.

**Example 5 — reading `--trace-gc`.**

javascript

```
// gc.js: run with  node --trace-gc gc.js
// Phase 1: lots of short-lived garbage (dies young -> Scavenge)
for (let i = 0; i < 2_000_000; i++) { const tmp = { i, s: 'x' + i }; }
console.log('--- phase 1 done: short-lived objects');

// Phase 2: objects that stay reachable (promoted to old space -> Mark-Compact)
const keep = [];
for (let i = 0; i < 1_500_000; i++) keep.push({ i, s: 'y' + i });
console.log('--- phase 2 done: kept', keep.length, 'objects');
```

bash

```
$ node --trace-gc gc.js | sed -E 's/^\[[0-9]+:0x[0-9a-f]+\] +//' | sed -n '1,3p;/phase 1/,$p'
18 ms: Scavenge 4.7 (6.5) -> 3.9 (7.5) MB, pooled: 0 MB, 0.47 / 0.00 ms  (average mu = 1.000, current mu = 1.000) allocation failure; 
19 ms: Scavenge 4.7 (7.5) -> 4.1 (7.7) MB, pooled: 0 MB, 0.50 / 0.00 ms  (average mu = 1.000, current mu = 1.000) allocation failure; 
20 ms: Scavenge 4.9 (7.7) -> 4.3 (9.7) MB, pooled: 0 MB, 0.41 / 0.00 ms  (average mu = 1.000, current mu = 1.000) allocation failure; 
--- phase 1 done: short-lived objects
93 ms: Scavenge 13.0 (22.6) -> 8.1 (22.1) MB, pooled: 0 MB, 2.14 / 0.00 ms  (average mu = 1.000, current mu = 1.000) allocation failure; 
100 ms: Scavenge 14.0 (23.6) -> 12.0 (42.5) MB, pooled: 0 MB, 4.57 / 0.00 ms  (average mu = 1.000, current mu = 1.000) allocation failure; 
117 ms: Scavenge 27.8 (45.8) -> 23.0 (47.2) MB, pooled: 0 MB, 8.39 / 0.00 ms  (average mu = 1.000, current mu = 1.000) allocation failure; 
130 ms: Mark-Compact 23.8 (47.5) -> 22.0 (86.7) MB, pooled: 0 MB, 9.89 / 0.00 ms  (+ 0.1 ms in 10 steps since start of marking, biggest step 0.0 ms, walltime since start of marking 12 ms) (average mu = 0.919, current mu = 0.919) finalize incremental marking via stack guard; GC in old space requested
194 ms: Scavenge 104.6 (137.2) -> 75.0 (123.2) MB, pooled: 0 MB, 12.43 / 0.00 ms  (average mu = 0.919, current mu = 0.919) allocation failure; 
231 ms: Scavenge 103.5 (136.2) -> 95.7 (216.7) MB, pooled: 0 MB, 15.46 / 0.00 ms  (average mu = 0.919, current mu = 0.919) allocation failure; 
--- phase 2 done: kept 1500000 objects
253 ms: Mark-Compact 128.1 (239.3) -> 4.0 (135.2) MB, pooled: 77 MB, 2.99 / 0.00 ms  (+ 0.9 ms in 27 steps since start of marking, biggest step 0.1 ms, walltime since start of marking 4 ms) (average mu = 0.944, current mu = 0.968) finalize incremental marking via task; GC in old space requested
```

(The first `sed` strips the `[pid:isolate]` prefix; the second keeps the first 3 lines and everything from "phase 1 done". About 20 more phase-1 Scavenge lines like the first three are cut.)

How to read one line: `Scavenge 4.7 (6.5) -> 3.9 (7.5) MB` means the heap held 4.7 MB of objects (6.5 MB reserved) before this GC and 3.9 MB (7.5 MB reserved) after; `0.47 / 0.00 ms` is the main-thread pause. `mu` is mutator utilisation: the share of time your JavaScript, not the GC, was running. What it shows:

- **Phase 1** (garbage dies young): each scavenge takes about 0.5 ms and the heap drops back to about 4 MB every time. Copying only the live objects is cheap when almost nothing is live.
- **Phase 2** (objects stay reachable): scavenges get slower (2 → 15 ms) because every survivor must be copied and promoted, and the old generation grows until a **Mark-Compact** runs. "+ 0.1 ms in 10 steps since start of marking" is the incremental marking that happened before the final 9.89 ms pause.
- **The last line** runs after the main module has returned. `keep` is no longer reachable, so 128 MB drops to 4 MB.

**Example 6 — what `--max-old-space-size` does to `heap_size_limit`.**

javascript

```
// heaplimit.js: how big may the V8 heap grow in this process?
const v8 = require('node:v8');
const mb = (b) => `${(b / 1024 / 1024).toFixed(0)} MB`;
const s = v8.getHeapStatistics();
console.log('heap_size_limit:', mb(s.heap_size_limit), '| execArgv:', process.execArgv.join(' ') || '(none)');
```

bash

```
$ node heaplimit.js
heap_size_limit: 2240 MB | execArgv: (none)
$ node --max-old-space-size=512 heaplimit.js
heap_size_limit: 704 MB | execArgv: --max-old-space-size=512
$ node --max-old-space-size=4096 heaplimit.js
heap_size_limit: 4288 MB | execArgv: --max-old-space-size=4096
$ NODE_OPTIONS=--max-old-space-size=1024 node heaplimit.js
heap_size_limit: 1216 MB | execArgv: (none)
$ node --max-semi-space-size=16 heaplimit.js
heap_size_limit: 2096 MB | execArgv: --max-semi-space-size=16
$ node --max-semi-space-size=128 heaplimit.js
heap_size_limit: 2432 MB | execArgv: --max-semi-space-size=128
$ node --max-old-space-size-percentage=25 heaplimit.js
heap_size_limit: 3986 MB | execArgv: --max-old-space-size-percentage=25
```

On this 15 GB machine the numbers fit `limit = old-generation limit + 3 × max semi-space`: the default is 2048 + 3 × 64 = 2240 MB, `--max-old-space-size=512` gives 512 + 192 = 704 MB, and a 16 MB semi-space gives 2048 + 48 = 2096 MB. `NODE_OPTIONS` works the same as the flag but does not appear in `process.execArgv`. 25% of this machine's 15,178 MB is 3,794 MB, plus 192 MB young generation = 3,986 MB. The default old-generation limit depends on the machine's memory and the V8 version, so print `heap_size_limit` on your real host instead of assuming 2 GB.

**Example 7 — every field of `v8.getHeapStatistics()`.**

bash

```
$ node -p "require('v8').getHeapStatistics()"
{
  total_heap_size: 6025216,
  total_heap_size_executable: 262144,
  total_physical_size: 5779456,
  total_available_size: 2345390936,
  used_heap_size: 4458104,
  heap_size_limit: 2348810240,
  malloced_memory: 163928,
  peak_malloced_memory: 114752,
  does_zap_garbage: 0,
  number_of_native_contexts: 1,
  number_of_detached_contexts: 0,
  total_global_handles_size: 12360,
  used_global_handles_size: 2400,
  external_memory: 1807292
}
```

| Field | Meaning (bytes unless noted) |
| --- | --- |
| `total_heap_size` | Memory V8 has allocated for the heap; grows as `used_heap_size` needs more. |
| `total_heap_size_executable` | The part that may hold executable (JIT-compiled) code. |
| `total_physical_size` | Heap memory actually committed, not just reserved. |
| `total_available_size` | How much more V8 can use before it hits the limit. |
| `used_heap_size` | Bytes occupied by JavaScript objects right now. |
| `heap_size_limit` | The maximum heap size (Example 6). |
| `malloced_memory`, `peak_malloced_memory` | Memory V8 allocated with `malloc` for itself, now and at the peak. |
| `does_zap_garbage` | 0/1: whether V8 overwrites freed memory with a pattern (a debug option). |
| `number_of_native_contexts` | Top-level contexts alive (1 here; each `vm` context adds one). A number that keeps growing means a context leak. |
| `number_of_detached_contexts` | Contexts that were detached but not yet collected; non-zero for long is a likely leak. |
| `total_global_handles_size`, `used_global_handles_size` | Memory for global handles: the C++-side references that keep JavaScript objects alive. |
| `external_memory` | Memory outside the heap that V8 has been told about (Buffers and other native allocations). |

**Example 8 — edge case: a 100 MB `Buffer` that does not move `rss`.** `process.memoryUsage()` returns five fields: `rss` (resident set size: all memory of the process that is in physical RAM, including C++ objects, code and the heap), `heapTotal` and `heapUsed` (V8's heap, reserved and used), `external` (memory of C++ objects bound to JavaScript objects) and `arrayBuffers` (memory for `ArrayBuffer`s and `SharedArrayBuffer`s, including every Node `Buffer`; this is also counted in `external`). `arrayBuffers` was added in v13.9.0 / v12.17.0 and `external` in v7.2.0.

javascript

```
// memory.js: which field of process.memoryUsage() moves for which allocation
const MB = (b) => (b / 1024 / 1024).toFixed(1).padStart(6);
function show(label) {
  const m = process.memoryUsage();
  console.log(`${label.padEnd(24)} rss ${MB(m.rss)} | heapTotal ${MB(m.heapTotal)} | ` +
    `heapUsed ${MB(m.heapUsed)} | external ${MB(m.external)} | arrayBuffers ${MB(m.arrayBuffers)}`);
}

show('start');
const objects = Array.from({ length: 1_000_000 }, (_, i) => ({ i }));   // JS objects: V8 heap
show('+1M small objects');
const buf = Buffer.alloc(100 * 1024 * 1024);                              // bytes live outside the V8 heap
show('+100 MB Buffer');
buf.fill(1);                                                              // now the OS must back every page
show('after buf.fill(1)');
console.log('keys:', Object.keys(process.memoryUsage()).join(', '), '| kept', objects.length, buf.length);

// Real output (Node v24.19.0, Linux x64):
//   start                    rss   44.4 | heapTotal    6.2 | heapUsed    3.8 | external    1.5 | arrayBuffers    0.1
//   +1M small objects        rss  113.4 | heapTotal   60.1 | heapUsed   42.2 | external    1.7 | arrayBuffers    0.1
//   +100 MB Buffer           rss  113.4 | heapTotal   60.1 | heapUsed   42.2 | external  101.7 | arrayBuffers  100.1
//   after buf.fill(1)        rss  213.4 | heapTotal   60.1 | heapUsed   42.2 | external  101.7 | arrayBuffers  100.1
//   keys: rss, heapTotal, heapUsed, external, arrayBuffers | kept 1000000 104857600
```

Walk through it:

1. One million small objects add about 38 MB to `heapUsed` and about 69 MB to `rss` (the heap reserves more than it uses, and V8's own bookkeeping grows too).
2. `Buffer.alloc(100 MB)` adds 100 MB to `arrayBuffers` and `external`, and **nothing to `heapUsed`**: Buffer bytes live outside the V8 heap, which is also why `--max-old-space-size` does not limit them.
3. It also adds **nothing to `rss`**. `Buffer.alloc` must return zero-filled memory, and on Linux a fresh zeroed allocation of this size is mapped by the kernel without giving it physical pages yet. A page becomes resident only when it is first written.
4. `buf.fill(1)` writes every page, and `rss` rises by exactly 100 MB.

So "allocated" and "resident" are different, and alerting on `heapUsed` alone misses Buffers completely. For container limits, watch `rss`; for JavaScript leaks, watch `heapUsed` after GC; for Buffer leaks, watch `arrayBuffers`. On glibc systems `rss` can also keep growing while `heapTotal` is stable because of malloc fragmentation (the docs mention this).

> ⚠️ Unverified: whether `rss` also stays flat after `Buffer.alloc` on macOS and Windows; this depends on each OS's lazy page allocation, and I could only run it on Linux.

**Example 9 — edge case: a 64 MB old space that reports a 256 MB limit, then aborts.**

javascript

```
// oom.js: keep everything reachable until the old space is full
const v8 = require('node:v8');
console.log('limit', (v8.getHeapStatistics().heap_size_limit / 1048576).toFixed(0), 'MB');
const keep = [];
for (;;) keep.push({ n: keep.length, s: 'item-' + keep.length });
```

bash

```
$ node --max-old-space-size=64 oom.js 2>&1 | grep -v '^\s*[0-9]*: 0x' | head -12; echo "exit status: ${PIPESTATUS[0]}"
limit 256 MB

<--- Last few GCs --->

[90195:0x14afd000]      139 ms: Mark-Compact 46.9 (105.4) -> 40.8 (105.9) MB, pooled: 0 MB, 32.27 / 0.00 ms  (+ 0.1 ms in 10 steps since start of marking, biggest step 0.0 ms, walltime since start of marking 34 ms) (average mu = 0.681, current mu = 0.605)[90195:0x14afd000]      222 ms: Mark-Compact 75.5 (123.8) -> 63.6 (128.9) MB, pooled: 0 MB, 62.34 / 0.00 ms  (+ 0.0 ms in 0 steps since start of marking, biggest step 0.0 ms, walltime since start of marking 63 ms) (average mu = 0.443, current mu = 0.249) 
FATAL ERROR: Reached heap limit Allocation failed - JavaScript heap out of memory
----- Native stack trace -----

exit status: 134
```

Two surprises. The limit reads 256 MB, not 64 MB, because 192 MB of young generation (3 × 64 MB semi-space on this machine) is added to the 64 MB old space; for small limits the young generation is most of the number. And the process does not throw a catchable `RangeError`: V8 aborts (exit status 134 = 128 + `SIGABRT`), so no `try/catch`, `uncaughtException` handler or `exit` listener runs. (The `grep -v` removes the native stack-trace frames.) If your container has 512 MB, set `--max-old-space-size` well below it, for example 384, so the heap limit, Buffers and native memory together fit.

**Example 10 — a real startup snapshot.** The build step runs slow setup work once; each later start deserializes the result.

javascript

```
// entry.js: build-time script for `node --build-snapshot`
const v8 = require('node:v8');

// Slow to compute, small to keep: count the primes below 30 million.
function primesBelow(n) {
  const composite = new Uint8Array(n);
  let count = 0, last = 0;
  for (let i = 2; i < n; i++) {
    if (composite[i]) continue;
    count++; last = i;
    for (let j = i * i; j < n; j += i) composite[j] = 1;
  }
  return { count, last };
}
const t0 = performance.now();
const result = primesBelow(30_000_000);           // runs ONCE, at build time
const buildMs = (performance.now() - t0).toFixed(0);

v8.startupSnapshot.setDeserializeMainFunction(() => {
  // Runs on every start from the blob, with that process's argv and env.
  console.log('argv:', process.argv.slice(1));
  console.log(`primes: ${result.count}, last: ${result.last} (${buildMs} ms of work done at build time)`);
});
```

javascript

```
// plain.js: the same work, done on every start
function primesBelow(n) {
  const composite = new Uint8Array(n);
  let count = 0, last = 0;
  for (let i = 2; i < n; i++) {
    if (composite[i]) continue;
    count++; last = i;
    for (let j = i * i; j < n; j += i) composite[j] = 1;
  }
  return { count, last };
}
const result = primesBelow(30_000_000);
console.log('argv:', process.argv.slice(1));
console.log(`primes: ${result.count}, last: ${result.last}`);
```

bash

```
$ node --snapshot-blob app.blob --build-snapshot entry.js; echo "build exit: $?"
build exit: 0
$ ls -l app.blob | awk '{print $5, $9}'
6310958 app.blob
$ node --snapshot-blob app.blob hello
argv: [ 'hello' ]
primes: 1857859, last: 29999999 (1611 ms of work done at build time)
$ node plain.js hello
argv: [
  '…/snap/plain.js',
  'hello'
]
primes: 1857859, last: 29999999
$ for i in 1 2 3; do /usr/bin/time -f "snapshot %e s" node --snapshot-blob app.blob >/dev/null; /usr/bin/time -f "plain    %e s" node plain.js > /dev/null; done
snapshot 0.03 s
plain    0.47 s
snapshot 0.03 s
plain    0.49 s
snapshot 0.03 s
plain    0.47 s
```

The snapshot start takes 0.03 s against 0.47 s because the heap already contains `result`; the sieve never runs again. Note `argv`: in a snapshot start there is no script path, so `process.argv[1]` is the first user argument (`hello`), not a file name. Code that reads `process.argv[2]` silently gets the wrong value. The blob size and the build-time milliseconds change slightly on every build (6,308,270 and 6,310,958 bytes, 1,503 and 1,611 ms in two builds). Two more limits showed up when I ran this. `entry-require.js` is the same entry script with the sieve moved into a separate file and loaded with `const result = require('./work.js');`:

bash

```
$ node --snapshot-blob app.blob --build-snapshot entry-require.js
node:internal/main/mksnapshot:117
    throw err;
    ^

Error: Cannot find module './work.js'. 
    at requireForUserSnapshot (node:internal/main/mksnapshot:113:17)
    …
$ npx -y node@22 --snapshot-blob app.blob; echo "exit $?"
Failed to load the startup snapshot because it was built withNode.js version 24.19.0 and the current Node.js version is v22.23.3.
exit 14
```

The builder accepts one entry script, which may load built-in modules but not your own files, so a real app must be bundled into one file first. And a blob only loads on the exact Node version, architecture and platform that built it, so build it in the same Docker image you deploy. The `--snapshot-blob` docs say a mismatch "exits with status code 1"; on v22.23.3 the real status was 14, which `process` docs list as "Snapshot Failure". A snapshot also only helps when setup is slow to *compute* but small to *store*: a first attempt with a 200,000-entry `Map` produced a 21 MB blob and started no faster than the plain script (0.13 s each), because deserializing 21 MB costs about as much as building the map.

> ⚠️ Unverified: why the sieve took 1,435–1,611 ms inside `--build-snapshot` but about 460 ms in plain `node`. With `--no-maglev --no-turbofan` it took about 1,290 ms, which suggests the snapshot builder runs without the optimizing tiers, but I did not find this documented.

## Common mistakes & gotchas

- **"V8 compiles JavaScript once, ahead of time."** It interprets first, compiles to baseline code, and optimizes only hot functions, using assumptions it can throw away (Examples 1 and 2).
- **Micro-optimizing for shapes everywhere.** The cost is nanoseconds per access (Example 4). It matters at call sites that run millions of times per second; elsewhere, clarity wins.
- **Building objects of one kind in several different ways.** Different property order, adding properties later or conditionally, and mixing literals with class instances all create different maps and can push a hot call site to megamorphic.
- **Using `delete` on hot objects.** It can switch an object to dictionary mode (Example 3). Set the property to `undefined`, or use a `Map` for keys that come and go.
- **Treating `heapUsed` as "memory used".** Buffers are outside the heap (Example 8), and `rss` includes code, stacks, native libraries and allocator fragmentation.
- **Setting `--max-old-space-size` equal to the container limit.** The heap limit adds the young generation, and Buffers and native memory come on top. Leave headroom, or the kernel's OOM killer ends the process before V8 does.
- **Expecting to catch heap exhaustion.** "JavaScript heap out of memory" aborts the process (exit 134). Fix the cause, or restart from a supervisor.
- **Shipping `--allow-natives-syntax` or `%` functions.** They are V8 internals that change between versions.
- **Reading `process.argv[2]` in a snapshot-started app.** The script path is missing, so arguments start at index 1 (Example 10).

## Best practices

- **Create objects of one kind in one place** (a class or a factory) and initialise every property there, in the same order, even if the value starts as `null`.
- **Keep types stable in hot functions.** A function that always gets numbers stays optimized; one that gets numbers, strings and `undefined` keeps its generic code.
- **Profile before tuning.** Use `node --cpu-prof`, `--trace-deopt` filtered to your function, or `--trace-gc` to find a real hot spot first (topic 55).
- **Set the heap limit from the environment.** In containers, pass `--max-old-space-size` (or `NODE_OPTIONS=--max-old-space-size=…`) sized below the memory limit, and log `v8.getHeapStatistics().heap_size_limit` at start-up so you can see what you got.
- **Export all five `process.memoryUsage()` fields** to your metrics, not only `heapUsed`. Use `process.memoryUsage.rss()` (added in v15.6.0) for a cheap, frequent RSS sample.
- **Use startup snapshots for start-up time only when it matters** (CLIs, serverless cold starts), bundle the app first, and build the blob with the same Node binary you run.

## ⭐ Interview lens

> **Q: “Walk me through what V8 does with a function from first call to fastest code.”**  
> A: It is pre-parsed at load. On the first call it is fully parsed and Ignition compiles it to bytecode and runs it, recording type feedback per operation. Sparkplug then compiles the bytecode to unoptimized machine code. When the function is hot and its feedback is stable, Maglev compiles optimized code from the feedback; if it stays hot, TurboFan (with its Turboshaft backend) compiles faster code. Optimized code has checks; when a check fails, V8 deoptimizes back to Ignition. *Trap:* saying "V8 is a JIT that compiles everything to machine code up front".

> **Q: “What is a hidden class, and why does property order matter?”**  
> A: A hidden class (V8 calls it a map) describes an object's layout: which properties it has, in which order, at which offset. Objects built the same way share one map, and each property access site caches the maps it has seen. `{x, y}` and `{y, x}` get different maps, so code that sees both is polymorphic; more than 4 maps make the site megamorphic, which in my measurement cost 3.5–5× the monomorphic time per read.

> **Q: “Explain V8's garbage collector.”**  
> A: Generational. New objects go into a young generation of two semi-spaces; a scavenge copies the live ones to the other semi-space or promotes them to old space, so its cost depends on survivors. The old generation is collected by mark-sweep-compact: marking is incremental and concurrent with your JavaScript, sweeping is concurrent, and a short final pause finishes marking and compacts. Use `--trace-gc` to see each collection and its pause.

> **Q: “Your container has 1 GB of RAM. How do you set Node's memory?”**  
> A: Set `--max-old-space-size` to about 700–768 MB, not 1024, because the heap limit also includes the young generation (3 × the semi-space size) and Buffers, code and native memory live outside the heap. Verify with `v8.getHeapStatistics().heap_size_limit`, and monitor `rss` and `arrayBuffers` as well as `heapUsed`.

> **Q: “What is a deoptimization and how would you find one?”**  
> A: Optimized code assumes the types and shapes it saw before; when a value breaks the assumption (a string where only small integers came before), V8 discards that code and resumes in the interpreter. Run with `--trace-opt --trace-deopt` and filter for your function; repeated "bailout … reason: …" lines for the same function point to unstable types.

## Cheat-card

- **Pipeline:** parser → Ignition (bytecode + feedback) → Sparkplug (baseline) → Maglev (mid-tier) → TurboFan with Turboshaft (top tier); a failed check deoptimizes back to Ignition.
- **See it:** `--trace-opt`, `--trace-deopt`, `--trace-baseline`, `--trace-gc`; `node --v8-options` lists every flag and its default.
- **Shapes:** same creation path → same map. IC: 1 map monomorphic, 2–4 polymorphic, 5+ megamorphic (3.5–5× slower per read in Example 4).
- **Avoid in hot code:** changing property order, adding properties later, `delete`, mixing value types.
- **GC:** young gen = scavenge (copy survivors, cost ∝ live objects); old gen = mark-sweep-compact (incremental + concurrent marking, concurrent sweeping).
- **Heap limit:** `heap_size_limit` = old space + 3 × semi-space; set with `--max-old-space-size` (MiB), `--max-semi-space-size`, `--max-old-space-size-percentage`.
- **OOM:** "JavaScript heap out of memory" aborts the process (exit 134); it cannot be caught.
- **`process.memoryUsage()`:** `rss`, `heapTotal`, `heapUsed`, `external`, `arrayBuffers`. Buffers count in `arrayBuffers` and `external`, never `heapUsed`.
- **Snapshots:** `node --snapshot-blob app.blob --build-snapshot entry.js`, then `node --snapshot-blob app.blob`; one bundled entry file, same Node binary, `process.argv[1]` is the first argument.

---

## Learn More

- [The V8 JavaScript Engine nodejs.org](https://nodejs.org/en/learn/getting-started/the-v8-javascript-engine)
- [Ignition (V8 docs) v8.dev](https://v8.dev/docs/ignition)
- [Sparkplug — a non-optimizing JavaScript compiler v8.dev](https://v8.dev/blog/sparkplug)
- [Maglev — V8's Fastest Optimizing JIT v8.dev](https://v8.dev/blog/maglev)
- [Land ahoy: leaving the Sea of Nodes (Turboshaft) v8.dev](https://v8.dev/blog/leaving-the-sea-of-nodes)
- [Fast properties in V8 v8.dev](https://v8.dev/blog/fast-properties)
- [JavaScript engine fundamentals: Shapes and Inline Caches mathiasbynens.be](https://mathiasbynens.be/notes/shapes-ics)
- [Trash talk: the Orinoco garbage collector v8.dev](https://v8.dev/blog/trash-talk)
- [Concurrent marking in V8 v8.dev](https://v8.dev/blog/concurrent-marking)
- [Understanding and Tuning Memory nodejs.org](https://nodejs.org/en/learn/diagnostics/memory/understanding-and-tuning-memory)
- [V8 module: startup snapshot API nodejs.org](https://nodejs.org/api/v8.html)
