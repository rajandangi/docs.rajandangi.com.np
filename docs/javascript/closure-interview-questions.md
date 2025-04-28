---
icon: material/arrow-right
---

# :question: Interview Questions: Closures

## :material-head-question: What are closures In JS?

Imagine a function that carries a little backpack with it. This backpack holds all the variables that were around when the function was created. That's essentially a closure!

More formally, a **closure** is a function that retains access to its **lexical scope** (the environment where it was written), even when the function is executed *outside* that original scope. It "remembers" the variables from its birthplace.

## :material-head-question: How you’ll explain more about it?

Every function in JavaScript automatically gets access to variables and functions defined in its parent's environment (its outer lexical scope).

The key part of a closure is this: even if you take that inner function and run it somewhere else entirely (like returning it from the outer function and calling it later), it *still* remembers and can use the variables from its original parent scope.

## :material-head-question: What about an example to explain all this?

Sure, let's look at a classic example:

```javascript
function outer() {
  var a = 10; // Variable defined in outer's scope

  function inner() {
    // inner() can access 'a' because it's in its lexical scope
    console.log(a);
  }

  return inner; // Return the inner function itself
}

// Execute outer(), which returns the inner function.
// Then immediately execute the returned inner function.
outer()(); // Output: 10
```

In this code:

1.  `inner()` is defined inside `outer()`.
2.  `inner()` has access to the variable `a` from `outer()`'s scope.
3.  We *return* `inner` from `outer`.
4.  When we call `outer()()`, the `inner` function is executed *after* `outer` has finished.
5.  Even though `outer` has finished, `inner` still remembers `a` (which is 10) because it formed a closure over `outer`'s scope.

So, `inner()` *is* the closure here.

## :material-head-question: What is `outer()()`? Why there are two parenthesis?

The double parentheses `()` `()` are used because `outer()` *returns a function*, and we then immediately want to *call* that returned function.

Let's break it down:

1.  `outer()`: This first call executes the `outer` function. Its job is to set up the variable `a` and return the `inner` function definition.
    *   Output of just `outer()`:
        ```console
        ƒ inner() {
            console.log(a);
        }
        ```
2.  `()`: This second set of parentheses calls the function that was *returned* by `outer()`, which is the `inner` function. This is when `console.log(a)` actually runs.
    *   Output of `outer()()`:
        ```console
        10
        ```

You could achieve the same result more explicitly like this:

```javascript
function outer() {
  var a = 10;
  function inner() {
    console.log(a);
  }
  return inner;
}

var close = outer(); // 'close' now holds the 'inner' function
close();             // Now call the 'inner' function. Output: 10
```

This shows clearly that `outer()` returns a function, which we store in `close`, and then we execute `close`. The `outer()()` syntax is just a shorthand for this.

## :material-head-question: What if `var` declaration is moved just before return? Will it still be a Closure?

Yes, absolutely! Let's see:

```javascript
function outer() {
  function inner() {
    // inner still knows about 'a' due to lexical scoping rules,
    // even though 'a' is declared later in the outer scope.
    console.log(a);
  }
  var a = 10; // Declaration moved down
  return inner;
}

var close = outer();
close(); // Output: 10
```

It still works and `inner` is still a closure. Why? Because JavaScript determines the scope of variables based on where functions are *lexically* (physically) written in the code, not based on the order of execution or declaration within that scope (thanks to hoisting for `var`). The `inner` function is defined within the scope of `outer`, so it has access to all variables declared within `outer`, regardless of whether they are declared before or after `inner` itself.

## :material-head-question: What if `var` is replaced by `let`? What will happen?

It still works perfectly fine, and `inner` remains a closure.

```javascript
function outer() {
  function inner() {
    console.log(a); // Still has access to 'a'
  }
  let a = 10; // Using 'let' instead of 'var'
  return inner;
}

var close = outer();
close(); // Output: 10
```

Using `let` (or `const`) doesn't change the fundamental concept of lexical scoping or closures. The `inner` function is still defined within the scope where `a` exists, so it closes over `a`. The main difference `let` introduces is block scoping and the temporal dead zone, but that doesn't prevent the closure from forming here.

## :material-head-question: What if `outer()` have a parameter? What happens then?

Parameters of a function are also part of its local scope. So, an inner function can close over its outer function's parameters just like it closes over its variables.

