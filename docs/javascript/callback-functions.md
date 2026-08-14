---
icon: material/arrow-right
---

# 🔁 Callback Functions and Anonymous Functions

Callbacks are one of those JavaScript ideas that sound harder than they are. The
function itself is ordinary. What changes is who gets to call it.

You hand a function to a timer, a button, an array method, or another piece of
code. That code calls your function when its job reaches the right point. That is
the whole idea, but one small pair of parentheses causes a surprising amount of
confusion.

!!! success "Callback in one sentence"
    **A callback is a function you give to other code so that code can decide when
    to run it.**

Start with these two lines:

```javascript
doSomething;   // The function value. No execution yet.
doSomething(); // Invoke the function now and produce its return value.
```

If that distinction feels completely natural by the end of this guide, callbacks
will stop looking mysterious.

## 🧱 Functions Are Values

JavaScript functions are [first-class values](first-class-functions.md). You can
store one in a variable, put one in an object, return one from another function,
or pass one as an argument. Strings and numbers are values, and functions are too.

```javascript
function greet() {
  console.log('Hello');
}

const anotherReference = greet;

console.log(typeof greet);            // "function"
console.log(greet === anotherReference); // true

anotherReference(); // "Hello"
```

`anotherReference` does not contain the *result* of `greet()`. It points to the
same function. Nothing runs until we add parentheses.

Compare that with this:

```javascript
function greet() {
  console.log('Hello');
  return 'finished';
}

const functionReference = greet; // Store the function.
const returnedValue = greet();    // Run it now and store "finished".
```

| Expression | Meaning | Value produced |
| --- | --- | --- |
| `greet` | Refer to the function | The function object |
| `greet()` | Invoke the function now | Whatever `greet` returns |
| `() => greet()` | Create a new function that will invoke `greet` later | A new function object |

## ☎️ So, What Makes a Function a Callback?

There is no special callback syntax and no callback keyword. A normal function
becomes a **callback** when we pass it to other code and let that code invoke it.

The function receiving a callback is often called a **higher-order function**.

```javascript
function runTask(task) {
  console.log('Before the task');
  task();
  console.log('After the task');
}

function sayHello() {
  console.log('Hello');
}

runTask(sayHello);
```

Output:

```text
Before the task
Hello
After the task
```

`sayHello` is a regular function. In this example it plays the role of a callback
because `runTask` receives it and decides when to call it.

!!! note "Two common misconceptions"
    - A callback does **not** have to be anonymous.
    - A callback does **not** have to be asynchronous.

## 🕶️ Anonymous Is About the Name, Callback Is About the Job

An **anonymous function** is simply a function without an explicit name:

```javascript
const greet = function () {
  console.log('Hello');
};
```

The function is anonymous even though the variable holding it is named `greet`.
Modern JavaScript engines often infer the name `greet` for debugging, but the
function expression itself has no explicit name.

Anonymous functions are commonly created inline where a function value is needed:

```javascript
setTimeout(function () {
  console.log('Timer finished');
}, 1000);

document.addEventListener('click', () => {
  console.log('Document clicked');
});
```

This gives us an important distinction. **Anonymous** describes the function's
name. **Callback** describes the job it is doing.

| Term | Describes | Example |
| --- | --- | --- |
| **Anonymous function** | Whether the function has its own name | `function () {}` |
| **Named function** | A function with an explicit name | `function saveDraft() {}` |
| **Callback function** | How a function is passed and later invoked | `setTimeout(saveDraft, 1000)` |

The same function can be both anonymous and a callback:

```javascript
setTimeout(function () {
  console.log('Anonymous callback');
}, 1000);
```

Or named and a callback:

```javascript
function announceCompletion() {
  console.log('Named callback');
}

setTimeout(announceCompletion, 1000);
```

## ⚖️ The Parentheses That Change Everything

Suppose we want to autosave after one second:

```javascript
function autosaveLMS() {
  console.log('Saving now');
}
```

### Incorrect: invoke it while registering the timer

```javascript
setTimeout(autosaveLMS(), 1000);
```

This looks close, but the parentheses tell JavaScript to run `autosaveLMS` while
it is preparing the arguments for `setTimeout`. The actual order is:

