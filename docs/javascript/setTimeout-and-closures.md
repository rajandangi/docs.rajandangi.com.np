---
icon: material/arrow-right
---

# :question: Interview Questions: setTimeout and Closures

---

**Prerequisite:**
If you don’t understand how Closures work, I highly recommend reading this first:

:octicons-link-external-16: [Closures in JavaScript](closures.md)

---

!!! answer "Basic Definition: `setTimeout`"

    {==

    The `setTimeout()` function executes a provided function or code snippet once after a specified delay (in milliseconds).

    **Syntax:**
    ```javascript
    setTimeout(callbackFunction, delayInMilliseconds);
    ```

    *   `callbackFunction`: The function to run after the delay.
    *   `delayInMilliseconds`: The time, in milliseconds, to wait before executing the function.

    ==}

Let's look at a simple example:

```javascript linenums="1" title="index.js"
function x() {
    var i = 99;
    // Schedule the console.log(i) to run after 3000ms (3 seconds)
    setTimeout(function () {
        console.log(i); // Accesses 'i' via closure
    }, 3000);
    console.log('Hello'); // This runs almost immediately
}

x();
```

If we run this program, what do you expect to see in the console?

```console title="Expected Output"
Hello
99
```

You might initially expect `setTimeout()` to pause execution for 3 seconds, print `i`, and *then* print 'Hello'. But that's not how it works! It prints "Hello" first, then waits 3 seconds, and finally prints the value of `i`.

## :confused: What `setTimeout()` Actually Does

1.  **Closure:** The anonymous function `function () { console.log(i); }` passed to `setTimeout` forms a closure. This means it "remembers" the variable `i` from its surrounding scope (`function x`). Crucially, it remembers the *reference* to `i`, not its value at that exact moment.
2.  **Scheduling:** `setTimeout()` takes this callback function, hands it off to the browser's timer mechanism (or equivalent in other environments), and sets the 3000ms timer.
3.  **Non-Blocking:** JavaScript execution *doesn't* pause. It immediately moves to the next line: `console.log('Hello');`.
4.  **Callback Execution:** After 3000ms elapse, the timer mechanism places the callback function onto the message queue. When the JavaScript call stack is empty, the event loop picks up the callback from the queue and executes it. At this point, the callback accesses the current value of the `i` it closed over (which is still 99).

Now, let's tackle a common interview question that combines `setTimeout` and closures within a loop.

## :computer: The Challenge: Sequential Logging with Delay

**Problem:** Print the numbers 1, 2, 3, 4, 5 to the console, but with a delay. Print 1 after 1 second, 2 after 2 seconds, 3 after 3 seconds, and so on. How would you achieve this?

A common first attempt involves using a `for` loop with `setTimeout()` inside, like this:

```javascript linenums="1" title="index.js"
function y() {
    for (var i = 1; i <= 5; i++) {
        setTimeout(function () {
            console.log(i); // What will this print?
        }, i * 1000);
    }
    console.log('Hello');
}
y();
```

Let’s check the output:

```console title="Expected Output"
Hello
6
6
6
6
6
```

This is not what we wanted! Why does it print 6 five times? 🧐

**Explanation:**

1.  **Closures Capture References:** As we established, the callback function passed to `setTimeout` forms a closure. When using `var`, this closure captures a *reference* to the *same* variable `i` across all loop iterations. `var` has function scope (or global scope), not block scope, so there's only one `i` variable being updated.
2.  **Loops Finish Quickly:** JavaScript doesn't wait for `setTimeout` timers. The `for` loop runs to completion almost instantly. It schedules five `setTimeout` callbacks (for 1000ms, 2000ms, ..., 5000ms).
3.  **Final Value of `i`:** By the time the loop finishes, the condition `i <= 5` becomes false. This happens when `i` is incremented to 6. So, the single `i` variable now holds the value 6.
4.  **Callback Execution Time:** Later, when the first timer (1000ms) expires and its callback function finally runs, it looks up the value of the `i` it has a reference to. At this point, `i` is 6. The same happens for the callbacks at 2000ms, 3000ms, etc. All five callbacks reference the same `i`, which holds the value 6.

## 🤯 Why Do They Point to the Same Reference?

Because `var` declarations are scoped to the nearest function (or globally), not to the loop block (`{...}`). In the `y()` function, there is only *one* `i` variable created. Each iteration of the loop updates this single variable. All the `setTimeout` callbacks created within the loop close over this *same* `i`.

## 💡 How Can We Fix This?

We need each `setTimeout` callback to close over the value of `i` as it was during that *specific* loop iteration.

**Solution 1: Use `let` (Modern JavaScript)**

The simplest and most common solution today is to use `let` instead of `var`:

```javascript linenums="1" title="index.js"
function z() {
    for (let i = 1; i <= 5; i++) {
        // 'let' creates a new binding for 'i' in each iteration
        setTimeout(function () {
            console.log(i); // Prints 1, 2, 3, 4, 5 correctly
        }, i * 1000);
    }
    console.log('Hello');
}
z();
```

```console title="Expected Output"
Hello
1
2
3
4
5
```

It works!

### ✨ Why `let` Works

1.  **Block Scope:** `let` (and `const`) have *block scope*. This means a new `i` variable is created for *each iteration* of the `for` loop block.
2.  **Separate Closures:** When the `setTimeout` callback is created in each iteration, it forms a closure over that iteration's *specific*, unique `i` variable.
3.  **Correct Values Captured:** The first callback closes over an `i` with value 1, the second closes over a *different* `i` with value 2, and so on. When the callbacks eventually execute, they each access the correct value they captured.

**Solution 2: Use `var` with Closures (Older Approach)**

What if you *must* use `var` (perhaps due to older environment constraints or interview requirements)? You need to manually create a new scope for each iteration.

**Method A: Using a separate function**

```javascript linenums="1" title="index.js"
function a() {
    for (var i = 1; i <= 5; i++) {
        // Create a new scope by calling a function
        function createClosure(value) {
            setTimeout(function () {
                console.log(value); // Closes over 'value'
            }, value * 1000);
        }
        createClosure(i); // Pass the current value of 'i'
    }
    console.log('Hello');
}
a();
```

**Method B: Using an Immediately Invoked Function Expression (IIFE)**

```javascript linenums="1" title="index.js"
function b() {
    for (var i = 1; i <= 5; i++) {
        // Create a new scope using an IIFE
        (function (value) {
            setTimeout(function () {
                console.log(value); // Closes over 'value'
            }, value * 1000);
        })(i); // Immediately call the function, passing the current 'i'
    }
    console.log('Hello');
}
b();
```

Both `a()` and `b()` produce the desired output:

```console title="Expected Output"
Hello
1
2
3
4
5
```

**Why these `var` solutions work:**

1.  **New Scope Creation:** Both the separate function (`createClosure`) and the IIFE create a *new function scope* during each loop iteration.
2.  **Passing by Value:** The loop's `i` is passed as an argument (`value`) into this new scope. Inside the scope, `value` holds the value of `i` *at that specific moment* in the iteration (1, then 2, then 3...).
3.  **Closure Captures Local Value:** The `setTimeout` callback is created *inside* this new scope. It forms a closure over the `value` variable, which is local to that scope and holds the correct number for that iteration.

---


For more examples and explanations of `setTimeout`, you might find this article helpful:
[Playing with JavaScript setTimeout](https://medium.com/swlh/playing-with-javascript-settimeout-11201cd0d0c)