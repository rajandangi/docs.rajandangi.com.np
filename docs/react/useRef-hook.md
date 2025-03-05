---
icon: material/arrow-right
---

# 14 - useRef hook
----

## 🔗 Understanding `useRef` in React

The `useRef` hook in React is a versatile tool that allows you to persist values between renders without triggering re-renders when those values change. It's commonly used for accessing and manipulating DOM elements directly, storing mutable values, and integrating with third-party libraries.

## 📌 What is `useRef`?

`useRef` is a React hook that returns a mutable ref object. This object has a `.current` property that you can use to store any value, similar to how you might use instance fields in classes. Unlike state variables returned by `useState`, updating a ref does not cause a component to re-render.

```javascript
import { useRef } from 'react';

const Example = () => {
  const myRef = useRef(initialValue);

  // You can access or modify the value using myRef.current
};
```

## 🛠️ Common Use Cases

### 1. Accessing DOM Elements

`useRef` is often used to access DOM elements directly. This is useful for tasks like focusing an input field, selecting text, or measuring the size of an element.

```jsx
import React, { useRef } from 'react';

const FocusInput = () => {
  const inputRef = useRef(null);

  const focusInputField = () => {
    inputRef.current.focus();
  };

  return (
    <div>
      <input ref={inputRef} type="text" placeholder="Focus me with the button" />
      <button onClick={focusInputField}>Focus Input</button>
    </div>
  );
};

export default FocusInput;
```

### 2. Storing Mutable Values

`useRef` can store values that persist across renders without triggering re-renders when updated. This is useful for keeping track of previous values, timers, or any mutable data.

```jsx
import React, { useRef, useState, useEffect } from 'react';

const Timer = () => {
  const [seconds, setSeconds] = useState(0);
  const intervalRef = useRef(null);

  useEffect(() => {
    intervalRef.current = setInterval(() => {
      setSeconds(prev => prev + 1);
    }, 1000);

    return () => clearInterval(intervalRef.current);
  }, []);

  return <div>Seconds Elapsed: {seconds}</div>;
};

export default Timer;
```

### 3. Integrating with Third-Party Libraries

When working with libraries that require direct DOM manipulation or need to access DOM nodes, `useRef` provides a way to reference those nodes.

```jsx
import React, { useRef, useEffect } from 'react';
import SomeThirdPartyLibrary from 'third-party-library';

const ThirdPartyComponent = () => {
  const containerRef = useRef(null);

  useEffect(() => {
    const instance = new SomeThirdPartyLibrary(containerRef.current);
    instance.initialize();

    return () => {
      instance.destroy();
    };
  }, []);

  return <div ref={containerRef}></div>;
};

export default ThirdPartyComponent;
```

## 📝 Example: Login Component Using `useRef`

Let's explore a practical example of how `useRef` can be utilized within a React component, specifically a simplified Login component.

### 📄 Login Component Code

```jsx
import Header from "./Header";
import bgImage from "../assets/large-bg-image.jpg";
import { useRef, useState } from "react";
import { validateData } from "../utils/validate";

const Login = () => {
  const [isSignIn, setIsSignIn] = useState(true);
  const [errors, setErrors] = useState({});

  const emailRef = useRef();
  const passwordRef = useRef();
  const fullnameRef = useRef();

  const toggleForm = () => setIsSignIn(!isSignIn);

  const handleSubmit = () => {
    const formData = {
      email: emailRef.current.value,
      password: passwordRef.current.value,
      fullname: isSignIn ? undefined : fullnameRef.current.value,
    };
    const validationErrors = validateData(formData, isSignIn);
    setErrors(validationErrors);
  };

  return (
    <div
      className="bg-center bg-cover h-screen"
      style={{ backgroundImage: `url(${bgImage})` }}
    >
      <Header />
      <div>
        <div>
          <h1>
            {isSignIn ? "Sign In" : "Sign Up"}
          </h1>
          <form
            onSubmit={(e) => e.preventDefault()}
          >
            <div>
              <input
                ref={emailRef}
                type="email"
                placeholder="Email Address"
              />
              {errors.email && <p>{errors.email}</p>}
            </div>

            {!isSignIn && (
              <div>
                <input
                  ref={fullnameRef}
                  type="text"
                  placeholder="Full Name"
                />
                {errors.fullname && <p>{errors.fullname}</p>}
              </div>
            )}

            <div>
              <input
                ref={passwordRef}
                type="password"
                placeholder="Password"
              />
              {errors.password && <p className="text-red-500 text-sm">{errors.password}</p>}
            </div>

            <button
              type="submit"
              className="w-full bg-red-600 text-white p-2 rounded hover:bg-red-700"
              onClick={handleSubmit}
            >
              {isSignIn ? "Sign In" : "Sign Up"}
            </button>
          </form>
          <p className="text-gray-300 text-sm text-center">
            {isSignIn ? "New here?" : "Already have an account?"}
            <span
              className="text-white ml-1 cursor-pointer"
              onClick={toggleForm}
            >
              {isSignIn ? "Sign Up" : "Sign In"}
            </span>
          </p>
        </div>
      </div>
    </div>
  );
};

export default Login;
```

