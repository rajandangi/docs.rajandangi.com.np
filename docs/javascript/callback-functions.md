---
icon: material/arrow-right
---

# 🔁 Callback Functions

Callbacks are ordinary functions with a particular job. You give a function to
some other code, and that code decides when to call it.

```javascript
function sayHello() {
  console.log('Hello');
}

setTimeout(sayHello, 1000);
```

Here, `sayHello` is the callback. The timer receives it now and calls it later.

Most callback confusion comes from one small pair of parentheses:

```javascript
sayHello;   // The function itself.
sayHello(); // Call the function now.
```

That distinction is the heart of this chapter.

!!! info "Useful background"
    This chapter assumes you already know that JavaScript functions can be passed
    around as values. If that idea is new, begin with [First-Class
    Functions](first-class-functions.md) and [Higher-Order
    Functions](higher-order-functions.md).

## ☎️ What Makes a Function a Callback?

JavaScript has no `callback` keyword. A function becomes a callback because of how
it is used.

```javascript
function runTask(task) {
  task();
}

runTask(function printMessage() {
  console.log('Task is running');
});
```

`printMessage` is passed into `runTask`. The parameter named `task` holds that
function, and `task()` invokes it.

The code receiving a callback controls the callback's contract:

- when it runs;
- how many times it can run;
- which arguments it receives;
- what happens to its return value;
- how errors are handled.

!!! note "Callback does not mean asynchronous"
    `Array.prototype.map()` invokes its callback immediately for every item.
    `setTimeout()` invokes its callback later. Both functions accept callbacks,
    but their timing is different.

## 🕶️ Anonymous Function and Callback Are Not Synonyms

**Anonymous** describes whether a function has a name. **Callback** describes how
the function is used. This is an anonymous callback:

```javascript
setTimeout(function () {
  console.log('Anonymous callback');
}, 1000);
```

A callback can also be named:

```javascript
function showMessage() {
  console.log('Named callback');
}

setTimeout(showMessage, 1000);
```

