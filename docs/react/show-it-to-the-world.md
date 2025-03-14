---
icon: material/arrow-right
---

# 10 - Jo Dikhta Hai, Vo Bikta Hai

## Exploring Ways to Style React Components with CSS

### 1. Inline CSS :pencil2:
Apply styles directly to elements using the `style` attribute.

```jsx
function Button() {
  return (
    <button style={{ backgroundColor: 'blue', color: 'white' }}>
      Click Me
    </button>
  );
}
```

!!! success "Pros"
    - Quick and easy for simple styles
    - Scoped to the specific element

!!! warning "Cons"
    - Limited CSS features
    - Harder to maintain for larger projects

### 2. Importing External Stylesheet :pencil:
Use separate `.css` files and import them into your React components.

```css
/* styles.css */
.button {
  background-color: blue;
  color: white;
}
```

```jsx
import './styles.css';

function Button() {
  return <button className="button">Click Me</button>;
}
```

!!! success "Pros"
    - Cleaner separation of concerns
    - Full access to CSS features

!!! warning "Cons"
    - Global scope can lead to naming conflicts

### 3. CSS Modules :octicons-link-16:
Scope CSS to specific components by using modular CSS files.

```css
/* Button.module.css */
.button {
  background-color: blue;
  color: white;
}
```

```jsx
import styles from './Button.module.css';

function Button() {
  return <button className={styles.button}>Click Me</button>;
}
```

!!! success "Pros"
    - Avoids naming conflicts
    - Maintains scope within components

!!! warning "Cons"
    - Requires configuration in the build process

## 4. Styled Components :nail_care:
Use a CSS-in-JS library to style components with tagged template literals.

```jsx
import styled from 'styled-components';

const Button = styled.button`
  background-color: blue;
  color: white;
`;

function App() {
  return <Button>Click Me</Button>;
}
```

!!! success "Pros"
    - Scoped styles with dynamic props
    - Enhanced styling capabilities

!!! warning "Cons"
    - Adds extra library dependency
    - May increase bundle size

## Conditional Styling in React :material-scale-balance:
Apply styles based on component state or props.

#### Using Inline Styles
```jsx
function Button({ primary }) {
  const style = {
    backgroundColor: primary ? 'blue' : 'gray',
    color: 'white',
  };

  return <button style={style}>Click Me</button>;
}
```

#### Using Class Names
```jsx
import './Button.css';

function Button({ primary }) {
  return (
    <button className={primary ? 'button-primary' : 'button-secondary'}>
      Click Me
    </button>
  );
}
```

#### Using Styled Components
```jsx
import styled from 'styled-components';

const Button = styled.button`
  background-color: ${(props) => (props.primary ? 'blue' : 'gray')};
  color: white;
`;

function App() {
  return <Button primary>Click Me</Button>;
}
```


!!! tip
    - Choose the method that best fits your project size and complexity.
    - For larger projects, CSS Modules or Styled Components can help maintain organized and scalable styles.
    - Conditional styling enhances interactivity and user experience by dynamically changing styles based on state or props.

