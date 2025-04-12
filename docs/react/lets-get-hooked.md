---
icon: material/arrow-right
---

# 05 - Let's Get Hooked

## Why Use React?

You might wonder why we don't just stick to HTML, CSS, and JavaScript for everything. While it's entirely possible to build applications using these technologies alone, we choose React because it enhances the developer experience, making development more seamless and efficient.

!!! info
    Learn more about the [React folder structure](https://legacy.reactjs.org/docs/faq-structure.html).

## Export and Import in React

### 1. Named Export/Import
- **Export:**
  ```javascript
  export const name = 'John';
  ```
- **Import:**
  ```javascript
  import { name } from './file.js';
  ```
- You can have multiple named exports in a single file.

### 2. Default Export/Import
- **Export:**
  ```javascript
  export default name;
  ```
- **Import:**
  ```javascript
  import name from './file.js';
  ```
- Only one default export is allowed per file.

### Can You Use Default Export with Named Exports?
Yes, you can combine default exports with named exports in the same file.

## React Hooks

Hooks are regular JavaScript functions provided by React that add powerful features to your components. When you install React via npm, you gain access to these hooks.

### Common Hooks

#### `useState()`
- **Usage:**
  ```javascript
  const [list, setList] = useState([]);
  ```
- **Description:**
  - Returns a state variable and a function to update it.
  - When the state updates, React re-renders the component.

#### `useEffect()`
- Used to perform side effects in functional components, such as data fetching or subscribing to events.

### Important Notes on State
- Updating a state variable triggers React's reconciliation cycle, which determines what changes need to be made to the UI.
- React keeps the data layer in sync with the UI layer using a reconciliation algorithm, ensuring efficient updates.

## Reconciliation and Rendering

Understanding the distinction between reconciliation and rendering is crucial for grasping how React optimizes updates.

### What is Reconciliation?

Reconciliation is the process React uses to determine what has changed in the virtual DOM compared to the previous version. Here's a breakdown:

1. **Virtual DOM Creation:** When you render a React application, React creates a virtual DOM tree that represents the UI.
2. **Update Trigger:** When there's a change in state or props, React generates a new virtual DOM tree.
3. **Diffing (Reconciliation):** React compares the new virtual DOM with the previous virtual DOM to identify what has changed.
4. **Calculating Minimum Updates:** Based on the differences, React calculates the minimal set of changes needed to update the real DOM.

**Key Points:**
- **Efficiency:** Reconciliation ensures that only the necessary parts of the DOM are updated, enhancing performance.
- **Stability with Keys:** When dealing with lists, using stable and unique keys helps React efficiently manage and update elements.

### What is Rendering?

Rendering is the phase where React updates the real DOM based on the results of the reconciliation process.

1. **Applying Changes:** After reconciliation, React applies the calculated changes to the real DOM.
2. **Updating the UI:** This ensures the user interface reflects the current state of the application.

**Key Points:**
- **Separation of Concerns:** Reconciliation handles the "what" needs to change, while rendering handles the "how" to make those changes happen in the UI.
- **Performance Optimization:** By separating these phases, React can optimize rendering performance, ensuring smooth and efficient updates.

## The Diff Algorithm

The diff algorithm is a crucial part of the reconciliation process. It identifies the differences between the updated virtual DOM and the previous virtual DOM, allowing React to update only what has changed.

**Benefits:**

- **Optimized Rendering:** By updating only the necessary parts of the DOM, React minimizes performance overhead.
- **Predictable Updates:** The diffing process ensures that changes are applied consistently and accurately.

## Why is React Fast?

React's speed is largely attributed to **React Fiber**, a reimplementation of React's core algorithm. Fiber introduces several enhancements that optimize React's performance and responsiveness. It's released as part of React 16.

### Key Features of React Fiber

- **Incremental Rendering:** Splits rendering work into smaller chunks that can be spread over multiple frames. This ensures that high-priority updates, like user interactions, are handled promptly without being blocked by less critical tasks.
- **Optimized for Animation, Layout, and Gestures:** Enhances performance in dynamic interfaces by efficiently managing complex updates.
- **Concurrency Primitives:** Introduces new tools that allow React to handle multiple tasks simultaneously, improving the overall user experience.

### How React Fiber Enhances Performance

1. **Scheduling Work:** Fiber allows React to prioritize work based on its importance, ensuring that critical updates are processed first.
2. **Pausing and Resuming:** Fiber can pause ongoing work to handle higher priority tasks and then resume the paused work later.
3. **Resource Management:** By breaking down tasks, Fiber ensures that the application remains responsive, even under heavy load.

## Integrating with Other Libraries

React works well with libraries like [GSAP (GreenSock Animation Platform)](https://greensock.com/gsap/) and [Three.js](https://threejs.org/) for creating animations and 3D designs. These integrations leverage React's efficient rendering and state management to produce smooth and interactive user experiences.

## How It Works Behind the Scenes

To understand React's efficiency, it's essential to grasp the interplay between the virtual DOM, reconciliation, rendering, and React Fiber.

1. **Virtual DOM:** React creates virtual DOM objects that replicate real DOM elements. This lightweight representation allows React to manage UI updates efficiently.
2. **Reconciliation:** When changes occur, React uses the diff algorithm to compare the new virtual DOM with the previous one, determining the minimal set of changes required.
3. **Rendering:** Based on the reconciliation results, React updates only the necessary parts of the real DOM, ensuring optimal performance.
4. **React Fiber:** Fiber enhances this entire process by introducing incremental rendering and improved scheduling, ensuring that updates are handled smoothly without compromising the application's responsiveness.

**Flow Summary:**

- **State Change:** Triggers an update in the virtual DOM.
- **Reconciliation:** Compares new and old virtual DOMs to find differences.
- **Rendering:** Updates the real DOM based on these differences.
- **Fiber's Role:** Manages the scheduling and execution of these tasks to optimize performance.


!!! info
    Learn more about the [React Fiber Architecture](https://github.com/acdlite/react-fiber-architecture).