1. Evaluate `autosaveLMS()`.
2. `autosaveLMS` runs immediately.
3. Take its return value, normally `undefined`.
4. Pass that return value to `setTimeout` instead of passing a function.

The timer never receives the function. It receives whatever the function returned.

### Correct: pass the function itself

```javascript
setTimeout(autosaveLMS, 1000);
```

Without parentheses, the timer receives the function itself. It can keep that
reference and invoke it after the delay.

### Also correct: pass a wrapper function

```javascript
setTimeout(function () {
  autosaveLMS();
}, 1000);
```

Or with an arrow function:

```javascript
setTimeout(() => {
  autosaveLMS();
}, 1000);
```

The wrapper is created immediately, but creating a function is not the same as
running its body. `autosaveLMS()` sits inside that body and waits until the timer
calls the wrapper.

### Why the wrapper behaves differently

Compare when the parentheses are evaluated:

```javascript
setTimeout(autosaveLMS(), 1000);
//         ^^^^^^^^^^^^^
// Evaluated now as an argument to setTimeout.

setTimeout(function () {
  autosaveLMS();
  // ^^^^^^^^^^^
  // Evaluated later, inside the callback body.
}, 1000);
```

!!! tip "Read it out loud"
    - `setTimeout(autosaveLMS(), 1000)` means “call `autosaveLMS` now, then pass
      its result.”
    - `setTimeout(autosaveLMS, 1000)` means “pass `autosaveLMS` so the timer can
      call it later.”
    - `setTimeout(() => autosaveLMS(), 1000)` means “pass a new function that
      will call `autosaveLMS` later.”

## 📦 When a Wrapper Earns Its Keep

Pass the original function when it already does exactly what you need:

```javascript
setTimeout(autosaveLMS, 1000);
```

A wrapper becomes useful when you need to:

- pass arguments;
- call more than one function;
- add a condition;
- transform the callback's input or output;
- preserve a particular lexical `this` with an arrow function.

### Pass arguments later

```javascript
function saveCourse(courseId, force) {
  console.log({ courseId, force });
}

setTimeout(() => saveCourse(58, true), 1000);
```

This is wrong if the intention is to delay the call:

```javascript
setTimeout(saveCourse(58, true), 1000);
```

### Perform multiple operations later

```javascript
setTimeout(() => {
  showSavingIndicator();
  autosaveLMS();
  recordAnalyticsEvent();
}, 1000);
```

### Decide at execution time

```javascript
setTimeout(() => {
  if (document.visibilityState === 'visible') {
    autosaveLMS();
  }
}, 1000);
```

The visibility check happens when the callback runs, not when the timer is
registered.

## 🧩 Who Supplies the Callback Arguments?

The caller chooses the arguments. That sounds obvious, but it explains many odd
callback bugs. When your code calls `callback(user)`, the callback gets a user. When
the browser calls an event listener, the listener gets an event object.

```javascript
function processUser(callback) {
  const user = { id: 42, name: 'Rajan' };
  callback(user);
}

processUser(function (user) {
  console.log(user.name); // "Rajan"
});
```

Every callback-based API has a small contract. For example,
`Array.prototype.map` passes the current value, index, and original array:

```javascript
const courses = ['JavaScript', 'PHP'];

const labels = courses.map(function (course, index) {
  return `${index + 1}. ${course}`;
});

console.log(labels); // ["1. JavaScript", "2. PHP"]
```

Event listeners receive an event object:

```javascript
button.addEventListener('click', function (event) {
  console.log(event.type);          // "click"
  console.log(event.currentTarget); // The button.
});
```

### Timer arguments

Browsers can pass additional `setTimeout` arguments to the callback:

```javascript
function greet(name) {
  console.log(`Hello, ${name}`);
}

setTimeout(greet, 1000, 'Rajan');
```

The wrapper form is often easier to spot at a glance, and the same pattern works
with almost every callback API:

```javascript
setTimeout(() => greet('Rajan'), 1000);
```

## ⏱️ Callback Does Not Automatically Mean "Later"

A callback can run now or later. The API receiving it makes that decision.

### Synchronous callback

Array methods such as `map`, `filter`, and `forEach` invoke their callbacks during
the current call stack:

```javascript
console.log('A');

[1, 2, 3].forEach(function (number) {
  console.log(number);
});

console.log('B');
```

Output:

```text
A
1
2
3
B
```