```javascript
function outer(b) { // 'b' is a parameter of outer
  function inner() {
    // inner can access both 'a' (variable) and 'b' (parameter)
    console.log(a, b);
  }
  let a = 10;
  return inner;
}

// Pass an argument when calling outer()
var close = outer("Hi Closures");
close(); // Output: 10 Hi Closures
```

It works exactly as you'd expect. The parameter `b` is part of `outer`'s environment, and `inner` forms a closure over that environment, remembering both `a` and `b`.

## :material-head-question: What if `outer()` function is nested into another function? Will `inner()` have access to the outermost function’s environment as well?

Yes! This demonstrates the **scope chain**. An inner function has access to its own scope, its parent's scope, its grandparent's scope, and so on, all the way up to the global scope.

```javascript
function outest() {
  var c = 12; // Variable in the outermost function

  function outer(b) { // Middle function with parameter 'b'
    function inner() {
      // inner can access 'a', 'b', and 'c'
      console.log(a, b, c);
    }
    let a = 10; // Variable in the middle function
    return inner;
  }
  return outer; // outest returns the outer function
}

// Call outest() to get the 'outer' function
// Then call the returned 'outer' function with an argument ("Hi Closures")
// This returns the 'inner' function
var close = outest()("Hi Closures");

// Finally, call the 'inner' function
close(); // Output: 10 Hi Closures 12
```

The `inner` function forms a closure that encompasses the scopes of *both* `outer` (including `a` and `b`) and `outest` (including `c`).

## :material-head-question: What if we have global variable with conflicted name?

JavaScript uses lexical scoping, meaning it looks for variables starting from the innermost scope and working outwards. The first scope where it finds the variable is the one it uses.

**Scenario 1: Local variable shadows global variable**

```javascript linenums="1" title="index.js"
function outest() {
  var c = 12;
  function outer(b) {
    function inner() {
      // Looks for 'a' in inner's scope (not found)
      // Looks for 'a' in outer's scope (found!) -> uses let a = 10
      console.log(a, b, c);
    }
    let a = 10; // This 'a' is local to 'outer'
    return inner;
  }
  return outer;
}

let a = 100; // Global variable 'a'

var close = outest()("Hi Closures");
close(); // Output: 10 Hi Closures 12
```

Here, `inner()` finds `a` in its parent `outer`'s scope (`let a = 10`). It uses that one and stops looking. The global `let a = 100` is ignored because the closer, local `a` "shadows" it.

**Scenario 2: Local variable commented out**

```javascript linenums="1" title="index.js"
function outest() {
  var c = 12;
  function outer(b) {
    function inner() {
      // Looks for 'a' in inner's scope (not found)
      // Looks for 'a' in outer's scope (not found)
      // Looks for 'a' in outest's scope (not found)
      // Looks for 'a' in global scope (found!) -> uses let a = 100
      console.log(a, b, c);
    }
    // let a = 10; // Commented out
    return inner;
  }
  return outer;
}

let a = 100; // Global variable 'a'

var close = outest()("Hi Closures");
close(); // Output: 100 Hi Closures 12
```

Now, since `inner` can't find `a` in `outer`'s scope, it continues searching outwards and finds the global `a = 100`.

**Scenario 3: Both local and global commented out**

```javascript linenums="1" title="index.js"
function outest() {
  var c = 12;
  function outer(b) {
    function inner() {
      // Looks for 'a' everywhere up to global scope (not found)
      console.log(a, b, c);
    }
    // let a = 10; // Commented out
    return inner;
  }
  return outer;
}

// let a = 100; // Commented out

var close = outest()("Hi Closures");
// close(); // This would cause an error!
// Output: ReferenceError: a is not defined
```

If `a` isn't found anywhere in the scope chain, trying to access it results in a `ReferenceError`.

## :material-head-question: What are the advantages of Closure?

Closures are powerful and enable several important patterns and techniques in JavaScript:

*   **Data Hiding and Encapsulation:** Creating private variables and functions that are protected from the outside world, but accessible to privileged functions. This is fundamental to the Module Pattern.
*   **Module Design Pattern:** Creating self-contained pieces of code with private state and public interfaces, avoiding global scope pollution.
*   **Currying:** Transforming a function that takes multiple arguments into a sequence of functions that each take a single argument.
*   **Function Factories:** Creating functions with specific configurations based on the outer function's arguments (like in the `outer(b)` example earlier).
*   **Maintaining State in Asynchronous Operations:** Callbacks (like those used in `setTimeout`, event listeners, or Promises) often rely on closures to access variables from the scope where they were created, even if the callback executes much later.
*   **Implementing Iterators:** Creating functions that remember their state between calls to generate sequences of values.
*   **Memoization:** Caching the results of expensive function calls and returning the cached result when the same inputs occur again. Closures can hold the cache.
*   **Creating Functions like `once`:** Designing functions that can only be executed a single time.

