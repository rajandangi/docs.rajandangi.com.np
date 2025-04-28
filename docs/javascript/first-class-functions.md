---
icon: material/arrow-right
---

# First-Class Functions

Functions are fundamental building blocks in JavaScript. But have you ever wondered why sometimes you can call a function *before* you define it, and other times you can't? This difference often boils down to how the function is created. Let's dive in!

## :thinking: Statement vs. Expression: The Hoisting Puzzle

One of the most crucial differences between the two main ways of creating functions lies in [hoisting](hoisting.md). Hoisting is JavaScript's behavior of moving declarations (like function declarations and `var` variables) to the top of their scope *before* code execution.

!!! tip "The difference between function statement and function expression is hoisting."

Consider this:

```javascript linenums="1" title="index.js"

greet(); // ✅ This works! Why? Because function *statements* are fully hoisted.

// greetMe(); // ❌ TypeError: greetMe is not a function. Why? Function *expressions* are not fully hoisted like statements.

console.log('--------Function Statement aka function Declaration--------');
// Definition below

console.log('--------Function Expression--------');
// Definition below
```

Let's break down each type.

### Function Statement (aka Function Declaration)

This is the classic way to define a function.

```javascript linenums="1" title="index.js"
function greet() {
    console.log('This way of defining function is called Function Statement');
}

// You can call it before or after its definition in the code (due to hoisting)
greet();
```

Key characteristics:

*   Starts with the `function` keyword.
*   Requires a function name (`greet` in this case).
*   The entire function definition is hoisted, making it available throughout its scope even before the line it's defined on.

### Function Expression

Here, a function is created and assigned to a variable. The function itself often doesn't have a name (making it anonymous), although it can ([see Named Function Expression](#named-function-expression)).

```javascript linenums="1" title="index.js"
var greetMe = function () {
    console.log('Function acts like a value and can be assigned to a variable');
    console.log('This way of defining function is called Function Expression');
}

// You can only call it *after* the assignment
greetMe();
```

Key characteristics:

*   The function is treated like a value.
*   It's assigned to a variable (`greetMe`).
*   If using `var`, the variable declaration (`var greetMe`) is hoisted, but the assignment (`= function() {...}`) is not. This is why you get a `TypeError` if you call it before the assignment line – `greetMe` exists but holds `undefined` initially, not the function.

---

## Anonymous Function

An anonymous function is simply a function without a name.

```javascript linenums="1" title="index.js"
// Standalone anonymous function - This causes an error!
/*
function () {
    console.log('Function without a name is called Anonymous Function');
    // Uncaught SyntaxError: Function statements require a function name
}
*/

// Use Case: Assigning to a variable (making it a Function Expression)
var sayHi = function () {
    console.log('Anonymous function is assigned to a variable');
};
sayHi();

// Another Use Case: Passing as an argument (see First-Class Functions)
setTimeout(function() {
    console.log("This anonymous function runs after 1 second");
}, 1000);
```

!!! question "Why the error?"

{==

Function *statements* require a name. Anonymous functions are typically used where functions are treated as values, like in function expressions or when passed as arguments.

==}

## Named Function Expression

This is similar to a function expression, but the function being assigned *does* have a name.

```javascript linenums="1" title="index.js"
var greetYou = function innerGreet() {
    console.log('Function with a name assigned to a variable is a Named Function Expression');
    // The name 'innerGreet' is primarily useful for debugging (stack traces)
    // and recursion within the function itself.
};

greetYou();
```

!!! warning "Corner Case Gotcha"

```javascript linenums="1" title="index.js"
var b = function xyz() {
    console.log('Trying to access xyz from outside...');
    // console.log(xyz); // This would work inside the function for recursion
};

// b(); // This works

// xyz(); // ❌ Uncaught ReferenceError: xyz is not defined
```

!!! tip "Important"
    The name in a named function expression (`innerGreet`, `xyz`) is typically only available *within the function's own scope*. It's not created in the outer (global or enclosing) scope. You still call the function using the variable it was assigned to (`greetYou`, `b`).

## Parameters vs. Arguments

These terms are often confused, but they have distinct meanings:

*   **Parameters** are the names listed in the function definition. They act as placeholders or local variables within the function.
*   **Arguments** are the actual values passed *to* the function when it is called (invoked).

```javascript linenums="1" title="index.js"
// 'param1' and 'param2' are parameters
function greetWithParameters(param1, param2) {
    console.log('Parameter 1 (param1): ' + param1);
    console.log('Parameter 2 (param2): ' + param2);
}

// 'Hello' and 'World' are arguments
greetWithParameters('Hello', 'World');
```

## ✨ First-Class Functions

In JavaScript, functions are =="first-class citizens"==. This fancy term means you can treat functions just like any other value (like numbers, strings, or objects). This ability includes:

1.  **Assigning functions to variables** (as seen in Function Expressions).
2.  **Passing functions as arguments** to other functions.
3.  **Returning functions** from other functions.

This is a powerful concept that enables patterns like callbacks and higher-order functions.

```javascript linenums="1" title="index.js"

// 1. Passing a function as an argument
function logGreeting(fn) { // 'fn' is a parameter expected to be a function
    console.log("Executing the function passed as an argument:");
    fn(); // Calling the passed-in function
}

// Passing an anonymous function expression
logGreeting(function () {
    console.log('Hello World (from anonymous function argument)');
});

// Passing a named function
function sayGoodbye() {
    console.log('Goodbye! (from named function argument)');
}
logGreeting(sayGoodbye);


// 2. Returning a function from another function
function createGreeter() {
    // This function creates and returns *another* function
    return function() {
        console.log('This function was returned from createGreeter!');
    };
}

var generatedGreeter = createGreeter(); // generatedGreeter now holds the returned function
generatedGreeter(); // Execute the returned function

// You can also call the returned function immediately
console.log("Calling the returned function immediately:");
createGreeter()(); // Calls createGreeter, then calls the function it returns
```

## ➡️ Arrow Functions (ES6)

Introduced in ES6 (ECMAScript 2015), arrow functions provide a more concise syntax for writing function expressions. They also have some differences in how they handle the `this` keyword (which is a topic for another discussion).

```javascript linenums="1" title="index.js"
// Traditional function expression
var add = function(a, b) {
  return a + b;
};

// Equivalent arrow function
var addArrow = (a, b) => {
  return a + b;
};

// Even shorter for single return expressions
var addArrowConcise = (a, b) => a + b;

console.log('--------Arrow Functions--------');
console.log("Traditional:", add(2, 3));
console.log("Arrow:", addArrow(2, 3));
console.log("Concise Arrow:", addArrowConcise(2, 3));

var greetArrow = () => {
    console.log('Arrow functions offer a shorter syntax!');
}
greetArrow();
```

Arrow functions are very common in modern JavaScript due to their brevity.