See [First-Class Functions](first-class-functions.md#anonymous-function) for the
different ways functions can be declared and named.

## ⚖️ Passing a Function vs. Calling It

Suppose we want to display a reminder after one second:

```javascript
function showReminder() {
  console.log('Time for a break');
}
```

### Calling it too early

```javascript
setTimeout(showReminder(), 1000);
```

JavaScript evaluates function arguments before it calls `setTimeout`. The order is:

1. Call `showReminder()` immediately.
2. Take its return value, which is `undefined` here.
3. Pass that return value to `setTimeout`.

The timer never receives the function.

### Passing the function

```javascript
setTimeout(showReminder, 1000);
```

Without parentheses, `showReminder` refers to the function itself. The timer can
store that reference and call it after the delay.

### Passing a wrapper

```javascript
setTimeout(function () {
  showReminder();
}, 1000);
```

The anonymous function is created immediately, but its body does not run yet.
`showReminder()` is inside that body, so it waits until the timer calls the
wrapper.

The arrow-function version works the same way:

```javascript
setTimeout(() => {
  showReminder();
}, 1000);
```

!!! tip "Read the code literally"
    - `setTimeout(showReminder(), 1000)` means “call it now and pass its result.”
    - `setTimeout(showReminder, 1000)` means “pass it so the timer can call it.”
    - `setTimeout(() => showReminder(), 1000)` means “pass a function that will
      call it.”

## 📦 When a Wrapper Is Useful

Pass the original function when it already has the shape you need:

```javascript
setTimeout(showReminder, 1000);
```

Use a wrapper when you need to supply arguments:

```javascript
function greet(name) {
  console.log(`Hello, ${name}`);
}

setTimeout(() => greet('Maya'), 1000);
```

Calling `greet` directly would run it too early:

```javascript
setTimeout(greet('Maya'), 1000); // Incorrect.
```

A wrapper can also make a decision at execution time:

```javascript
setTimeout(() => {
  if (document.visibilityState === 'visible') {
    showReminder();
  }
}, 1000);
```

The visibility check happens when the callback runs, not when the timer is created.

## 🧩 The Caller Supplies the Arguments

A callback does not choose which arguments it receives. The code invoking it does.
For example, `map()` passes the current value, its index, and the original array:

```javascript
const colours = ['Blue', 'Green'];

const labels = colours.map(function (colour, index) {
  return `${index + 1}. ${colour}`;
});

console.log(labels); // ["1. Blue", "2. Green"]
```

This is why a wrapper can be useful when an existing function expects a different
signature:

```javascript
['1', '2', '3'].map(parseInt); // [1, NaN, NaN]
```

`map` passes the index as the second argument. `parseInt` treats that argument as
the radix. A wrapper adapts one contract to the other:

```javascript
['1', '2', '3'].map((value) => parseInt(value, 10)); // [1, 2, 3]
```

## ↩️ Return Values and Async Boundaries

`return` sends a value back to the function's caller. It does not travel backwards
through time to a function that has already finished.

```javascript
function getMessage() {
  setTimeout(() => {
    return 'Finished';
  }, 1000);
}

console.log(getMessage()); // undefined
```

`getMessage()` finishes before the timer callback runs. When the callback later
returns `'Finished'`, it returns that value to the timer machinery, which does not
use it.

In callback-style code, pass the result to another callback:

```javascript
function getMessage(onComplete) {
  setTimeout(() => {
    onComplete('Finished');
  }, 1000);
}

getMessage(function (message) {
  console.log(message);
});
```

Promises provide a cleaner way to represent a future result. That subject belongs
in [Promises](promises.md), where chaining, rejection, and `async`/`await` are
covered properly.

## 🪪 Function Identity Matters

Every function expression creates a new function object:

```javascript
console.log(function () {} === function () {}); // false
```

This matters when an API expects the same callback reference later. The following
code does not remove the listener:

```javascript
button.addEventListener('click', function () {
  console.log('Clicked');
});

button.removeEventListener('click', function () {
  console.log('Clicked');
});
```

The two functions look identical, but they are different objects. Store or name
the callback when you will need it again:

```javascript
function handleClick() {
  console.log('Clicked');
}

button.addEventListener('click', handleClick);
button.removeEventListener('click', handleClick);
```

The same issue appears when setup code creates a fresh listener every time it runs:

```javascript
function render() {
  window.addEventListener('resize', () => updateLayout());
}
```

Each call to `render()` creates another function. Long-lived listeners should
normally be registered once or removed during cleanup.

## 🔍 A Quick Debugging Routine

When a callback behaves unexpectedly, ask:

1. Am I passing the function or calling it?
2. Who invokes the callback?
3. Does it run now or later?
4. Which arguments does the caller supply?
5. Can it run more than once?
6. Will I need the same function reference for cleanup?

Once the hand-off itself is clear, follow the topic that owns the remaining
problem:

| Problem | Continue with |
| --- | --- |
| A callback sees an unexpected outer variable | [Closures](closures.md) |
| A method loses its `this` value | [`call`, `apply`, and `bind`](call-apply-bind.md) |
| A timer or event runs in an unexpected order | [Asynchronous JavaScript and the Event Loop](async-js-and-event-loop.md) |
| Nested callbacks hide the workflow | [Callback Hell](callback-hell.md) |
| A future result or error is difficult to pass along | [Promises](promises.md) |

## 🧠 Keep This Picture in Your Head

```javascript
function save() {}     // Create a function.

const task = save;      // Store or pass the function.
task();                 // Invoke the function.

const later = () => {   // Create a wrapper.
  save();               // This runs when later() is invoked.
};

setTimeout(later, 1000);
```

If you can identify who owns each pair of parentheses and when that line runs, you
can follow most callback code without guessing.

## 📚 Continue Learning

- [First-Class Functions](first-class-functions.md) for function forms and values
- [Higher-Order Functions](higher-order-functions.md) for functions that receive or return functions
- [Closures](closures.md) for variables remembered by callbacks
- [`setTimeout` and Closures](setTimeout-and-closures.md) for timer loop questions
- [Asynchronous JavaScript and the Event Loop](async-js-and-event-loop.md) for scheduling
- [Callback Hell](callback-hell.md) for nested asynchronous workflows
- [Promises](promises.md) for future values and error propagation
