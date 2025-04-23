---
icon: material/arrow-right
---

# `undefined` vs `not defined` in JavaScript

JavaScript handles variables in a unique way compared to many other programming languages, especially regarding the concepts of `undefined` and `not defined`.

## :grey_question: What is `undefined`?

In JavaScript, `undefined` is a special keyword and primitive value. It is automatically assigned to variables that have been declared but not yet given a value.

### :material-google-lens: Why does this happen?

- When JavaScript code runs, it first creates a *global execution context*.
- During this process, JavaScript allocates memory for all variables and functions before any code is executed (this phase is called **hoisting**).
- For variables, JavaScript sets their initial value to `undefined` as a placeholder until they are assigned a real value.


```javascript title="Example: Hoisting and undefined"
console.log("a =", a); // Output: a = undefined
var a = 10;
console.log("a =", a); // Output: a = 10
```

!!! tip "Explanation"
    - `a` is declared with `var`. Before the value `10` is assigned, `a` exists in memory with the value `undefined`.
    - After the assignment, `a` holds the value `10`.


---

## :warning: What is `not defined`?

A variable is `not defined` when you try to access a variable that has never been declared in your code.

```javascript title=" Example: not defined "
console.log(x); // ReferenceError: x is not defined
```

!!! tip "Explanation"
    `x` has not been declared anywhere in the code. Trying to access it causes a `ReferenceError`.
---

## :vs: Key Differences

|               | `undefined`                     | `not defined`                                     |
|---------------|---------------------------------|---------------------------------------------------|
| **When?**     | Variable declared but unassigned | Variable never declared                           |
| **Error?**    | No error; value is `undefined`   | ReferenceError when accessed                      |
| **Example**   | `var a; console.log(a); // undefined` | `console.log(x); // ReferenceError: x is not defined` |

---

## :warning: Common Mistake: Assigning `undefined`

It's possible to assign `undefined` to a variable:

```javascript
var b = undefined; // Technically allowed, but discouraged
```

However, **this is generally considered bad practice** because:

- `undefined` is meant as a placeholder before a variable is assigned a value.
- Assigning `undefined` directly might make your code harder to understand and can lead to bugs or confusion.

---

## :material-filter: Quick FAQs

<div class="grid cards" markdown>

-   __Is `undefined` the same as "empty"?__

    ---

     No. An `undefined` variable has memory reserved for it; it's not the same as empty or non-existent.

-   __What if I declare a variable but never assign a value?__

    ---

     It will hold the value `undefined` for its entire lifetime.

-   __Should I use `undefined` as a value on purpose?__

    ---

    Best practice is to let JavaScript use `undefined` naturally rather than assigning it yourself.

</div>

---

!!! abstract "Key Takeaway"
    - `undefined`: Variable declared, memory reserved, but not yet assigned a value.
    - `not defined`: Variable does not exist in the current scope; accessing it throws a `ReferenceError`.
    - Avoid directly assigning `undefined` to variables.