---
icon: material/arrow-right
---

# 09 - Optimizing our app


## What is the Single Responsibility Principle?

- A function, class, or any single entity in an app should have only one responsibility.
- For example, the `<Header>` component should solely display the application's header.
- If a component performs multiple tasks, it should be divided into smaller components, each with a single responsibility.
- Breaking down the code into small modules promotes modularity.

## Benefits of the Single Responsibility Principle

- **Reusability:** Components with single responsibilities can be reused in different parts of the application.
- **Maintainability:** It's easier to maintain and update code when each component has a clear and distinct purpose.
- **Testability:** Components with single responsibilities are easier to test and debug.

## 🤔 What is a Custom Hook?

- A hook is nothing more than a utility function.
- Hooks are reusable functions.
- When you have component logic that needs to be used by multiple components, you can extract that logic to a custom hook.
- A custom hook in React is a JavaScript function that allows you to extract and reuse logic involving stateful behavior and side effects from function components.
- Custom hooks enable you to encapsulate common logic in a way that can be shared across multiple components, promoting code reuse and better organization.

## 🌟 Why Use Custom Hooks?

### a. 🛠️ Code Reusability
Custom hooks allow you to reuse stateful logic across different components without duplicating code.

### b. 🧹 Cleaner Components
By extracting complex logic into custom hooks, you can keep your components smaller and more focused on rendering.

### c. 🔄 Separation of Concerns
Custom hooks help separate the logic from the UI, making your code easier to manage and understand.

## 📁 File Naming Convention
When creating custom hooks, it is a good practice to prefix the file name with `use`. For example, `useFetch.js`, `useLocalStorage.js`, etc.

## 📦 Component Example: Fetch Data from API and Display It

```jsx
import React, { useState, useEffect } from 'react';

const Home = () => {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch('https://api.example.com/data')
      .then((response) => response.json())
      .then((data) => {
        setData(data);
      });
  }, []);

  if (data === null) {
    return <p>Loading...</p>;
  }

  return (
    <div>
      <h1>{data.title}</h1>
      <p>{data.body}</p>
    </div>
  );
};

export default Home;
```

### Explanation
The above component performs two tasks:
1. **Fetch data from the API**
2. **Display data on the webpage**

We can modify this component to focus solely on displaying the data and create a custom hook to handle data fetching.

## 🔧 Our Custom Hook

```jsx
import { useState, useEffect } from 'react';

const useFetch = () => {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetchData();
  }, []);

  const fetchData = async () => {
    const response = await fetch('https://api.example.com/data');
    const data = await response.json();
    setData(data);
  };

  return data;
};

export default useFetch;
```

## 🔄 Using the Custom Hook in a Component

```jsx
import React from 'react';
import useFetch from './useFetch';

const Home = () => {
  const data = useFetch();

  if (data === null) {
    return <p>Loading...</p>;
  }

  return (
    <div>
      <h1>{data.title}</h1>
      <p>{data.body}</p>
    </div>
  );
};

export default Home;
```

### Benefits
- **Separation of Logic:** The logic for fetching data is moved into the `useFetch` hook.
- **Clean Component:** The `Home` component is now solely responsible for displaying data.
- **Reusability:** The `useFetch` hook can be reused in other components as needed.

## 🌐 Create a Custom Hook to Check Online Status

```jsx
import { useEffect, useState } from "react";

const useOnlineStatus = () => {
    const [isOnline, setIsOnline] = useState(true);

    useEffect(() => {
        const offline = () => setOnlineStatus(false);
        const online = () => setOnlineStatus(true);

        window.addEventListener('offline', offline);
        window.addEventListener('online', online);

        return () => {
            window.removeEventListener('offline', offline);
            window.removeEventListener('online', online);
        }
    }, [])
    return isOnline;
}

export default useOnlineStatus;
```

### Using the `useOnlineStatus` Hook in a Component

```jsx
import React from 'react';
import useOnlineStatus from './useOnlineStatus';

const OnlineStatus = () => {
  const isOnline = useOnlineStatus();

  return (
    <div>
      <h1>User is {isOnline ? 'Online' : 'Offline'}</h1>
    </div>
  );
};

export default OnlineStatus;
```

