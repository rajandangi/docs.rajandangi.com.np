---
icon: material/arrow-right
---

# Promises, Async, and Await

Mastering asynchronous JavaScript can be tricky. This guide walks you through the problems callbacks introduce, how Promises help, and why `async`/`await` makes your code cleaner and more readable.

---

## :thinking: What Is `async` in JavaScript?

**Problem:** You want a function that always returns a Promise.

**Solution:** Prefix it with `async`.

- An `async` function always returns a Promise.
- If you return a non-Promise value, JavaScript wraps it in a resolved Promise.

```javascript
async function getData() {
  return "Hello World";  // Returns Promise.resolve("Hello World")
}

const dataPromise = getData();
console.log(dataPromise); // Promise {<fulfilled>: "Hello World"}
dataPromise.then(res => console.log(res)); // Hello World
```

If you return a Promise explicitly, it stays as is:

```javascript
const p = new Promise(resolve => setTimeout(() => resolve("Delayed"), 2000));

async function getData() {
  return p; // returns p unwrapped
}

getData().then(console.log); // after 2s logs "Delayed"
```

---

## :material-hand-heart-outline: Handling Promises: `.then()` vs `await`

### 1. Using `.then()` (Old Way)

```javascript title="Using .then()" linenums="1"
const p = new Promise(resolve => setTimeout(() => resolve("Hello"), 2000));

function getData() {
  p.then(res => console.log(res));
  console.log("Runs before promise resolves");
}

getData();
```

```console title="Console Output"
Runs before promise resolves
Hello
```

### 2. Using `async`/`await`

```javascript title="Using async/await" linenums="1"
async function getData() {
  console.log("Waiting for promise...");
  const res = await p;          // pauses here
  console.log(res);
  console.log("After await");
}

getData();
```

```console title="Console Output"
Waiting for promise...
Hello
After await
```

---

## :material-set-center: Behind the Scenes of `await`

When you `await` a Promise:

1. The async function is **suspended** at that point.
2. It’s **removed** from the call stack (the main thread is free).
3. When the Promise resolves, the function **resumes** where it left off.

This prevents blocking the UI and keeps your app responsive.

---

## :globe_with_meridians: Real-World Example: `fetch`

**Problem:** You need to fetch JSON data from an API.

```javascript
const API_URL = "https://api.github.com/users/yourusername";

async function fetchUser() {
  try {
    const response  = await fetch(API_URL);      // 1. fetch() returns a Promise
    const jsonValue = await response.json();      // 2. response.json() returns a Promise
    console.log(jsonValue);
  } catch (err) {
    console.error("Error fetching user:", err);
  }
}

fetchUser();
```


**Key Points:**

- [x] Both `fetch()` and `response.json()` return Promises.
- [x] `await` pauses only the async function, not the entire JavaScript engine.
- [x] Use `try/catch` to handle network or parsing errors.

---

## :warning: Error Handling

### 1. `try…catch` inside an async function

```javascript
async function safeFetch(url) {
  try {
    const res  = await fetch(url);
    const data = await res.json();
    return data;
  } catch (err) {
    console.error("Fetch error:", err);
  }
}
```

### 2. `.catch()` on the returned Promise

```javascript
async function badFetch() {
  const res = await fetch("https://invalid.url");
  return res.json();
}

badFetch().catch(err => console.error("Caught:", err));
```

---

## :loudspeaker: Quick Interview Tips

!!! question "What is `async`?"
    Marks a function to always return a Promise.

!!! question "What is `await`?"
    Pauses an async function until a Promise settles.

!!! question "Why use async/await?"
    Cleaner syntax, easier error handling.

!!! question "When not to use it?"
    For small one-off Promises or in environments without ES2017 support.

---

## :notepad_spiral: `async`/`await` vs `.then()`/`.catch()`

- **Syntax & Readability**

    - `.then()`: chaining callbacks, can become nested
    - `async/await`: flat, “top-to-bottom” flow

- **Error Handling**

    - `.catch()`: single or per-chain handlers
    - `try…catch`: group multiple awaits or isolate individual calls

- **Sequential vs. Parallel**

    - Sequential by default in both (`await a; await b;`)
    - Parallel with `Promise.all([…, …])`

- **Debugging & Stack Traces**

    - `.then()`: anonymous callbacks in traces
    - `async/await`: resembles sync code, easier breakpoints

- **When to Use**

    - `async/await`: multiple dependent steps, cleaner logic, modern codebases
    - `.then()/.catch()`: simple one-off Promises, existing promise-only libraries

Both patterns run on Promises; choose based on readability, error-handling style, and project needs. `async/await` is purely syntactic sugar.