---
icon: material/arrow-right
---

# 07 - Finding the Path

## useEffect:
- If there is no dependency array, the effect will run after every render.
```javascript
useEffect(() => {
  console.log('This will run after every render');
});
```
- If there is an empty dependency array, the effect will only run after the first render.
```javascript
useEffect(() => {
  console.log('This will only run after the first render');
}, []);
```
- If there is a dependency array, the effect will only run when the dependencies change.
```javascript
useEffect(() => {
  console.log('This will only run when the dependencies change');
}, [dependency1, dependency2]);
```

## useState:
- Never call useState inside a loop, condition, or nested function or outside of a React component.

## What is SPA?
SPA (Single Page Application) is a web application that interacts with users by dynamically rewriting the current web page instead of loading entire new pages from the server. It loads a single HTML page initially and updates content dynamically as users interact with the application.

### Key characteristics of SPAs:

1. **Dynamic Updates**
   - Content updates without full page reloads using JavaScript and client-side routing

2. **Smooth User Experience**
   - More responsive as only parts of the page update without full refreshes

3. **Initial Load**
   - May take longer initially but provides faster subsequent interactions

4. **Client-Side Routing**
   - Uses libraries like React Router to simulate traditional navigation

5. **API-Centric**
   - Communicates with backend APIs to fetch and send data, typically in JSON format

6. **State Management**
   - Often uses libraries like Redux or Vuex to manage application state

Common frameworks for building SPAs include React, Angular, and Vue.js, which provide tools for creating efficient and maintainable applications.

## Client-Side Routing vs Server-Side Routing

### Client-Side Routing

#### Key Features:
1. **Browser-Side Handling**
   - Routes managed in the browser
   - Uses frameworks like React Router or Vue Router
   - No server requests for page changes

2. **Performance**
   - Faster page transitions
   - No full page reloads
   - Dynamic DOM updates

3. **Architecture**
   - Typical in Single Page Applications (SPAs)
   - Initial HTML load followed by dynamic content updates
   - JavaScript-dependent

4. **Limitations**
   - SEO challenges
   - Requires additional setup for search engine optimization
   - May need server-side rendering (SSR) solutions

### Server-Side Routing

#### Key Features:
1. **Server-Side Handling**
   - Routes managed on server
   - Server generates new HTML for each route
   - Traditional web architecture

2. **Performance**
   - Slower page transitions
   - Full page reloads required
   - Higher server load

3. **Architecture**
   - Used in traditional multi-page websites
   - Each route serves a complete HTML page
   - Less JavaScript dependency

4. **Advantages**
   - Better SEO by default
   - Simpler implementation
   - Better for content-focused websites

### Best Use Cases
- **Choose Client-Side Routing for:**
  - Interactive applications
  - Dynamic content
  - Fast user experience requirements

- **Choose Server-Side Routing for:**
  - Content-heavy websites
  - SEO priority
  - Traditional web applications

Some applications use a hybrid approach, combining both methods to leverage their respective advantages.

# React Routing Guide

## Why react-router-dom?
- `react-router-dom` is specifically designed for web applications, while `react-router` is the core package
- `react-router-dom` includes all the features of `react-router` plus additional DOM-specific components
- It provides web-specific components like `<BrowserRouter>`, `<Link>`, and `<NavLink>`
- Always use `react-router-dom` for web applications unless you're building a React Native app

## Basic Routing Setup

### 1. Creating Router Configuration
```javascript
import { createBrowserRouter, RouterProvider, Outlet } from "react-router-dom";

const appRouter = createBrowserRouter([
  {
    path: "/",
    element: <AppLayout />,
    errorElement: <ErrorPage />,
    children: [
      {
        path: "/",
        element: <Body />
      },
      {
        path: "/about",
        element: <About />
      }
    ]
  }
]);
```
**Note:**
- `createBrowserRouter` creates the routing configuration
- Configuration defines what happens on specific routes
- `errorElement` handles undefined routes
- `children` defines nested routes

### 2. Implementing Router
```jsx
root.render(<RouterProvider router={appRouter} />);
```
**Note:**
- `RouterProvider` is necessary to provide routing configuration to the app
- Without `RouterProvider`, routes won't work

## Key Components

### 1. AppLayout Component
```jsx
const AppLayout = () => {
  return (
    <div className="app">
      <Header />
      <Outlet />  {/* Children components render here */}
    </div>
  );
};
```
**Note:**
- `Outlet` is a placeholder where child routes are rendered
- It automatically updates when route changes
- Essential for nested routing

### 2. Error Handling
```jsx
const ErrorPage = () => {
  const error = useRouteError();
  return (
    <div>
      <h1>Oops! Something went wrong</h1>
      <p>{error.message}</p>
    </div>
  );
};
```
**Note:**
- `useRouteError` hook provides detailed error information
- Useful for custom error pages
- Helps in debugging routing issues

## Dynamic Routing

### 1. Route Configuration
```jsx
{
  path: "/restaurant/:resId",
  element: <RestaurantMenu />
}
```
**Note:**
- `:resId` is a URL parameter
- Can have multiple dynamic parameters
- Makes routes flexible and reusable

### 2. Accessing Parameters
```jsx
const RestaurantMenu = () => {
  const { resId } = useParams();
  // Use resId as needed
};
```
**Note:**
- `useParams` hook extracts URL parameters
- Parameters are accessible as object properties
- Useful for fetching specific data based on URL

## Navigation Best Practices

### 1. Internal Links
```jsx
// Correct way
<Link to="/about">About</Link>

// Avoid
<a href="/about">About</a>
```
**Note:**
- Always use `Link` from react-router-dom for internal navigation
- `<a>` tags cause full page reloads, breaking SPA behavior
- `Link` maintains the single-page application experience

### 2. Benefits
- Preserves React application state
- Faster navigation
- Better user experience
- No unnecessary server requests

### 3. Advanced Features
- Can use `NavLink` for styled active links
- Supports programmatic navigation with `useNavigate` hook
- Enables route protection and guards
- Allows for route-specific data loading