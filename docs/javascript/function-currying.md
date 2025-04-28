---
icon: material/arrow-right
---

# Function Currying

Ever needed to create specialized versions of a function without rewriting it? Function currying offers an elegant solution!

Currying is a functional programming technique where a function that takes multiple arguments is transformed into a sequence of functions, each taking only a single argument. Instead of accepting all arguments at once, the curried function takes the first argument and returns a *new* function. This new function then takes the second argument and returns another function, and so on, until all arguments have been provided. The final function in the chain then returns the result.


---

## Prerequisite

:octicons-link-external-24: [call, apply, and bind method in JavaScript](call-apply-bind.md)

:octicons-link-external-24: [Closures in JavaScript](closures.md)

---

## :thinking: Why Use Currying?

*   **Creating Specialized Functions:** Easily create new functions with some arguments pre-set.
*   **Improving Reusability:** Break down complex functions into smaller, reusable pieces.
*   **Enhancing Readability:** Can sometimes make function composition clearer.

## How to Achieve Currying in JavaScript

There are two primary ways to implement function currying in JavaScript:

### 1. Using the `bind` Method

The `bind()` method creates a new function that, when called, has its `this` keyword set to the provided value, with a given sequence of arguments preceding any provided when the new function is called. We can leverage this to "pre-fill" arguments.

!!! example "Example of a `multiply` function that multiplies two numbers using `bind` for currying"

```javascript
let multiply = function (x, y) {
    console.log(x * y);
};
```

Now, let's use `bind` to create specialized versions:

```javascript
// Create a function that always multiplies by 2
let multiplyByTwo = multiply.bind(this, 2);
multiplyByTwo(5); // Output: 10 (x is bound to 2, y is 5)

// Create a function that always multiplies by 3
let multiplyByThree = multiply.bind(this, 3);
multiplyByThree(5); // Output: 15 (x is bound to 3, y is 5)

// Bind only 'this', arguments are provided later
let multiplyByFour = multiply.bind(this);
multiplyByFour(4, 5); // Output: 20 (x is 4, y is 5)
```

In the examples `multiplyByTwo` and `multiplyByThree`, `bind` creates new functions where the first argument (`x`) is fixed (or *bound*) to `2` and `3` respectively. The new functions only need the second argument (`y`) to complete the calculation. In `multiplyByFour`, no arguments are bound initially, so it behaves like the original `multiply` function.

### 2. Using Closures

Closures provide a natural way to achieve currying. A closure occurs when a function remembers its lexical scope (the environment in which it was created), even when the function is executed outside that scope. We can use nested functions to create this effect.

!!! example "Example of a `sum` function that adds two numbers using closures for currying"

```javascript
let sum = function (a) {
    // This inner function forms a closure, remembering 'a'
    return function (b) {
        console.log(a + b);
    };
}

// Create a specialized function 'addTwo' where 'a' is fixed to 2
let addTwo = sum(2);

// Call the inner function with 'b' = 3
addTwo(3); // Output: 5 (a was 2, b is 3)
```

Here's how it works:

1.  Calling `sum(2)` executes the outer function with `a = 2`.
2.  It returns the *inner* function `function (b) { console.log(a + b); }`.
3.  Crucially, this inner function *remembers* that `a` was `2` because of the closure.
4.  When we call `addTwo(3)`, we are executing the inner function, providing `b = 3`. It accesses the remembered `a = 2` and calculates `2 + 3`.

{==

Both `bind` and closures are effective ways to implement function currying in JavaScript, allowing you to create more flexible and reusable functions.

==}