### 📊 How `useRef` is Utilized in the Login Component

In the `Login` component, `useRef` is employed to manage form input fields efficiently. Here's a breakdown of how it's used:

1. **Creating References:**

    ```jsx
    const emailRef = useRef();
    const passwordRef = useRef();
    const fullnameRef = useRef();
    ```

    - **`emailRef`**: References the email input field.
    - **`passwordRef`**: References the password input field.
    - **`fullnameRef`**: References the fullname input field (only visible during sign-up).

2. **Attaching References to DOM Elements:**

    Each input field is linked to its corresponding ref using the `ref` attribute.

    ```jsx
    <input
        ref={emailRef}
        type="email"
        placeholder="Email Address"
        /* ...other props */
    />

    <input
        ref={passwordRef}
        type="password"
        placeholder="Password"
        /* ...other props */
    />

    {!isSignIn && (
        <input
        ref={fullnameRef}
        type="text"
        placeholder="Full Name"
        /* ...other props */
        />
    )}
    ```

3. **Accessing Input Values:**

    When the user submits the form by clicking the button, the `handleSubmit` function accesses the current values of each input field using the `.current.value` property of the respective refs.

    ```javascript
    const handleSubmit = () => {
        const formData = {
        email: emailRef.current.value,
        password: passwordRef.current.value,
        fullname: isSignIn ? undefined : fullnameRef.current.value,
        };
        const validationErrors = validateData(formData, isSignIn);
        setErrors(validationErrors);
    };
    ```

4. **Advantages in This Context:**

    - **Simplified Form Handling:** By using refs, there's no need to bind state variables to each input field, reducing the complexity of state management.
    - **Performance Optimization:** Since updating refs doesn't trigger re-renders, the component remains performant, especially beneficial for forms with many fields.
    - **Direct Access:** Provides straightforward access to input values for validation or other operations without intermediary steps.

## ⚖️ `useRef` vs. `useState`

While both `useRef` and `useState` can hold values, they serve different purposes:

- **`useState`:**
  - Ideal for values that influence what gets rendered.
  - Updating state triggers a re-render of the component.
  - Suitable for form inputs when you need to display or react to user input in real-time.

- **`useRef`:**
  - Best for values that persist across renders but don't need to trigger re-renders when changed.
  - Ideal for accessing DOM elements directly or storing mutable values like timers or previous state values.
  - Prevents unnecessary re-renders, enhancing performance for certain use cases.

## ⚠️ Caveats and Pitfalls of Using `useRef`

While `useRef` is a powerful tool in React, it's essential to use it judiciously to avoid common mistakes and potential issues. Here are some **caveats and pitfalls** to be aware of, along with examples to illustrate each point:

### 1. **Bypassing React's Declarative Model**

!!! danger
    Using `useRef` to manipulate DOM elements directly can interfere with React's declarative approach, making the code harder to understand and maintain.

**Example: Inconsistent UI State**

Imagine a scenario where you use `useRef` to update an input's value directly, bypassing React's state management:

```jsx
import React, { useRef } from 'react';

const InconsistentInput = () => {
const inputRef = useRef(null);

const updateInput = () => {
    inputRef.current.value = "Updated via useRef!";
    // React is unaware of this change
};

return (
    <div>
    <input ref={inputRef} type="text" />
    <button onClick={updateInput}>Update Input</button>
    </div>
);
};

export default InconsistentInput;
```

In this example:

- Clicking the "Update Input" button changes the input's value directly.
- However, React remains unaware of this update since it's not using state.
- If other parts of the component rely on the input's value via state, inconsistencies can arise.

!!! success "Solution"
    Prefer controlled components using `useState` for form inputs and UI interactions unless there's a compelling reason to manipulate the DOM directly.

```jsx
import React, { useState } from 'react';

const ControlledInput = () => {
const [value, setValue] = useState('');

const updateInput = () => {
    setValue("Updated via useState!");
};

return (
    <div>
    <input
        type="text"
        value={value}
        onChange={(e) => setValue(e.target.value)}
    />
    <button onClick={updateInput}>Update Input</button>
    </div>
);
};

export default ControlledInput;
```

### 2. **Potential for Stale or Inconsistent Data**