### Asynchronous callback

Timers and event listeners arrange for callbacks to run after the current
synchronous work has finished and the relevant condition has been met:

```javascript
console.log('A');

setTimeout(function () {
  console.log('Timer callback');
}, 0);

console.log('B');
```

Output:

```text
A
B
Timer callback
```

Even a zero-millisecond timer does not interrupt the current call stack.

!!! warning "A callback does not make heavy work non-blocking"
    Moving a CPU-heavy loop into `setTimeout` only postpones the blocking work.
    When the callback eventually runs on the main thread, it can still freeze the
    page. Truly CPU-intensive browser work may need to be split into smaller tasks
    or moved to a Web Worker.

## ⌛ A Timer Is a Threshold, Not an Appointment

`setTimeout` does not reserve the JavaScript engine for an exact moment. Its delay
is a **minimum threshold**.

```javascript
setTimeout(runReport, 1000);
```

Read this as: make `runReport` eligible to run after roughly 1000 milliseconds. If
JavaScript is busy at that point, the callback waits its turn.

```javascript
const startedAt = Date.now();

setTimeout(() => {
  console.log(Date.now() - startedAt);
}, 100);

// Occupy the main thread for around 500 ms.
while (Date.now() - startedAt < 500) {
  // Busy work for demonstration only.
}
```

The callback cannot run after 100 ms because the main thread is still busy. The
logged time will be approximately 500 ms or more.

The simplified flow is:

1. JavaScript calls `setTimeout` with a callback and delay.
2. The host environment manages the timer.
3. Once the threshold passes, the callback becomes eligible for scheduling.
4. The event loop waits for the current call stack to become empty.
5. JavaScript invokes the callback.

For the full scheduling model, see [Asynchronous JavaScript and the Event
Loop](async-js-and-event-loop.md).

## ↩️ Where Does a Callback's Return Value Go?

`return` always returns to the function's caller. With a timer callback, the timer
machinery is the caller, not `getMessage`. That is why this code cannot return the
future message:

```javascript
function getMessage() {
  setTimeout(() => {
    return 'Finished';
  }, 1000);
}

const message = getMessage();
console.log(message); // undefined
```

`getMessage()` has already finished by the time the callback runs. One second
later, `'Finished'` goes back to the timer machinery, where nothing uses it.

With callback-style code, pass the result to another callback:

```javascript
function getMessage(onComplete) {
  setTimeout(() => {
    onComplete('Finished');
  }, 1000);
}

getMessage(function (message) {
  console.log(message); // "Finished"
});
```

With modern asynchronous code, a Promise can represent the future result:

```javascript
function getMessage() {
  return new Promise((resolve) => {
    setTimeout(() => resolve('Finished'), 1000);
  });
}

const message = await getMessage();
console.log(message); // "Finished"
```

See [Promises](promises.md) and [Promises with async/await](promises-async-await.md)
for the modern approach to composing asynchronous results.

## 🖱️ Event Listeners: Callbacks Waiting for a Signal

An event listener is a callback parked beside an event. The browser stores it and
invokes it whenever that event occurs:

```html
<button id="save-button">Save</button>

<script>
  const button = document.getElementById('save-button');

  button.addEventListener('click', function handleSaveClick(event) {
    console.log('Saving from', event.currentTarget);
  });
</script>
```

`handleSaveClick` is a **named callback**. The click determines when it runs.

### Do not invoke an event handler while registering it

```javascript
button.addEventListener('click', handleSaveClick()); // Incorrect.
button.addEventListener('click', handleSaveClick);   // Correct.
```

The first line invokes `handleSaveClick` during setup and passes its return value
as the listener. This is the same mistake as `setTimeout(autosaveLMS(), 1000)`.

### Removing a listener requires the same function reference

This does **not** remove the first listener:

```javascript
button.addEventListener('click', function () {
  console.log('Clicked');
});

button.removeEventListener('click', function () {
  console.log('Clicked');
});
```

Although the two functions contain identical code, they are two different function
objects.

```javascript
function handleClick() {
  console.log('Clicked');
}

button.addEventListener('click', handleClick);
button.removeEventListener('click', handleClick);
```

This works because both calls receive the same reference.

For one-time listeners, the browser also provides an option:

```javascript
button.addEventListener('click', handleClick, { once: true });
```

