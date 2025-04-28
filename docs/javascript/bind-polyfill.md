---
icon: material/arrow-right
---

# Polyfill for `bind()`

Sometimes, you want to use modern JavaScript features, but not all environments support them. For example, some older browsers might not have the `bind()` method. So how do you ensure your functions still work as expected?

---

## Prerequisite

:octicons-link-external-24: [call, apply, and bind method in JavaScript](call-apply-bind.md)

---

## :bulb: The Solution: Introducing Polyfills

**Polyfill:** A piece of code that adds missing functionality by emulating features that don't exist in certain JavaScript environments.

By writing a polyfill, you can make sure your code works everywhere—even where built-in functionality is missing.

---

##  Polyfilling `bind()`

Let’s see how the built-in `bind()` works first:

```js
const name = {
    firstName: 'John',
    lastName: 'Doe'
};

function printName(homeTown, state) {
    console.log(this.firstName + ' ' + this.lastName + ' from ' + homeTown + ', ' + state);
}

// Built-in bind(): sets 'this' to 'name' and pre-fills 'homeTown'
const printMyName = printName.bind(name, 'Salyan');
printMyName('Karnali');
// Output: John Doe from Salyan, Karnali
```

The built-in `bind()` lets you fix `this` and optionally preset some arguments.

---

## Writing a Polyfill: `myBind`

Suppose the built-in `bind()` is missing. Let's create a polyfill called `myBind`:

```js
Function.prototype.myBind = function (...args) {
    const originalFunction = this; // The function on which myBind is called
    const context = args[0]; // The object to bind as 'this'
    const bindArgs = args.slice(1); // Any initial arguments to preset

    return function (...callArgs) {
        // Combine the preset arguments and those provided at call time
        return originalFunction.apply(context, [...bindArgs, ...callArgs]);
    };
};
```

**How it works:**

- `...args` captures all arguments passed to `myBind`.
- The first argument (`args[0]`) is the intended `this` context.
- The rest (`args.slice(1)`) are preset arguments.
- When calling the returned function, both preset and later arguments are combined and passed to the original function using `apply`.

---

## Example Usage

```js
const printMyName2 = printName.myBind(name, 'Salyan');
printMyName2('Karnali');
// Output: John Doe from Salyan, Karnali
```

Just like the built-in `bind()`, the polyfill creates a new function with the desired `this` context and preset arguments.