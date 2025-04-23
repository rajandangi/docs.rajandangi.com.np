---
icon: material/arrow-right
---

# JavaScript: Loosely Typed Language

JavaScript is known as a **loosely typed** (or **weakly typed**) language. This means that variables in JavaScript are **not bound to any specific data type**. You can assign any type of value to a variable, and you can even change its type at any time during the program.

---

## :material-lock-open: Loosely Typed Explained

In strictly typed languages (like C or C++), you must declare the type of a variable, and it can only store values of that type:

```c
// C language example
int a;
a = 10;        // ✔ Valid
a = "Hello";   // ✖ Error: Cannot assign string to int
```

In **JavaScript**, variables are flexible:

```javascript
var a;
a = 10;           // a is now a number
a = "Hello";      // a is now a string
a = true;         // a is now a boolean
```

You can reassign different types of values to the same variable without any errors!

---

## :octicons-light-bulb-16: Example: Changing Types in JavaScript

Here's a step-by-step example:

```javascript
var a;
console.log("Step 1, a =", a); // undefined

a = 10;
console.log("Step 2, a =", a); // number

a = "Hello World";
console.log("Step 3, a =", a); // string
```

```console title="Console Output"
Step 1, a =  undefined
Step 2, a =  10
Step 3, a =  Hello World
```

---


!!! abstract "Key Takeaway"
    - **Loosely typed** means you don't need to declare variable types.
    - Variables can change their type at runtime.
    - This provides flexibility, but you need to be careful to avoid type-related bugs