## 🏷️ Choosing Between Anonymous, Named, and Arrow Callbacks

All four forms below are valid. The useful question is not "Which syntax is best?"
but "Which one makes this particular callback easiest to understand and manage?"

```javascript
// Named function declaration.
function handleSave() {
  console.log('Saved');
}
setTimeout(handleSave, 1000);

// Anonymous function expression.
setTimeout(function () {
  console.log('Saved');
}, 1000);

// Named function expression.
setTimeout(function handleSaveTimer() {
  console.log('Saved');
}, 1000);

// Anonymous arrow function.
setTimeout(() => {
  console.log('Saved');
}, 1000);
```

### When an anonymous callback is a good choice

Use one when the callback is:

- short and used in only one place;
- immediately understandable beside the API call;
- not required later for cleanup;
- unlikely to need independent testing or reuse.

```javascript
const completedIds = modules
  .filter((module) => module.completed)
  .map((module) => module.id);
```

### When a named callback is a better choice

Name it when the callback:

- contains non-trivial business logic;
- is reused;
- must be removed later;
- is recursive;
- benefits from a meaningful stack-trace name;
- should be tested independently.

```javascript
function isCompletedModule(module) {
  return module.completed;
}

function getModuleId(module) {
  return module.id;
}

const completedIds = modules
  .filter(isCompletedModule)
  .map(getModuleId);
```

A name is not automatically an improvement. Use one when it helps explain intent,
debug a failure, reuse the code, or manage the callback's lifecycle.

## 🧭 The `this` Question

Regular functions get `this` from the way they are called. Arrow functions do not
create their own `this`; they borrow it from the surrounding scope. That difference
matters as soon as an object method becomes a callback.

```javascript
const course = {
  title: 'JavaScript Foundations',

  startWithRegularFunction() {
    setTimeout(function () {
      console.log(this.title); // Usually undefined in this example.
    }, 1000);
  },

  startWithArrowFunction() {
    setTimeout(() => {
      console.log(this.title); // "JavaScript Foundations"
    }, 1000);
  },
};
```

The arrow callback closes over the `this` value from `startWithArrowFunction`.
Another explicit option is `bind`:

```javascript
setTimeout(function () {
  console.log(this.title);
}.bind(course), 1000);
```

Event listeners require extra care. With a regular function, `this` is generally
the element on which the listener was registered; with an arrow function, it is
the surrounding lexical `this`. Prefer `event.currentTarget` when referring to the
element because the intent is clearer:

```javascript
button.addEventListener('click', (event) => {
  console.log(event.currentTarget);
});
```

## 🧠 How a Callback Remembers Its Surroundings

A callback carries access to the scope where it was created, even when it runs
later. That behaviour is called a [closure](closures.md).

```javascript
function createProgressTracker(courseName) {
  let completed = 0;

  return function recordCompletion() {
    completed += 1;
    console.log(`${courseName}: ${completed} complete`);
  };
}

const recordCompletion = createProgressTracker('JavaScript');

recordCompletion(); // "JavaScript: 1 complete"
recordCompletion(); // "JavaScript: 2 complete"
```

One detail is easy to miss: a closure keeps access to a variable, not a frozen copy
of its value. If the value changes before the callback runs, the callback sees the
new value:

```javascript
let status = 'draft';

setTimeout(() => {
  console.log(status); // "published"
}, 1000);

status = 'published';
```

Take a snapshot yourself when you need the old value:

```javascript
let status = 'draft';
const scheduledStatus = status;

setTimeout(() => {
  console.log(scheduledStatus); // "draft"
}, 1000);

status = 'published';
```

## 🚨 Errors Have to Be Caught Where They Happen

A `try...catch` around timer registration cannot catch an error thrown later by
the timer callback:

```javascript
try {
  setTimeout(() => {
    throw new Error('Autosave failed');
  }, 1000);
} catch (error) {
  // This does not catch the later error.
  console.error(error);
}
```

The outer `try` is long gone by the time the callback runs. Put the error handling
inside the asynchronous boundary:

```javascript
setTimeout(() => {
  try {
    riskyAutosave();
  } catch (error) {
    console.error('Autosave failed', error);
  }
}, 1000);
```

Promise-based APIs propagate asynchronous failure through rejection, allowing
`await` with `try...catch`.