### Why Name Hooks with `use` Prefix?

- **Consistency:** It is a naming convention for custom hooks followed by most companies.
- **Linting Compatibility:** Many linters throw an error if custom hooks are not prefixed with `use`.
- **Clarity:** Using the `use` prefix indicates that the function is a React hook, not a regular function.
- **Readability:** It helps other developers understand that the function follows the rules of React hooks.

## 🏗️ Building Large-Scale Applications

When building a large-scale application, it's important to break it down into different components (bundles). Having a single bundle can slow down your app since it contains all the code, resulting in longer load times.

**Solution:** Split your app into smaller chunks (bundles). This process is known as:

1. **Chunking**
2. **Code Splitting**
3. **Dynamic Bundling**
4. **Lazy Loading**
5. **On-Demand Loading**

**Example:**
For an e-commerce application, you can create a separate bundle for the Cart component. This bundle will load only when the user visits the cart page. This approach results in two bundles:
- **Main Bundle:** Contains all app code except the Cart component, loaded when the user visits the app.
- **Cart Bundle:** Contains the Cart component code, loaded only when the user navigates to the shopping cart.

This process is also known as **on-demand loading**.

---

## 💤 What and Why Lazy Loading?

**Lazy Loading** is a design pattern that allows you to load parts of your application on demand, reducing the initial load time.

- **How It Works:**
  Initially, load components related to essential features like user login and registration. Load other components based on user navigation.

- **Impact:**
  While lazy loading may not show much difference in small applications, it significantly improves the performance of large-scale applications by reducing initial load time, enhancing user experience, and boosting application performance.

---

## ✅ Advantages of Lazy Loading

1. **Reduces Initial Load Time:** Smaller bundle sizes.
2. **Decreases Browser Workload.**
3. **Enhances Performance in Low-Bandwidth Situations.**
4. **Improves User Experience During Initial Loading.**
5. **Optimizes Resource Usage.**

---

## ❌ Disadvantages of Lazy Loading

1. **Not Suitable for Small-Scale Applications.**
2. **Placeholders Can Slow Down Quick Scrolling.**
3. **Requires Additional Server Communication to Fetch Resources.**
4. **Can Affect SEO and Ranking.**

---

## 📦 Example of Lazy Loading in React

```jsx
import React, { lazy, Suspense } from 'react';

const Cart = lazy(() => import('./Cart'));
```

- **Lazy:** Defers loading the component's code until it's rendered for the first time.
- **Potential Issue:** If a lazy-loaded component fetches API data, React might throw an error: *"A component suspended while responding to synchronous input."*

---

## 🛑 Handling Errors with Suspense

**Suspense** is a built-in React component that allows you to display a fallback UI while the component is loading.

- **Usage:**
  Wrap the lazy-loaded component inside the `<Suspense>` component.

- **Fallback UI:**
  Can be a simple loading indicator or a shimmer effect.

**Example:**

```jsx
import React, { lazy, Suspense } from 'react';

const Cart = lazy(() => import('./Cart'));

function App() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <Cart />
      </Suspense>
    </div>
  );
}
```

---

## ❓ When and Why Use Suspense?

- **Best Use Cases:**
  Display a fallback while waiting for something to load, such as:
  - Fetching data from an API after the initial page load.
  - Lazy loading other React components.

---

## ⚠️ Best Practices

**Avoid:** Declaring lazy components inside other components, as it can cause all state to reset on re-renders.

```jsx
import React, { lazy, Suspense } from 'react';

function App() {
  // 🔴 Bad: This will cause all state to be reset on re-renders
  const Cart = lazy(() => import('./Cart'));
  // ...
}
```

**Use Instead:** Declare the lazy component at the module level to ensure it's loaded only once.

```jsx
import React, { lazy, Suspense } from 'react';

// ✅ Good: Declare lazy components outside of your components
const Cart = lazy(() => import('./Cart'));

function App() {
  // ...
}
```

---

!!! note "Key Takeaways"
    - **Single Responsibility Principle:** Each component should have a single responsibility.
    - **Custom Hooks:** Reusable functions that encapsulate logic.
    - **Lazy Loading:** Loading parts of your application on demand.
    - **Suspense:** Displaying a fallback UI while components are loading.