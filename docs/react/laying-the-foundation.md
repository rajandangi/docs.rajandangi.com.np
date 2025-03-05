---
icon: material/arrow-right
---

# 03 - Laying the foundation

## React Element Creation: Two Approaches
A comparison of creating React elements using the traditional `React.createElement` method versus JSX syntax.

### React.createElement approach:
1. React.createElement call
2. Creates ReactElement (JS object)
3. Converts to HTML Element
4. Places in DOM

```javascript
const heading = React.createElement('h1', {id: 'heading'}, 'My First React Element!');
```

### JSX approach:
1. Write JSX
2. [Babel](https://babeljs.io) transpiles to React.createElement
3. Creates ReactElement (JS object)
4. Converts to HTML Element
5. Places in DOM

```javascript
const heading = <h1 id="heading">My First React Element!</h1>;
```

For multi-line jsx:
```javascript
const heading = (
  <h1 id="heading" className="title">
    My First React Element!
  </h1>
);
```

## [JSX](https://react.dev/learn/writing-markup-with-jsx)
* JSX is a syntax extension for JavaScript that allows you to write HTML-like markup inside JavaScript files.
* It uses preprocessors (like Babel) to transform HTML-like syntax into standard JavaScript objects that can be parsed by JavaScript engines.

## React Components

There are two ways to create React components:

1. **Class-based Components** (older approach)
2. **Functional Components** (modern approach, recommended)

> Note: React component names must start with a capital letter to avoid errors.

### Functional Component Example:
```jsx
const MyComponent = () => {
  return <div>Hello World</div>;
};
```

**Definition:**
A functional component is a normal JavaScript function that:
- Accepts props (optional)
- Returns JSX code/React element
- Uses modern React features like Hooks

This is the preferred way of creating components in modern React applications.

## Using JavaScript in JSX
Inside functional components, we can execute JavaScript expressions using curly braces `{ }`.

```jsx
const MyComponent = () => {
  const name = "John";
  const getGreeting = () => "Hello";

  return (
    <div>
      {getGreeting()} {name}!
      {2 + 2}
    </div>
  );
};
```

## Nesting Elements and Components

### React Element inside Component
```jsx
const heading = <h1>Hello World</h1>;

const MyComponent = () => {
  return (
    <div>
      {heading}
    </div>
  );
};
```

### React Element inside Another Element
```jsx
const title = <h2>Title</h2>;
const container = <div>{title}</div>;
```
> When we use a functional component into another functional component, then it is known as **component composition.**

## Security Note
The curly braces `{ }` in JSX provide automatic protection against XSS (Cross-Site Scripting):
- Any content within `{ }` is automatically escaped
- Helps prevent malicious code injection
- Makes React applications more secure by default

> Note: JSX makes the code readable and maintainable, while React handles the underlying processing and security.


## Rendering React Elements vs React Components

```jsx
// Rendering a React Element
root.render(heading)

// Rendering a React Component
root.render(<Heading />)
```

**Key Differences:**
- React Elements are rendered using just the variable name: `heading`
- React Components are rendered using JSX tags: `<Heading />`
- Component names must start with a capital letter
- Components are wrapped in angle brackets like HTML tags

> Note: When rendering components, they can be self-closing (`<Heading />`) or have a closing tag (`<Heading></Heading>`).


## Different Ways to Use Components in JSX

### 1. `{ Title }`
- Represents a JavaScript expression or variable
- Used when `Title` is a variable reference
- Not rendering a component directly

```jsx
const Title = "Hello";
const Component = () => {
  return <div>{Title}</div>; // Outputs: Hello
};
```

### 2. `{ <Title /> }`
- Represents a self-closing component
- Returns JSX
- Used for components without children

```jsx
const Title = () => <h1>Hello</h1>;
const Component = () => {
  return <div>{<Title />}</div>;
};
```

### 3. `{ <Title></Title> }`
- Same as `<Title />` when there are no children
- Used when component might have children
- More explicit syntax

```jsx
const Title = ({children}) => <h1>{children}</h1>;
const Component = () => {
  return (
    <div>
      <Title>Hello World</Title>
    </div>
  );
};
```

> Note: `<Title />` and `<Title></Title>` are equivalent only when the component doesn't have any children elements.

## Script Tag Type Attribute

The `type` attribute in the `<script>` tag:
- Specifies the type of script
- Identifies the content between `<script>` tags
- Has a default value of `text/javascript`

### Available Type Options

#### 1. `text/javascript`
- Basic standard for JavaScript code
- Default value if type is not specified
```html
<script type="text/javascript">
    // JavaScript code here
</script>
```

#### 2. `text/ecmascript`
- Indicates script follows ECMAScript standards
```html
<script type="text/ecmascript">
    // ECMAScript code here
</script>
```

#### 3. `module`
- Indicates script is a module
- Allows import/export functionality
```html
<script type="module">
    import { function } from './module.js';
</script>
```

#### 4. `text/babel`
- Indicates Babel transpilation is required
- Used with React/JSX
```html
<script type="text/babel">
    // React/JSX code here
</script>
```

#### 5. `text/typescript`
- Indicates TypeScript code
```html
<script type="text/typescript">
    // TypeScript code here
</script>
```