### Error-first callbacks

Many traditional Node.js APIs use an **error-first callback** contract:

```javascript
readSomething(function (error, value) {
  if (error) {
    console.error(error);
    return;
  }

  console.log(value);
});
```

The first argument represents an error, and the later arguments contain successful
results. Always check the documented callback signature of the API you are using.

## 🪤 Callback Traps Worth Remembering

### 1. Calling instead of passing

```javascript
setTimeout(save(), 1000); // Wrong: save runs now.
setTimeout(save, 1000);   // Correct: the timer runs save later.
```

### 2. Expecting a delayed callback to return from the outer function

```javascript
function loadValue() {
  setTimeout(() => {
    return 42;
  }, 1000);
}

console.log(loadValue()); // undefined
```

Use another callback, a Promise, or `async`/`await` to communicate the future
result.

### 3. Losing the function reference needed for cleanup

```javascript
window.addEventListener('resize', () => updateLayout());

// There is no stored reference to pass to removeEventListener later.
```

When cleanup matters, store or name the callback:

```javascript
function handleResize() {
  updateLayout();
}

window.addEventListener('resize', handleResize);
window.removeEventListener('resize', handleResize);
```

### 4. Creating new listeners during repeated renders

```javascript
function renderForm() {
  window.addEventListener('click', () => handleGlobalClick());
}
```

Every render creates a new function object, so each one is a distinct listener.
Register global listeners once, or retain each reference and clean it up before
registering another. Registering the *same* callback reference more than once with
the same event type and capture option is ignored by `addEventListener`, but a new
inline wrapper has a new identity every time.

### 5. Accidentally relying on `this`

```javascript
setTimeout(course.save, 1000);
```

Passing a method separately from its object can lose its intended receiver. Use a
wrapper or bind it:

```javascript
setTimeout(() => course.save(), 1000);
setTimeout(course.save.bind(course), 1000);
```

### 6. Passing extra callback arguments unintentionally

Some array methods pass more arguments than a reused function expects:

```javascript
['1', '2', '3'].map(parseInt); // [1, NaN, NaN]
```

`map` passes `(value, index, array)`, while `parseInt` interprets its second
argument as the radix. Adapt the signature with a wrapper:

```javascript
['1', '2', '3'].map((value) => parseInt(value, 10)); // [1, 2, 3]
```

### 7. Creating callback hell

Deeply nested asynchronous callbacks make control flow and error handling hard to
follow:

```javascript
getUser(userId, (user) => {
  getCourse(user.courseId, (course) => {
    getProgress(course.id, (progress) => {
      renderProgress(progress);
    });
  });
});
```

Small named functions can help, while Promises and `async`/`await` usually make
sequential asynchronous workflows easier to compose. See [Callback
Hell](callback-hell.md).

## 🧹 Function Identity, Memory, and Cleanup

An event target holds on to its listeners. Those listeners may in turn hold values
through closures. That is useful while a feature is alive, but a forgotten
listener can retain memory and perform duplicate work long after the feature has
gone away.

```javascript
function mountCoursePage() {
  const largeCourseModel = loadCourseModel();

  function handleProgressUpdate() {
    renderCourse(largeCourseModel);
  }

  window.addEventListener('course-progress', handleProgressUpdate);

  return function unmountCoursePage() {
    window.removeEventListener('course-progress', handleProgressUpdate);
  };
}

const cleanup = mountCoursePage();

// Later, when the feature is removed:
cleanup();
```

The returned cleanup function closes over the exact callback reference used during
registration.

Timers can also be cancelled by retaining their IDs:

```javascript
const timerId = setTimeout(autosaveLMS, 1000);

// Cancel it if the user leaves before it runs.
clearTimeout(timerId);
```

## 🛠️ If You Design a Callback API

Do not make callers guess. A callback API should answer four questions:

1. **When** the callback runs.
2. Whether it can run **once or multiple times**.
3. Which **arguments** it receives.
4. How **errors and cleanup** work.

```javascript
function forEachCompletedModule(modules, callback) {
  for (const module of modules) {
    if (module.completed) {
      callback(module.id, module);
    }
  }
}

forEachCompletedModule(courseModules, (id, module) => {
  console.log(`Completed ${id}: ${module.title}`);
});
```

