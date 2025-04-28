---
icon: material/arrow-right
---

# `call`, `apply`, and `bind`

Have you ever needed to share a function between different objects, but found that `this` didn't refer to the object you expected? JavaScript provides powerful tools—`call`, `apply`, and `bind`—to explicitly control the `this` context when invoking functions. Let's see how you can use them to solve this common problem.

---

## :boxing_glove: The Problem: Reusing Methods Across Objects

Suppose you have an object method, but you don't want to copy or rewrite it for every object. Instead, you want to reuse a function and decide which object it should refer to when called.

```js
const name = {
    firstName: 'Rajan',
    lastName: 'Dangi'
};

const name2 = {
    firstName: 'Bhawana',
    lastName: 'Dangi'
};
```

You want to use a single `fullName` function for both objects.

---

## :person_in_lotus_position: The Solution: Using `call`, `apply`, and `bind`

### 1. `call()`

`call()` immediately invokes the function, letting you specify the `this` value and arguments one by one.

```js
const fullName = function() {
    console.log(this.firstName + ' ' + this.lastName);
};

fullName.call(name);   // Rajan Dangi
fullName.call(name2);  // Bhawana Dangi
```

**How it works:** The first argument is the object to use as `this`. Additional arguments are passed to the function.

---

### 2. `apply()`

`apply()` is almost identical to `call()`, but takes arguments as an array.

```js
const getHomeTown = function(hometown, state) {
    console.log(this.firstName + ' ' + this.lastName + ' is from ' + hometown + ', ' + state);
};

getHomeTown.apply(name, ['Kathmandu', 'Bagmati']);
// Rajan Dangi is from Kathmandu, Bagmati

getHomeTown.apply(name2, ['Kathmandu', 'Bagmati']);
// Bhawana Dangi is from Kathmandu, Bagmati
```

**Key difference:** Arguments after the context object are supplied as an array.

---

### 3. `bind()`

`bind()` doesn’t call the function immediately. Instead, it returns a new function with the chosen `this` context. You can then call this new function later.

```js
const fullNameBind = fullName.bind(name);
fullNameBind(); // Rajan Dangi

const fullNameBind2 = fullName.bind(name2);
fullNameBind2(); // Bhawana Dangi
```

**Use case:** Perfect when you need a reusable function with a preset context.

---

## Quick Comparison Table

| Method   | Call Immediately? | How to Pass Arguments         | Returns       |
|----------|------------------|------------------------------|---------------|
| `call`   | Yes              | Individually                 | Result value  |
| `apply`  | Yes              | As an array                  | Result value  |
| `bind`   | No               | Individually (when bound)    | New function  |

---

## Summary

- **`call()`** and **`apply()`** allow you to borrow a function and execute it in the context of another object, right away.
- **`bind()`** creates a new function with the desired context, which you can use later.
- Use `call` and `apply` based on whether you want to provide arguments as a list or array.

---

## Related Topics

:octicons-link-external-24: [Arrow Functions and `this`](this-keyword.md)