---
icon: material/arrow-right
---

# Diving into Node.js Modules

In this section, we'll explore how Node.js modules work behind the scenes. We'll examine how modules are loaded, how Node.js handles multiple modules, and take a deep dive into the `module.exports` and `require` functions.

## Module Privacy: The IIFE Wrapper

Node.js ensures that variables and functions defined within a module remain private by default. This is achieved using a technique similar to function scope in JavaScript, but implemented with a special wrapper called an **IIFE (Immediately Invoked Function Expression)**.

### Understanding Scope in JavaScript

First, let's recall how function scope works in plain JavaScript:

```javascript
function x () {
  const a = 10; // 'a' is local to function x
  function b () { // 'b' is local to function x
    console.log("b");
  }
  // 'a' and 'b' are only accessible inside function x
}

// console.log(a); // Error: a is not defined (outside x's scope)
// x(); // Calling x executes its code, but doesn't expose 'a' or 'b' globally
```

Variables and functions declared inside a function are generally not accessible from outside that function.

### Node.js Module Wrapping with IIFE

When you `require` a file in Node.js, the code within that file isn't executed directly in the global scope. Instead, [Node.js wraps it inside an IIFE like this](https://github.com/nodejs/node/blob/main/lib/internal/modules/cjs/loader.js#L350) before execution:

```javascript
(function (exports, require, module, __filename, __dirname) {
    // All the code from your module file (e.g., myModule.js) runs here.

    // Example:
    // const myVar = 'Hello';
    // function privateFunction() { /* ... */ }
    // module.exports.myVar = myVar; // Only explicitly exported things are public.

})(); // The final () immediately invokes the function
```

!!! question "What is an IIFE?"
    **What is an IIFE?** It's a function that is defined and executed immediately. This pattern is distinct from defining a function and calling it later:

```javascript title="Normal function definition and invocation"
function myFunction() { /* ... */ } // Definition
myFunction(); // Invocation
```

```javascript title="IIFE - Definition and invocation combined"
(function() {
    /* ... */
})();
```

### Why Use an IIFE for Modules?

1.  **Privacy:** It prevents variables, functions, and classes defined within the module from polluting the global scope or conflicting with identifiers in other modules. Everything inside is local unless explicitly attached to `module.exports` or `exports`.
2.  **Dedicated Scope:** It creates a unique execution environment for the module.
3.  **Module Globals:** Node.js uses the function parameters to inject useful, module-specific variables:
    *   `exports`: An object initially referencing `module.exports`. Often used as a shorthand.
    *   `require`: The function used to import other modules.
    *   `module`: An object containing metadata about the current module, including the crucial `exports` property.
    *   `__filename`: The absolute path to the current module file.
    *   `__dirname`: The absolute path to the directory containing the current module file.

!!! question "How are variables and functions kept private in different modules?"
    Primarily due to the IIFE wrapper. Each module's code runs within its own isolated function scope created by the IIFE.

!!! question "Where do `module` and `module.exports` come from?"
    They are provided by Node.js as parameters to the IIFE wrapper function when the module is loaded via `require`. Assigning to `module.exports` (or the `exports` object) defines what the module makes publicly available.

## How `require()` Works Behind the Scenes

The `require()` function is the mechanism for importing modules in Node.js (specifically for CommonJS modules). Here's a breakdown of its process when you call `require('some-module')`:

{==

1.  **Resolving:** Node.js determines the absolute path of `some-module`.
    *   It first checks if it's a **core module** (like `fs`, `http`, `path`).
    *   If not, it checks if it starts with `./`, `/`, or `../`, indicating a **local file or directory**. It searches for `.js`, `.json`, and `.node` files.
    *   If not, it looks for `some-module` within **`node_modules` directories**, starting from the current directory and moving up the parent directory chain.
2.  **Loading:** Node.js reads the content of the file found at the resolved path.
3.  **Wrapping:** The loaded file content (JavaScript code) is wrapped inside the IIFE discussed earlier, providing the module scope and injecting `exports`, `require`, `module`, `__filename`, `__dirname`.
4.  **Evaluating:** The wrapped code is executed by the V8 JavaScript engine. This is where the module defines its exports by assigning values to `module.exports` or `exports`.
5.  **Caching:** This is a critical optimization. The *first time* a module is required, its evaluated `module.exports` object is stored in a cache, keyed by its resolved filename. Subsequent `require()` calls for the *exact same file* will retrieve the cached `module.exports` directly, skipping steps 1-4 entirely.

==}

### Importance of Caching

Caching ensures that:

*   **Performance:** Module code is executed only once, even if required multiple times from different parts of an application. This avoids redundant file reading and execution overhead.
*   **Consistency:** All parts of the application requiring the same module receive the *exact same instance* (the same object in memory). This is important for modules that maintain state or represent singletons.

:man_mage: **Scenario:** Imagine `app.js`, `serviceA.js`, and `serviceB.js` all need a utility module `./utils.js`.

*   **First `require('./utils.js')` (e.g., in `app.js`):**

    Node.js performs resolving, loading, wrapping, and evaluation. The resulting `module.exports` from `utils.js` is cached.

*   **Subsequent `require('./utils.js')` (e.g., in `serviceA.js`):**

    Node.js finds the cached entry for the resolved path of `./utils.js` and immediately returns the *same* `module.exports` object created earlier. The `utils.js` code is *not* executed again.

## Exploring the Node.js Source Code

Node.js is open-source, and its codebase reveals how these mechanisms are implemented. You can explore it on GitHub: <https://github.com/nodejs/node>

*   **Core Dependencies:**

    *   **[V8 Engine:](https://github.com/nodejs/node/tree/main/deps/v8)** Integrates Google's V8 JavaScript engine for executing JS code.
    *   **[Libuv](https://github.com/nodejs/node/tree/main/deps/uv):** Provides the crucial asynchronous I/O capabilities (event loop, file system operations, networking, etc.), enabling Node.js's non-blocking nature. Much of Node's power comes from `libuv`.

*   **JavaScript Modules (`lib` directory):**

    The `lib` directory (<https://github.com/nodejs/node/tree/main/lib>) contains the JavaScript source code for Node.js's built-in modules (`fs`, `http`, `path`, `events`, `timers`, etc.). These modules often provide a JavaScript interface over underlying C++ functionality implemented using V8 and Libuv.

    Example: `lib/timers.js` contains logic related to `setTimeout`, `setInterval`.

*   **`require` Implementation:**

    The logic for the `require` function itself is implemented within internal modules:

    *   **`lib/internal/modules/cjs/loader.js`:** Contains the core logic for the CommonJS module loader, including resolution, loading, wrapping, evaluation, and caching (`Module._load`, `Module._resolveFilename`, `Module.wrap`, `Module._cache`, etc.).
    *   **`lib/internal/modules/helpers.js`:** Includes helper functions like `makeRequireFunction`, which creates the specific `require` function available inside each module.

*   **Error Handling:**

    The loader code in `cjs/loader.js` also handles errors, such as throwing a `TypeError` if you pass an invalid argument (like `undefined`) to `require()`.