## :material-head-question: What are disadvantages of Closure?

While powerful, closures aren't without potential downsides:

*   **Memory Consumption:** Since closures retain references to their outer scope variables, those variables cannot be garbage collected as long as the closure exists. If many closures are created, or if closures hold references to large data structures that are no longer needed otherwise, it can lead to higher memory usage than expected.
*   **Memory Leaks (if not careful):** In older browsers or specific scenarios (like circular references involving DOM elements and JavaScript objects), closures could inadvertently prevent memory from being reclaimed, leading to memory leaks if the closures themselves were never released. Modern JavaScript engines and garbage collectors are much better at handling this, but it's still a theoretical possibility if closures are managed improperly.

## :simple-ccleaner: What is Garbage Collector?

A **Garbage Collector (GC)** is an automatic memory management feature found in many high-level programming languages like JavaScript. Its main job is to identify and free up memory that is no longer being used by the program.

*   **How it works (simplified):** The GC periodically looks at all the objects (variables, functions, etc.) the program has created. It figures out which objects are still reachable (can be accessed by the program, starting from the root, like the global object) and which are unreachable. Unreachable objects are considered "garbage" because the program can no longer use them. The GC then reclaims the memory occupied by this garbage, making it available for new objects.
*   **Contrast with manual management:** In languages like C or C++, the programmer is often responsible for explicitly allocating memory when needed and deallocating (freeing) it when it's no longer required. Forgetting to deallocate leads to memory leaks. JavaScript's GC automates this process, reducing the burden on the developer.

## :material-head-question: How Closures and Garbage Collector related to each other?

Closures directly influence how the Garbage Collector operates because they keep variables "alive".

Consider this example:

```javascript
function a() {
  var x = 10; // 'x' is local to function 'a'
  return function b() { // 'b' is a closure
    console.log(x); // 'b' references 'x' from its parent scope
  };
}

var y = a(); // 'y' now holds the function 'b'
// At this point, function 'a' has finished executing.

// Later...
// y(); // If we call y(), it needs access to 'x'
```

Here's the relationship:

1.  Normally, when function `a` finishes executing, its local variable `x` would become unreachable and eligible for garbage collection.
2.  However, function `a` *returned* function `b`.
3.  Function `b` is a closure and maintains a reference to its lexical scope, which includes the variable `x`.
4.  Because the function `b` (now referenced by the global variable `y`) *might* be called later, and it needs `x` to work correctly, the JavaScript engine ensures that `x` is *not* garbage collected. It's kept in memory specifically for the closure `b`.

So, closures prevent the GC from reclaiming the memory of the variables they close over, for as long as the closure itself is reachable. This is essential for closures to work but is also why they can increase memory usage (as mentioned in the disadvantages).

## :material-head-question: What is Smartly garbaged variable, examples?

Modern JavaScript engines often employ optimizations in their garbage collection related to closures. They can sometimes detect if a variable within the outer scope is *actually* used by the inner closure or not. If a variable is part of the outer scope but *not* referenced by the returned inner function(s), the engine might be smart enough to garbage collect it, even if other variables from the same scope are kept alive by the closure.

Example:

```javascript
function a() {
  var x = 10; // Referenced by the closure 'b'
  var z = 20; // NOT referenced by the closure 'b'

  return function b() {
    // This closure only uses 'x'
    console.log(x);
  };
}

var y = a(); // 'y' holds the closure 'b'
// y(); // Calling y would print 10
```

In this case:

*   The closure `b` (held by `y`) needs `x`, so `x` **cannot** be garbage collected.
*   The closure `b` **does not** need or reference `z` in any way.
*   A smart garbage collector can recognize that `z` is no longer reachable after function `a` finishes, even though `x` from the same scope is being kept alive. Therefore, `z` **can be** garbage collected ("smartly garbaged"), while `x` remains in memory for the closure.

This optimization helps mitigate the memory overhead of closures by only keeping the *necessary* parts of the outer scope alive.