!!! bug
    Since updating a ref does not trigger a re-render, there's a risk of the UI displaying outdated information if the ref's value changes but the component doesn't re-render to reflect those changes.


**Example: Stale Data in Event Handlers**

Consider a counter using `useRef` to track the current count without re-rendering:

```jsx
import React, { useRef } from 'react';

const StaleCounter = () => {
  const countRef = useRef(0);

  const handleClick = () => {
    countRef.current += 1;
    console.log(`Current count: ${countRef.current}`);
  };

  return (
    <div>
      <p>Counter: {countRef.current}</p> {/* Always displays 0 */}
      <button onClick={handleClick}>Increment</button>
    </div>
  );
};

export default StaleCounter;
```

In this example:

- Clicking "Increment" increases `countRef.current` and logs it.
- However, the displayed counter (`countRef.current`) remains `0` because the component doesn't re-render.

!!! success "Solution"
    Use `useState` for values that need to be reflected in the UI to ensure consistency.


```jsx
import React, { useState } from 'react';

const PersistentCounter = () => {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    setCount(prev => prev + 1);
    console.log(`Current count: ${count + 1}`);
  };

  return (
    <div>
      <p>Counter: {count}</p>
      <button onClick={handleClick}>Increment</button>
    </div>
  );
};

export default PersistentCounter;
```

### 3. **Overusing Refs for State Management**

!!! bug
    Relying heavily on `useRef` for managing state can lead to code that's difficult to debug and reason about, as it sidesteps React's state management patterns.


**Example: Managing Multiple States with Refs**

```jsx
import React, { useRef } from 'react';

const OverusedRefs = () => {
  const firstNameRef = useRef('');
  const lastNameRef = useRef('');
  const emailRef = useRef('');

  const handleSubmit = () => {
    console.log(`Name: ${firstNameRef.current} ${lastNameRef.current}`);
    console.log(`Email: ${emailRef.current}`);
  };

  return (
    <form onSubmit={(e) => { e.preventDefault(); handleSubmit(); }}>
      <input type="text" placeholder="First Name" onChange={(e) => { firstNameRef.current = e.target.value; }} />
      <input type="text" placeholder="Last Name" onChange={(e) => { lastNameRef.current = e.target.value; }} />
      <input type="email" placeholder="Email" onChange={(e) => { emailRef.current = e.target.value; }} />
      <button type="submit">Submit</button>
    </form>
  );
};

export default OverusedRefs;
```

In this example:

- Managing multiple form fields with `useRef` complicates the component.
- It becomes harder to track changes, validate inputs, and manage stateful behaviors.
- Debugging becomes more challenging due to the lack of a centralized state.

!!! success "Solution"
    Use `useState` or form management libraries like Formik or React Hook Form for handling multiple form inputs efficiently.


```jsx
import React, { useState } from 'react';

const ControlledForm = () => {
  const [form, setForm] = useState({ firstName: '', lastName: '', email: '' });

  const handleChange = (e) => {
    const { name, value } = e.target;
    setForm(prev => ({ ...prev, [name]: value }));
  };

  const handleSubmit = () => {
    console.log(`Name: ${form.firstName} ${form.lastName}`);
    console.log(`Email: ${form.email}`);
  };

  return (
    <form onSubmit={(e) => { e.preventDefault(); handleSubmit(); }}>
      <input name="firstName" type="text" placeholder="First Name" value={form.firstName} onChange={handleChange} />
      <input name="lastName" type="text" placeholder="Last Name" value={form.lastName} onChange={handleChange} />
      <input name="email" type="email" placeholder="Email" value={form.email} onChange={handleChange} />
      <button type="submit">Submit</button>
    </form>
  );
};

export default ControlledForm;
```

### 4. **Memory Leaks with Unmanaged Refs**

!!! bug
    If refs are used to store subscriptions, event listeners, or instances of third-party libraries without proper cleanup, it can lead to memory leaks.


**Example: Unmanaged Event Listener**

```jsx
import React, { useRef, useEffect } from 'react';

const MemoryLeakComponent = () => {
  const buttonRef = useRef(null);

  useEffect(() => {
    const handleClick = () => console.log('Button clicked!');
    const button = buttonRef.current;
    button.addEventListener('click', handleClick);

    // Missing cleanup
  }, []);

  return <button ref={buttonRef}>Click Me</button>;
};

export default MemoryLeakComponent;
```

In this example:

- An event listener is added to the button using `useRef`.
- The `useEffect` hook does not return a cleanup function to remove the event listener.
- If the component unmounts, the event listener remains, causing a memory leak.

!!! success "Solution"
    Always clean up side effects in the `useEffect` cleanup function when using refs for subscriptions or event listeners.


