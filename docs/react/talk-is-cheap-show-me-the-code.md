---
icon: material/arrow-right
---

# 04 - Talk is cheap, show me the code

## Props in React
- Props (short for Properties) are a way to pass data from parent to child components in React
- Props are read-only and cannot be modified by a child component
- They help make components reusable and dynamic

> Passing a prop to a function is like passing an argument to a function.

### Key Features of Props
- Props flow in one direction (parent to child)
- Props can carry any type of data:
  - Strings
  - Numbers
  - Objects
  - Arrays
  - Functions
  - JSX elements

### Example of Props Usage
```jsx
// Parent Component
function ParentComponent() {
  return (
    <ChildComponent
      text="Hello World"
      number={42}
      isActive={true}
    />
  );
}

// Child Component
function ChildComponent(props) {
  return (
    <div>
      <p>{props.text}</p>
      <p>{props.number}</p>
      <p>{props.isActive ? 'Active' : 'Inactive'}</p>
    </div>
  );
}
```

### Props Destructuring
```jsx
// Instead of using props.propertyName, you can destructure:
function ChildComponent({ text, number, isActive }) {
  return (
    <div>
      <p>{text}</p>
      <p>{number}</p>
      <p>{isActive ? 'Active' : 'Inactive'}</p>
    </div>
  );
}
```

### Default Props
- You can set default values for props in case they're not provided:
```jsx
function ChildComponent({ text = "Default Text" }) {
  return <p>{text}</p>;
}
```

## Config Driven UI
- Config driven UI is a technique that allows you to create user interfaces based on a configuration file (such as JSON or TypeScript) that defines the layout and content of UI components.
- This can be useful for creating dynamic and customizable UIs without hard coding them.

## Keys in React
### Why and When Do We Need Keys?
When we have components at same level and if a new component comes on the first without ID, DOM is going to re-render all the components again. As DOM can’t identify where to place it.

But if we give each of them a unique ID then react knows where to put that component according to the ID. It is a good
optimization and performance thing.

```jsx
const Body = () => (
  <div>
    {resList.map((restaurant) => (
      <RestaurantCard
        key={restaurant.id}
        resData={restaurant}
      />
    ))}
  </div>
);
```

!!! danger "Using Indexes as Keys"
    - It is not recommended to use indexes as keys if the order of items may change.
    - Using indexes as keys can negatively impact performance and cause issues with component state.
    - If no explicit key is assigned to list items, React defaults to using indexes as keys.
    - [Index as a key is an anti-pattern](https://robinpokorny.com/blog/index-as-a-key-is-an-anti-pattern/). Avoid using indexes as keys for list items.