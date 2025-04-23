---
icon: material/arrow-right
---


# Execution Context in JavaScript

- Everything in JavaScript happens inside an execution context.
- Think of an execution context as a big box or container where the entire JavaScript code runs.
- The execution context has two main components:
    1. **Memory Component**
    2. **Code Component**

## 1. Memory Component
- This is where all variables and functions are stored as key-value pairs.
- The memory component is also called the **variable environment**.

## 2. Code Component
- This is where the code is executed, one line at a time.
- The code component is also known as the **thread of execution**.
- The whole code is executed one line at a time.

## Visual Representation: Execution Context

| Memory (Variable Environment)| Code (Thread of Execution)     |
|------------------------------|---------------------------------|
| key : value                  | ...                             |
| a : 10                       | ....                             |
| fn : { ... }                 | .....                          |

- **Memory**: Stores variables and functions as key-value pairs.
- **Code**: Executes code one line at a time.

*Memory is also called the Variable Environment. Code is also known as the Thread of Execution.*

!!! question "Is JavaScript Synchronous or Asynchronous?"
    JavaScript is a **synchronous, single-threaded language** by default.

    - It executes one command at a time, in a specific order.
    - Each line of code runs only after the previous line has finished.
    - This means JavaScript moves to the next line **only when the current line is done**.

!!! note
    While JavaScript itself is synchronous, it can handle asynchronous operations (like callbacks, promises, and async/await) using its event loop and browser APIs. These features allow JavaScript to perform non-blocking operations, but the core execution model remains single-threaded and synchronous.

