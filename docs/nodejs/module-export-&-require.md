---
icon: material/arrow-right
---

# Module Export & Require

Splitting code into multiple files keeps a Node.js project organized and maintainable. In this chapter, we'll explore how to share functionality between files using CommonJS (CJS) and ES Modules (ESM).

---

## CommonJS Modules (CJS)

### :no_bell: Problem
You have two files : `app.js` and `xyz.js`— and want them to interact. How can `app.js` execute code or use functions from `xyz.js`?

### :material-lightbulb-on: Solution
Use `require()` to load one module into another and `module.exports` to expose functionality.

---

### 1. Requiring a Module

```javascript title="app.js"
const xyzModule = require('./xyz'); // Load xyz.js
console.log("Running app.js");
```

```javascript title="xyz.js"
console.log("xyz.js module is being loaded");
// By default, nothing is exported here.
```

Running `node app.js` logs:
```
xyz.js module is being loaded
Running app.js
```

---

### 2. Exporting a Single Function

```javascript title="sum.js"
function calculateSum(a, b) {
  return a + b;
}
module.exports = calculateSum;
```

```javascript title="app.js"
const sum = require('./sum'); // Load the sum module
console.log("Sum:", sum(5, 3)); // Sum: 8
```

---

### 3. Exporting Multiple Items

```javascript title="sum.js"
const x = "Exported value";
function calculateSum(a, b) { return a + b; }
module.exports = { x, calculateSum };
```

```javascript title="app.js"
const { x, calculateSum } = require('./sum');
console.log(x);                  // Exported value
console.log(calculateSum(10, 5)); // 15
```

---

### 4. Destructuring Imports (Common Pattern)

```javascript title="app.js"
const { x, calculateSum } = require('./sum');
```

This cleanly pulls out only what you need.

---

### 5. Folder Modules (index.js)

Structure:
```
project/
├─ app.js
└─ utils/
   ├─ index.js
   ├─ sum.js
   └─ multiply.js
```

```javascript title="utils/sum.js"
function sum(a, b) {
  return a + b;
}
module.exports = sum;
```

```javascript title="utils/multiply.js"
module.exports = (a, b) => a * b;
```

```javascript title="utils/index.js"
const sum = require('./sum');
const multiply = require('./multiply');
module.exports = { sum, multiply };
```

```javascript title="app.js"
const { sum, multiply } = require('./utils');
console.log(sum(2, 3));      // 5
console.log(multiply(2, 3)); // 6
```

---

### 6. Requiring JSON & Built‑in Modules

```json title="data.json"
{
  "name": "MyApp",
  "version": "1.0.0"
}
```

```javascript title="app.js"
const config = require('./data.json');
console.log(config.name); // MyApp

const fs   = require('fs');
const path = require('path');
```

---

## ES Modules (ESM)

ESM is the modern JavaScript module system. To enable it in Node.js, add `"type": "module"` to your `package.json` or use the `.mjs` extension.

!!! note "ESM Module"
    - Newer version of Node.

```json title="package.json"
{
  "name": "my-app",
  "version": "1.0.0",
  "type": "module"
}
```

### 1. Exporting

```javascript title="mathUtils.js"
export const PI = 3.14159;
export function add(a, b) { return a + b; }
export default 2.718;
```

### 2. Importing

```javascript title="main.js"
import E, { PI, add } from './mathUtils.js';
console.log(PI);     // 3.14159
console.log(add(2,3)); // 5
console.log(E);      // 2.718
```

---

### Key Differences: CJS vs. ESM

| Feature                | CommonJS (CJS)                   | ES Modules (ESM)               |
|------------------------|----------------------------------|--------------------------------|
| Loading                | Synchronous: `require()` blocks until the module is loaded | Asynchronous: `import` can load in parallel without blocking |
| Strict Mode            | Non-strict by default            | Always strict—enforces better parsing and error handling |

{==

Overall, ES Modules offer non‑blocking loading and built‑in strict mode, making your code more efficient and reliable.

==}

---

## Additional Patterns

- **Alternative CJS Exports**
  ```javascript
  module.exports.x = x;
  module.exports.calculateSum = calculateSum;
  ```
- **Import All**
  ```javascript
  import * as utils from './utils.js';
  ```
- **Default-only Export**
  ```javascript
  export default function() { /*…*/ }
  ```

---

## Recap

- CommonJS uses `require()` and `module.exports`.
- ES Modules use `import` and `export`.
- Organize with folders, `index.js`, or JSON imports.
- Choose CJS for legacy, ESM for modern codebases.
