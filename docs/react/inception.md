---
icon: material/arrow-right
---
# 01 - React Inception

## What is a CDN ?
- A Content Delivery Network or Content Distribution Network
(CDN) is a geographically distributed network of proxy servers
and their data centers.
- The goal is to provide high availability and performance by
distributing the service spatially relative to end users.
- CDNs have grown to serve a large portion of the internet content
today, including web objects (text, graphics & scripts),
downloadable objects (media files, software, documents),
applications (e-commerce, portals), live streaming media,
on-demand streaming media, and social media sites.
- CDNs are a layer in the internet ecosystem. Content owners such
as media companies and ecommerce vendors pay CDN operators to
pay their content to their end users.
- [We can add React into our project by injecting CDN links in it (in an .html file).](https://legacy.reactjs.org/docs/cdn-links.html)

```
<script crossorigin src="https://unpkg.com/react@18/umd/react.development.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
```

## Why do we use CDN ?
- Improved scalability and connectivity.
- In addition to facilitating end-users with faster load times, which translates into greater user -experience, a content delivery network also rewards web publishers with increased traffic, higher page views, etc.
- Decreased bandwidth consumption.
- Lower latency.
- Latency is the lag between request and response.
- Effective traffic spike management.
- Enhanced cyber security.
- A CDN employs automation and data analytics tools that help identify firewall issues, Man in the middle threat, Distributed Denial of Service attacks.

## What is crossorigin and why do we use it in React CDN ?
- CORS or cross-origin resource sharing is a mechanism that allows memory resources (e.g., fonts, JavaScript, etc) on a webpage to be requested from another domain outside the domain from which the resource originated.


## Difference between React and React-DOM

| React                 | React-DOM                   |
| :-----------------    | :-------------------------- |
| A JavaScript library for building user interfaces | A package that connects React to the browser's DOM  |
| Contains core functionality and component logic | Handles rendering React components in web browsers |


!!! tip
    React defines *what* to render, while React-DOM handles *how* to render it in the browser.

## First program in React
```jsx
const heading = React.createElement('div', { id: "parent" }, "Hello from React");

const root= React.createRoot(document.getElementById('root'));
root.render(heading)
```

- The costliest operation for a browser is when the browser needs to manipulate the DOM.
- Any React element is nothing but a **JavaScript object.**
- This object contains a key known as props which stores the children and other attributes of the React element in a key-value pair.
- The `render()` function is responsible for taking the JavaScript object (React element) as an argument, converting it into an HTML tag and putting it in the DOM.

## Create nested elements (with sibling elements)
```jsx
const heading = React.createElement('div', { id: "parent" },
    React.createElement("div", { id: "child" },
        [
            React.createElement("h1", {}, "Hello from React"),
            React.createElement("h2", {}, "This is h2 title")
        ]
    )
);

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(heading);
```

## What if there is already an HTML element inside the `<div>` which is rendered by React using `render()` function?

```html
<!-- index.html -->
<div id="root">
    <h1>Hello World</h1>
</div>
```

```javascript
// App.js
const rootElement = ReactDOM.createRoot(document.getElementById('root'));
rootElement.render(<App />);
```
- When we have both HTML inside `<div>` and React trying to render into that same `<div>`. First, the HTML content loads and appears on the page.
- Then, when React loads, this javascript code tells React "replace whatever is in that div with our React app (`<App />`)."

So while your React code is loading, users see the HTML content. Once React is ready, it swaps out the HTML content with your React app.

It's like having a "Loading..." message that gets replaced by your actual app once it's ready!


## What is the difference between a framework and a library?

**1. Library**
  - A library provides a set of helper functions/objects/modules which your application code calls for specific functionality.
  - Libraries typically focus on a narrow scope (e.g., strings, IO, sockets), so their APIs tend to be smaller and require fewer dependencies.

**2. Framework**
  - A framework, on the other hand, has defined open or unimplemented functions or objects which the user writes to create custom applications.
  - Because a framework is itself an application, it has a wider scope and includes almost everything necessary to make a user application according to specific needs.

**Key Differences:**
- When you use a library, you are in control of the application's flow.
- When you use a framework, the framework controls the application's flow.
- The framework dictates the architecture and how the application is structured, and you fill in the details within that structure.
- For example, a web framework will often handle routing, middleware, and request processing, and you define the specific actions for your application within that structure.
- In a library, you decide when and where to call library functions.
- For example, you might use a library to perform specific tasks like handling HTTP requests, manipulating data, or creating UI components.

## Why is React named "React"?

React got its name because of its core feature: it **reacts** to changes in data.

Key points that justify its name:
- It **reacts** to state changes automatically
- It **reacts** to user interactions promptly
- It **reacts** and updates only the necessary components in the DOM

As stated by Jordan Walke (React's creator):
> *"The name 'React' was chosen because the library was built to help developers build user interfaces that can quickly react to changes in data."*

This reactive nature makes it efficient because:
1. It updates quickly when data changes
2. It only re-renders components that need to change
3. It responds immediately to user actions

## Real DOM vs Virtual DOM

#### Real DOM
- The actual HTML structure of a webpage
- Direct representation of the web page in the browser
- Changes are immediately reflected on the webpage
- Updating is slower as it refreshes the entire DOM

#### Virtual DOM
- A lightweight copy of the Real DOM in memory
- Acts as a blueprint of the actual webpage
- Changes happen in two steps:
  1. First updates the Virtual DOM
  2. Then syncs changes with Real DOM
- More efficient because:
  - Only updates what's necessary
  - Reduces unnecessary re-rendering
  - Batches multiple changes together

#### Key Benefit
The Virtual DOM improves performance by minimizing direct manipulation of the Real DOM, making React applications faster and more efficient.

## When does React sync Virtual DOM with Real DOM?

React follows a process called **reconciliation** to sync changes, which happens in the following steps:

1. **Trigger**: When state or props change

2. **Process**:
   - Creates new Virtual DOM
   - Compares with previous Virtual DOM (Diffing)
   - Identifies necessary changes

3. **Batch Updates**:
   - Collects multiple changes
   - Groups them together
   - Updates Real DOM efficiently

4. **Timing**:
   - After user interactions (clicks, typing)
   - When state updates complete
   - During asynchronous operations

This process ensures efficient updates by minimizing direct manipulation of the Real DOM.

