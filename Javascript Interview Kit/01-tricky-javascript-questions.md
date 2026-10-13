---
title: "Tricky Javascript questions"
source: https://www.geekyfrontend.in/learn/javascript-interview-kit/tricky-javascript-questions
course: JavaScript Interview Kit
tags: [geeky-frontend, javascript-interview-kit]
---

# Tricky Javascript questions

*8 min read · Updated 13 Mar 2026 Topic 1 of 23 Bookmark Completed JavaScript Output Questions These questions are commonly asked in interviews to test JavaScript fundamentals · Topic 1 of 23*

**Source:** [https://www.geekyfrontend.in/learn/javascript-interview-kit/tricky-javascript-questions](https://www.geekyfrontend.in/learn/javascript-interview-kit/tricky-javascript-questions)

---

# JavaScript Output Questions

These questions are commonly asked in interviews to test **JavaScript fundamentals, hoisting, event loop, closures, and object behavior**.

---

# 1. Object.freeze vs Object.seal

## Code

javascript

```
let person1 = {
  name: "ravi",
  age: 21
};

let person2 = {
  name: "ram",
  age: 43
};

person1 = Object.freeze(person1);
person2 = Object.seal(person2);

person1.age = 31;
person2.age = 53;

person1.location = "US";
person2.location = "UK";

console.log(person1.location, person1.age);
console.log(person2.location, person2.age);
```

## Output

javascript

```
undefined 21
undefined 53
```

## Explanation

**Object.freeze**

- Prevents modifying existing properties
- Prevents adding new properties

So `person1` stays unchanged.

**Object.seal**

- Allows modification of existing properties
- Prevents adding new properties

So `person2.age` changes but `location` is not added.

---

# 2. Functions are Objects

## Code

javascript

```
function bark() {
  console.log("Woof!");
}

bark.animal = "dog";
```

## Explanation

Functions in JavaScript are **objects**, so you can attach properties to them.

javascript

```
console.log(bark.animal);
```

Output:

javascript

```
dog
```

---

# 3. Strict Mode and Global Variables

## Code

javascript

```
function getAge() {
  "use strict";
  age = 21;
  console.log(age);
}

getAge();
```

## Output

javascript

```
ReferenceError
```

## Explanation

In strict mode, assigning a value to an undeclared variable throws an error.

Without strict mode, `age` would become a global variable.

---

# 4. typeof with Arrow Functions

## Code

javascript

```
function sayHi() {
  return (() => 0)();
}

console.log(typeof sayHi());
```

## Output

javascript

```
number
```

## Explanation

`sayHi()` returns `0`.  
The type of `0` is `number`.

---

# 5. Sparse Arrays

## Code

javascript

```
const numbers = [1, 2, 3];
numbers[10] = 11;

console.log(numbers);
```

## Output

javascript

```
[1, 2, 3, empty × 7, 11]
```

## Explanation

JavaScript creates **empty slots** between index `3` and `9`.

This is called a **sparse array**.

---

# 6. Variable Leakage

## Code

javascript

```
{
  function display() {
    var a = (b = 10);
  }

  display();

  console.log(typeof b === "undefined");
  console.log(typeof a === "undefined");
}
```

## Output

javascript

```
false
true
```

## Explanation

javascript

```
var a = (b = 10)
```

means

javascript

```
b = 10
var a = b
```

- `b` becomes a **global variable**
- `a` remains local to the function

---

# 7. setTimeout with var

var vs let in a loop + setTimeoutvar (shared)One i shared by all iterationsLoop ends → i = 3Logs 3, 3, 3let (per-iteration)New i bound each iterationEach callback keeps its own iLogs 0, 1, 2vs

*let is block-scoped, so every loop turn captures a fresh binding — the classic closure fix.*

## Code

javascript

```
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), i * 10);
}
```

## Output

javascript

```
3
3
3
```

## Explanation

`var` is function scoped.

By the time callbacks run, `i` becomes `3`.

---

# 8. Variable Redeclaration

## Code

javascript

```
var num = 8;
var num = 10;

console.log(num);
```

## Output

javascript

```
10
```

## Explanation

`var` allows **re-declaration**, so the second assignment overwrites the first.

---

# 9. Event Loop Order

## Code

javascript

```
const foo = () => console.log("First");
const bar = () => setTimeout(() => console.log("Second"));
const baz = () => console.log("Third");

bar();
foo();
baz();
```

## Output

javascript

```
First
Third
Second
```

## Explanation

Execution order:

1. `bar()` schedules setTimeout
2. `foo()` runs
3. `baz()` runs
4. setTimeout runs later

---

# 10. Microtasks vs Macrotasks

The Event LoopCall Stackmain()console.log()runs to emptyMicrotask queue — Promise .then / awaitthen Athen BMacrotask queue — setTimeout / eventssetTimeoutWhen the stack is empty: drain ALL microtasks, then take ONE macrotask — and repeat.

*This ordering is why a Promise callback always runs before a setTimeout(…, 0) scheduled at the same time.*

## Code

javascript

```
console.log("one");

setTimeout(function () {
  console.log("two");
}, 0);

Promise.resolve().then(function () {
  console.log("three");
});

console.log("four");
```

## Output

javascript

```
one
four
three
two
```

## Explanation

Execution order:

1. synchronous code
2. microtasks (Promises)
3. macrotasks (setTimeout)

---

# 11. Hoisting Example

Hoisting: two phasesCreation phasevar x → hoisted, = undefinedfunction foo(){} → whole bodylet / const → TDZ (unusable)Execution phasex = 5 (assignment runs)code runs top to bottomTDZ ends at the declaration

*Declarations are set up first; assignments happen when the line actually runs. That gap explains most hoisting puzzles.*

## Code

javascript

```
var a = 1;

if (true) {
  function a() {}
  var a = 10;
}

console.log(a);
```

## Output

javascript

```
10
```

---

# 12. Function vs Variable Hoisting

## Code

javascript

```
console.log(typeof foo);

function foo() {
  console.log("1");
}

var foo = 9;

console.log(typeof foo);
```

## Output

javascript

```
function
number
```

## Explanation

Function declarations are hoisted before variables.

---

# 13. Variable Hoisting in Functions

## Code

javascript

```
var name = "outer";

function foo() {
  console.log(name);
  var name = "inner";
}

foo();
```

## Output

javascript

```
undefined
```

## Explanation

Inside the function:

javascript

```
var name;
console.log(name);
name = "inner";
```

---

# 14. Prototype Inheritance

The prototype chainconst dog = { bark() {} }the object you useAnimal.prototypeshared methods (eat, sleep)Object.prototypetoString, hasOwnProperty…nullend of the chain

*Miss a property on the object? JS walks UP the chain until it finds it or hits null. That lookup is prototypal inheritance.*

## Code

javascript

```
const a = { foo: 123 };
const b = Object.create(a);

console.log(b.foo);

b.foo = 444;
console.log(b.foo);

delete b.foo;
console.log(b.foo);
```

## Output

javascript

```
123
444
123
```

## Explanation

`b` inherits `foo` from `a`.

Deleting `b.foo` exposes the prototype property again.

---

# 15. arguments Object Trick

## Code

javascript

```
var length = 4;

function callback() {
  console.log(this.length);
}

const object = {
  length: 5,
  method() {
    arguments[0]();
  }
};

object.method(callback, 1, 2);
```

## Output

javascript

```
3
```

---

# 16. arguments vs normal call

## Code

javascript

```
var length = 4;

function callback() {
  console.log(this.length);
}

const object = {
  length: 5,
  method(callback) {
    callback();
  }
};

object.method(callback, 1, 2);
```

## Output

javascript

```
4
```

---

# 17. Function Hoisting with Duplicate Names

## Code

javascript

```
function x() {
  a();
  function a() {
    console.log("m");
  }

  a();

  function a() {
    console.log("n");
  }

  a();
}

x();
```

## Output

javascript

```
n
n
n
```

## Explanation

Function declarations are hoisted.  
The **last declaration overrides previous ones**.

---

# 18. Async Await Execution

## Code

javascript

```
async function data() {
  console.log("3");

  await new Promise((res) => setTimeout(res, 1000));

  console.log("4");
}

console.log("1");
data();
console.log("2");
```

## Output

javascript

```
1
3
2
4
```

---

# 19. Promise + setTimeout

## Code

javascript

```
console.log("1");

setTimeout(() => {
  console.log("3");
});

Promise.resolve("4").then(() => setTimeout(() => console.log("4")));

Promise.resolve("5").then((data) => console.log(data));

console.log("2");
```

## Output

javascript

```
1
2
5
3
4
```

---

# 20. Constructor Function

## Code

javascript

```
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}

const lydia = new Person("Lydia", "Hallie");
const sarah = Person("Sarah", "Smith");

console.log(lydia);
console.log(sarah);
```

## Output

javascript

```
Person { firstName: "Lydia", lastName: "Hallie" }
undefined
```

---

# 21. Generator Functions

## Code

javascript

```
function* generatorFunction() {
  yield 1;
  yield 2;
  return 3;
}

const generator = generatorFunction();

console.log(generator.next());
console.log(generator.next());
console.log(generator.next());
```

## Output

javascript

```
{ value: 1, done: false }
{ value: 2, done: false }
{ value: 3, done: true }
```

---

# 22. Prototype Chain Lookup

## Question

What will be printed?

javascript

```
const animal = {
  sound: "generic"
};

const dog = Object.create(animal);

dog.sound = "bark";

delete dog.sound;

console.log(dog.sound);
```

---

## Answer

javascript

```
generic
```

---

## Explanation

Steps:

1. `dog` inherits from `animal`
2. `dog.sound = "bark"` creates its own property
3. `delete dog.sound` removes the property from `dog`
4. JavaScript looks up the **prototype chain**

So it finds:

javascript

```
animal.sound → "generic"
```

---

# 23. Arrow Function and `this`

this: arrow vs normal functionNormal functionthis set by HOW it is calledobj.fn() → this = objStandalone → undefined / windowArrow functionNo own thisInherits this from where definedIgnores call-sitevs

*Arrows capture the surrounding this lexically — great for callbacks, wrong for object methods.*

## Question

javascript

```
const obj = {
  name: "JS",
  show: () => {
    console.log(this.name);
  }
};

obj.show();
```

---

## Answer

javascript

```
undefined
```

---

## Explanation

Arrow functions **do not have their own** `this`.

They inherit `this` from the **lexical scope**.

In this case:

javascript

```
this → global object
```

Global object does not have `name`.

So output:

javascript

```
undefined
```

---

# 24. setTimeout and Closure with let

## Question

javascript

```
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 10);
}
```

---

## Answer

javascript

```
0
1
2
```

---

## Explanation

`let` is **block scoped**.

Each loop iteration creates a **new binding of** `i`.

So callbacks capture:

javascript

```
0
1
2
```

instead of `3`.

---

# 25. Promise Execution Order

What runs first1 · Synchronousruns now, top to bottom2 · MicrotasksPromise .then / await3 · MacrotaskssetTimeout, events

*All sync code, THEN every queued microtask, THEN one macrotask. Memorize this order and output questions become easy.*

## Question

javascript

```
console.log("Start");

Promise.resolve().then(() => {
  console.log("Promise 1");
});

Promise.resolve().then(() => {
  console.log("Promise 2");
});

console.log("End");
```

---

## Answer

javascript

```
Start
End
Promise 1
Promise 2
```

---

## Explanation

Execution order:

### 1. Synchronous code

javascript

```
Start
End
```

### 2. Microtasks (Promises)

javascript

```
Promise 1
Promise 2
```

Promises run **after the call stack is empty**.

---

# 26. Object Reference

## Question

javascript

```
const obj1 = { a: 1 };
const obj2 = obj1;

obj2.a = 5;

console.log(obj1.a);
```

---

## Answer

javascript

```
5
```

---

## Explanation

Objects are assigned **by reference**, not by value.

javascript

```
obj1 → memory location
obj2 → same memory location
```

Changing `obj2` also changes `obj1`.

---

# 27. Array Reference Behavior

## Question

javascript

```
const arr = [1,2,3];

const copy = arr;

copy.push(4);

console.log(arr);
```

---

## Answer

javascript

```
[1,2,3,4]
```

---

## Explanation

Arrays are also **reference types**.

javascript

```
arr → reference
copy → same reference
```

So modifying one affects the other.

---

# 28. Spread Operator Copy

## Question

javascript

```
const arr = [1,2,3];

const copy = [...arr];

copy.push(4);

console.log(arr);
console.log(copy);
```

---

## Answer

javascript

```
[1,2,3]
[1,2,3,4]
```

---

## Explanation

Spread operator creates a **shallow copy**.

So:

javascript

```
arr and copy are different arrays
```

Changing one does not affect the other.

---

# 29. Equality Comparison

## Question

javascript

```
console.log([] == []);
console.log([] === []);
```

---

## Answer

javascript

```
false
false
```

---

## Explanation

Arrays are **objects**, so equality compares **references**.

Each array is stored at a different memory location.

javascript

```
[] !== []
```

---

# 30. NaN Comparison

## Question

javascript

```
console.log(NaN === NaN);
console.log(Object.is(NaN, NaN));
```

---

## Answer

javascript

```
false
true
```

---

## Explanation

`NaN` is the **only value in JavaScript that is not equal to itself**.

javascript

```
NaN === NaN → false
```

But:

javascript

```
Object.is(NaN, NaN) → true
```

because `Object.is()` checks values more precisely.

---

---

## Learn More

- [JavaScript output questions (repo) github.com](https://github.com/lydiahallie/javascript-questions)
- [The Modern JavaScript Tutorial javascript.info](https://javascript.info)
- [Solving output-based JS questions YouTube](https://www.youtube.com/watch?v=UXGwPCOkg6M)
- [Top 20 output-based questions YouTube](https://www.youtube.com/watch?v=cmkSBoUAbCg)
- [JS output tricky questions YouTube](https://www.youtube.com/watch?v=moNpNrd41jk)