```jsx
import React, { useRef, useEffect } from 'react';

const ManagedMemoryLeakComponent = () => {
  const buttonRef = useRef(null);

  useEffect(() => {
    const handleClick = () => console.log('Button clicked!');
    const button = buttonRef.current;
    button.addEventListener('click', handleClick);

    return () => {
      button.removeEventListener('click', handleClick);
    };
  }, []);

  return <button ref={buttonRef}>Click Me</button>;
};

export default ManagedMemoryLeakComponent;
```

### 5. **Refs Not Updating Immediately**

!!! bug
    Updates to refs are synchronous, but accessing them immediately after setting can sometimes lead to unexpected behaviors, especially within event handlers.


**Example: Immediate Access After Update**

```jsx
import React, { useRef } from 'react';

const ImmediateAccessRef = () => {
  const inputRef = useRef('');

  const handleChange = (e) => {
    inputRef.current = e.target.value;
    console.log(inputRef.current); // Always logs the latest value
  };

  const handleSubmit = () => {
    // Trying to use inputRef.current here works fine
    console.log(`Submitted value: ${inputRef.current}`);
  };

  return (
    <div>
      <input type="text" onChange={handleChange} />
      <button onClick={handleSubmit}>Submit</button>
    </div>
  );
};

export default ImmediateAccessRef;
```

While this example works as expected, issues can arise in more complex scenarios where multiple updates happen in quick succession or within nested events, leading to potential inconsistencies.

!!! success "Solution"
    To ensure the latest value is accessed, use the updated ref value within the next render cycle or within a `useEffect` hook to guarantee the value is up-to-date.


### 6. **Server-Side Rendering (SSR) Considerations**

!!! bug
    Accessing DOM elements via refs doesn't make sense in a server-side rendering context, as the DOM isn't available.

**Example: Ref Access in SSR**

```jsx
import React, { useRef, useEffect } from 'react';

const SSRComponent = () => {
  const divRef = useRef(null);

  useEffect(() => {
    // This will run only on the client, not on the server
    if (divRef.current) {
      console.log('Div dimensions:', divRef.current.getBoundingClientRect());
    }
  }, []);

  return <div ref={divRef}>Hello, world!</div>;
};

export default SSRComponent;
```

In this example:

- `useRef` is used to access the div's dimensions.
- During server-side rendering, `useEffect` doesn't run, so no errors occur.
- However, attempting to access `divRef.current` outside of `useEffect` could cause issues.

!!! success "Solution"
    Ensure that any ref-based DOM manipulations occur within `useEffect` or conditional checks that verify the presence of the DOM.

```jsx
import React, { useRef, useEffect } from 'react';

const SafeSSRComponent = () => {
  const divRef = useRef(null);

  useEffect(() => {
    if (typeof window !== 'undefined' && divRef.current) {
      console.log('Div dimensions:', divRef.current.getBoundingClientRect());
    }
  }, []);

  return <div ref={divRef}>Hello, world!</div>;
};

export default SafeSSRComponent;
```

## 📚 Best Practices

<div class="grid cards" markdown>

-   :material-car-emergency:{ .lg .middle } __Use When Necessary__

    ---

    Prefer `useState` for managing form inputs unless there's a specific need to avoid re-renders.

-   :do_not_litter:{ .lg .middle } __Avoid Overuse__

    ---

    Relying heavily on refs for state management can lead to code that's hard to maintain and debug.

-   :simple-apachenetbeanside:{ .lg .middle } __Encapsulate Logic__

    ---

    For complex interactions, consider encapsulating ref-related logic within custom hooks to keep components clean.

-   :simple-ccleaner:{ .lg .middle } __Cleanup Effects__

    ---

    When using refs for side effects (like event listeners or third-party libraries), ensure you clean them up appropriately to prevent memory leaks.

-   :material-sync:{ .lg .middle } __Combine with `useEffect`__

    ---

    When working with refs that interact with the DOM, use `useEffect` to handle side effects after the component has rendered.

</div>

## 📝 Summary

The `useRef` hook is a powerful tool in React for managing mutable values and interacting directly with DOM elements without the overhead of state management. In the provided `Login` component example, `useRef` simplifies form handling by providing direct access to input values, enhancing both performance and code readability. However, it's crucial to use `useRef` judiciously to avoid bypassing React's declarative patterns and to prevent potential issues like memory leaks or inconsistent UI states.

Understanding the **caveats and pitfalls** of `useRef`, such as memory leaks and inconsistent UI states, is essential for building efficient and maintainable React applications. By adhering to best practices and being mindful of potential issues, you can leverage `useRef` effectively without compromising the integrity of your components.