Here the callback runs synchronously, may run more than once, and receives an ID
plus the complete module. A reader can understand the contract without digging
through the implementation.

If the API performs one future operation that either succeeds or fails, returning
a Promise is often easier for callers than inventing a new callback convention.

## 🔍 Debugging a Callback Without Guessing

When a callback behaves strangely, walk through these questions in order:

1. Am I passing a function, or invoking it?
2. Who invokes the callback?
3. Is it invoked synchronously or asynchronously?
4. What arguments does the caller provide?
5. Can it run more than once?
6. What will `this` be?
7. Which variables does its closure capture?
8. How is it cancelled or removed?
9. Where must errors be handled?

### Add logs at registration and execution

```javascript
console.log('Registering autosave callback');

setTimeout(() => {
  console.log('Executing autosave callback');
  autosaveLMS();
}, 1000);

console.log('Registration complete');
```

The output makes the hand-off visible:

```text
Registering autosave callback
Registration complete
Executing autosave callback
```

### Prefer names for important callbacks

```javascript
setTimeout(function autosaveAfterIdlePeriod() {
  autosaveLMS();
}, 1000);
```

A meaningful name documents the intent and produces a more useful stack trace.

### Test the callback separately

Move substantial logic into a named function, test that function directly, and
keep the scheduling layer small:

```javascript
function buildProgressMessage(completed, total) {
  return `${completed} of ${total} modules complete`;
}

setTimeout(() => {
  statusElement.textContent = buildProgressMessage(3, 5);
}, 1000);
```

## ✅ Which Form Should I Use?

| Need | Prefer |
| --- | --- |
| Run an existing no-argument function later | `setTimeout(save, 1000)` |
| Supply arguments later | `setTimeout(() => save(id), 1000)` |
| Remove an event listener later | A stored or named function reference |
| Keep a tiny one-off transformation beside an array method | An inline arrow callback |
| Improve stack traces or test complex callback logic | A named callback |
| Preserve surrounding `this` | An arrow callback |
| Preserve an object's method receiver | `() => object.method()` or `object.method.bind(object)` |
| Represent one future success/failure result | Usually a Promise |
| Cancel a timer | Store its ID and call `clearTimeout(id)` |

## 🧪 Check Your Understanding

### When does `save` run?

```javascript
setTimeout(save(), 1000);
```

??? answer "Answer"
    Immediately. Its return value is passed to `setTimeout`.

```javascript
setTimeout(save, 1000);
```

??? answer "Answer"
    After the timer threshold has passed and JavaScript is able to run the
    callback.

```javascript
setTimeout(() => save(), 1000);
```

??? answer "Answer"
    The arrow function is created immediately. `save()` runs later, when the timer
    invokes the arrow function.

### Is every anonymous function a callback?

??? answer "Answer"
    No. “Anonymous” means the function has no explicit name. It becomes a callback
    only when it is passed to code that will invoke it.

### Is every callback asynchronous?

??? answer "Answer"
    No. `map`, `filter`, and `forEach` use synchronous callbacks. Timers and event
    listeners are asynchronous scheduling mechanisms.

### Why can two identical anonymous functions have different identities?

```javascript
console.log(
  function () {} === function () {}
); // false
```

??? answer "Answer"
    Each function expression creates a new function object. Equal-looking source
    code does not make them the same reference.

## 🧠 Put It All Together

Whenever callback code gets confusing, separate three actions: **creating** a
function, **passing** it somewhere, and **invoking** it.

```javascript
function save() {}       // Create a named function.
const task = save;        // Pass or store its function value.
task();                   // Invoke it.

const later = () => {     // Create an anonymous arrow function.
  save();                 // Invoke save only when later() runs.
};

setTimeout(later, 1000);  // Pass later to the timer.
```

Follow every pair of parentheses. Ask who placed it there and when that line gets
executed. That small habit untangles most callback code.

## 📚 Related Reading

- [First-Class Functions](first-class-functions.md)
- [Higher-Order Functions](higher-order-functions.md)
- [Closures](closures.md)
- [`setTimeout` and Closures](setTimeout-and-closures.md)
- [Asynchronous JavaScript and the Event Loop](async-js-and-event-loop.md)
- [Callback Hell](callback-hell.md)
- [Promises](promises.md)
- [Promises with async/await](promises-async-await.md)
