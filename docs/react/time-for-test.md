---
icon: material/arrow-right
---

# 13 - Time for Test
---

## Types of Testing

1. **Manual Testing**
   : Testing the functionality that has been developed.
    - *Example:* If we've developed a search bar, manual testing involves manually checking the search bar by entering queries.
    - ❌ This method isn't very efficient for large applications because it's impractical to test every new feature manually. A single change can introduce bugs throughout the app since multiple components are interconnected.

2. **Automated Testing**
   : Writing test cases to verify functionality automatically. It includes:
    1. **Unit Testing**
        Writing test cases for specific parts or isolated components.

    2. **Integration Testing**
        Writing test cases for connected components, such as the menu page and cart page.

    3. **End-to-End Testing**
        Writing test cases that simulate user interactions from entering the website to leaving it.

## 📦 Install Libraries

!!! note
    If you are using **Create React App** or **Vite**, you can skip these installation steps as these packages are already included.

### 1. 🧪 React Testing Library

**React Testing Library** builds on top of **DOM Testing Library** by adding APIs for working with React components.

- **Jest** is used behind the scenes.
- **Jest** is a delightful JavaScript Testing Framework focused on simplicity.
- It works with projects using Babel, TypeScript, Node, React, Angular, Vue, and more.
- It allows you to test React components effectively.

!!! info
    For more information, refer to the [React Testing Library Documentation](https://testing-library.com/docs/react-testing-library/intro).

**Install React Testing Library and DOM Testing Library:**

```bash
npm install --save-dev @testing-library/react @testing-library/dom
```

### 2. ⚙️ Jest

**Jest** is required as **React Testing Library** relies on it.

!!! info
    For more information, refer to the [Jest Documentation](https://jestjs.io/docs/getting-started).

**Install Jest:**

```bash
npm install --save-dev jest
```

### 3. 🛠️ Extra Babel Libraries

Since we are using **Babel** as a bundler, install the following additional libraries:

!!! info
    For more information, refer to the [Using Babel with Jest Documentation](https://jestjs.io/docs/getting-started#using-babel).

**Install Babel-related packages:**

```bash
npm install --save-dev babel-jest @babel/core @babel/preset-env
```

**Configure Babel:**

Create a `babel.config.js` file in the root of your project:

```javascript
// babel.config.js
const presets = [
  [
    "@babel/preset-env",
    {
      targets: {
        edge: "17",
        firefox: "60",
        chrome: "67",
        safari: "11.1",
      },
      useBuiltIns: "usage",
      corejs: "3.6.4",
    },
  ],
];
module.exports = { presets };
```

### 4. 🛡️ Configure Parcel

To avoid conflicts with Parcel's built-in Babel configuration, disable it by creating a `.parcelrc` file.

!!! info
    For more information, refer to the [Parcel Configuration Documentation](https://parceljs.org/languages/javascript/#usage-with-other-tools).

**Create `.parcelrc`:**

```json
// .parcelrc
{
  "extends": "@parcel/config-default",
  "transformers": {
    "*.{js,mjs,jsx,cjs,ts,tsx}": [
      "@parcel/transformer-js",
      "@parcel/transformer-react-refresh-wrap"
    ]
  }
}
```

### 5. ✅ Verify Installation

Run the test command to ensure everything is installed correctly. Initially, there will be no test cases.

```bash
npm run test
```

**Expected Output:**

```bash
> namaste-react@1.0.0 test
> jest

No tests found, exiting with code 1
```

### 6. 🛠️ Configure Jest

Initialize Jest configuration by running:

```bash
npx jest --init
```

**Follow the prompts:**

```bash
Would you like to use Typescript for the configuration file? … no
Choose the test environment that will be used for testing › jsdom (browser-like)
Do you want Jest to add coverage reports? … yes
Which provider should be used to instrument code for coverage? › babel
Automatically clear mock calls, instances, contexts and results before every test? … yes
```

This creates a `jest.config.js` file in your project.

!!! note
    We are using **jsdom** as the test environment to simulate a browser-like environment for running tests.

### 7. 🌐 Install jsdom Environment

!!! info
    For more information, refer to the [Setup Testing Library with Jest Documentation](https://testing-library.com/docs/react-testing-library/setup#jest-28).

**Install jsdom:**

```bash
npm install --save-dev jest-environment-jsdom
```

### 8. 📝 Start Writing Test Cases

#### Example: Testing a Sum Function

1. **Create the function file:**

   ```javascript
   // src/components/sum.js
   export const sum = (a, b) => {
     return a + b;
   };
   ```

2. **Create the test file:**

   ```javascript
   // src/__tests__/sum.test.js
   import { sum } from "../sum";

   test("Function should calculate the sum of two numbers", () => {
     const result = sum(3, 4);
     expect(result).toBe(7);
   });
   ```

3. **Run the test:**

   ```bash
   npm run test
   ```

**Expected Output:**

```bash
> namaste-react@1.0.0 test
> jest

PASS src/__tests__/sum.test.js
  ✓ Function should calculate the sum of two numbers

----------|---------|----------|---------|---------|-------------------
File      | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s
----------|---------|----------|---------|---------|-------------------
All files |     100 |      100 |     100 |     100 |
 sum.js   |     100 |      100 |     100 |     100 |
----------|---------|----------|---------|---------|-------------------
Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        0.306 s
Ran all test suites.
```

## 🧪 Testing React Components Made Easy

Testing ensures your React components work as expected. This guide covers **Unit Testing** and **Integration Testing** using **Jest** and **React Testing Library**. We'll use skeletal component structures for clarity and explain the reasoning behind each step, including important distinctions between various testing methods.

---

### 🧩 Unit Testing

Unit Testing focuses on individual components to ensure they function correctly in isolation.

#### 📄 Example Component Structure

Instead of a complete component, we'll use a skeleton to focus on testing logic.

```jsx
// Contact.jsx
const Contact = () => {
    return (
        <div>
            <h1>Contact</h1>
            <form>
                <input type="text" placeholder="Your Name" />
                <input type="email" placeholder="Your Email" />
                <textarea placeholder="Your Message"></textarea>
                <button>Send</button>
            </form>
        </div>
    );
};

export default Contact;
```

#### ✅ Writing a Test Case

**Objective:** Verify that the `Contact` component renders correctly.

**Why:** Ensures that all essential elements are present, preventing rendering issues.

```javascript
// Contact.test.jsx
import { render, screen } from '@testing-library/react';
import Contact from './Contact';
import '@testing-library/jest-dom'; // Extends Jest with custom matchers

test('renders Contact component', () => {
    render(<Contact />);
    const heading = screen.getByRole('heading', { name: /contact/i });
    expect(heading).toBeInTheDocument();
});
```

#### 🔍 Understanding `@testing-library/jest-dom`

- **Purpose:** Extends Jest with custom matchers like `toBeInTheDocument()`, enhancing test readability and expressiveness.
- **Usage:** Import it once in your test files to use additional matchers.

**Issue:** Missing `@testing-library/jest-dom` library can cause errors like `TypeError: expect(...).toBeInTheDocument is not a function`.

**Solution:**

1. **Install Jest DOM:**

    ```bash
    npm install --save-dev @testing-library/jest-dom
    ```

2. **Import Jest DOM in Test File:**

    ```javascript
    // Contact.test.jsx
    import '@testing-library/jest-dom';
    ```

#### 🔍 Why Use `{ name: /contact/i }`?

- **Purpose:** The `{ name: /contact/i }` option in `getByRole` searches for a heading with text matching the regular expression `/contact/i`.
- **Reason:**
  - **`/contact/i`**: The `/i` flag makes the search case-insensitive.
  - **Enhances Flexibility:** Allows the test to pass regardless of the text case, ensuring robustness.

#### 🔧 Enabling JSX in Testing

**Issue:** JSX may not be enabled, causing rendering errors.

**Solution:** Configure Babel to support JSX during tests.

1. **Install Babel Preset for React:**

    ```bash
    npm install --save-dev @babel/preset-react
    ```

2. **Configure Babel (`babel.config.json`):**

    ```json
    {
        "env": {
            "test": {
                "presets": [
                    ["@babel/preset-env", { "targets": { "node": "current" } }],
                    ["@babel/preset-react", { "runtime": "automatic" }]
                ]
            }
        }
    }
    ```

#### 📚 Grouping Multiple Test Cases

**Why:** Organizes related tests, making the test suite more readable and maintainable.

```javascript
// Contact.test.jsx
import { render, screen } from '@testing-library/react';
import Contact from './Contact';
import '@testing-library/jest-dom';

describe('Contact Component', () => {
    test('renders Contact heading', () => {
        render(<Contact />);
        const heading = screen.getByRole('heading', { name: /contact/i });
        expect(heading).toBeInTheDocument();
    });

    test('renders Send button', () => {
        render(<Contact />);
        const button = screen.getByText(/send/i);
        expect(button).toBeInTheDocument();
    });
});
```

---

### 🔗 Integration Testing

Integration Testing evaluates how different parts of your application work together.

#### 🛠 Automating Tests with a Watch Script

**Why:** Automatically run tests on file changes, improving development efficiency.

1. **Add `watch-test` Script (`package.json`):**

    ```json
    "scripts": {
        "watch-test": "jest --watch"
    }
    ```

2. **Run Watch Script:**

    ```bash
    npm run watch-test
    ```

#### 📄 Example Component Structures

**Header Component Skeleton:**

```jsx
// Header.jsx
import { Link } from 'react-router-dom';
import { useSelector } from 'react-redux';

const Header = () => {
    const cartItems = useSelector((store) => store.cart.items);

    return (
        <div>
            <h1>Site Name</h1>
            <nav>
                <Link to="/">Home</Link>
                <Link to="/cart">Cart ({cartItems.length})</Link>
                <button>Login</button>
            </nav>
        </div>
    );
};

export default Header;
```

**Body Component Skeleton:**

```jsx
// Body.jsx
import { useState, useEffect } from 'react';
import { Link } from 'react-router-dom';

const Body = () => {
    const [restaurants, setRestaurants] = useState([]);
    const [filteredRestaurants, setFilteredRestaurants] = useState([]);
    const [searchTerm, setSearchTerm] = useState('');

    useEffect(() => {
        // Fetch restaurants from API
    }, []);

    return (
        <div>
            <input placeholder="Search Restaurants" />
            <button>Search</button>
            <button>Top Rated Restaurants</button>
            {/* Restaurant cards will be rendered here */}
        </div>
    );
};

export default Body;
```

#### ✅ Writing Test Cases for Header Component

##### 🔧 Understanding `Provider` and `BrowserRouter`

- **`Provider`**:
  - **Purpose:** Makes the Redux store available to any nested components that need to access the Redux store.
  - **Usage:** Wrap your component with `Provider` and pass the store as a prop.

- **`BrowserRouter`**:
  - **Purpose:** Provides routing context to the components, enabling navigation functionalities.
  - **Usage:** Wrap your component with `BrowserRouter` to enable routing features like `Link` and `Route`.

```javascript
// Header.test.jsx
import { render, screen, fireEvent } from '@testing-library/react';
import Header from './Header';
import { Provider } from 'react-redux';
import store from '../../store';
import { BrowserRouter } from 'react-router-dom';
import '@testing-library/jest-dom'; // Extends Jest with custom matchers

test('renders Header with Login button', () => {
    render(
        <BrowserRouter>
            <Provider store={store}>
                <Header />
            </Provider>
        </BrowserRouter>
    );
    const loginButton = screen.getByText(/login/i);
    expect(loginButton).toBeInTheDocument();
});

test('changes Login to Logout on button click', () => {
    render(
        <BrowserRouter>
            <Provider store={store}>
                <Header />
            </Provider>
        </BrowserRouter>
    );
    const loginButton = screen.getByRole('button', { name: /login/i });
    fireEvent.click(loginButton);
    const logoutButton = screen.getByRole('button', { name: /logout/i });
    expect(logoutButton).toBeInTheDocument();
});
```

#### 🔥 What is `fireEvent`?

- **Purpose:** Simulates user interactions (e.g., clicks, typing) within tests.
- **Usage:**
  - **Syntax:** `fireEvent.eventName(element, eventData)`
  - **Example:**
    ```javascript
    fireEvent.click(button);
    fireEvent.change(input, { target: { value: 'new value' } });
    ```

#### 🔍 What is `getByPlaceholderText`?

**Purpose:** Selects input elements based on their placeholder text.

**Syntax:**

```javascript
screen.getByPlaceholderText(/placeholder text/i);
```

**Usage Example:**

```javascript
const searchInput = screen.getByPlaceholderText(/search restaurants/i);
fireEvent.change(searchInput, { target: { value: 'Pizza' } });
```

**Why Use It:**

- **Clarity:** Targets inputs by their descriptive placeholder.
- **Accessibility:** Reflects how users identify input fields through hints.

---

#### ✅ Writing Test Cases for Body Component

**Objective:** Test search functionality and API integration in `Body` component.

**Why:** Ensures that user interactions trigger the correct filtering and that API data is handled properly.

##### 📁 Mocking Fetch API

**Why:** Prevents actual API calls during tests, ensuring consistency and speed.

1. **Create Mock Data:**

    ```json
    // __mocks__/resListData.json
    [
        {
            "id": "1",
            "data": {
                "name": "Burger King",
                "rating": 4.5,
                "cuisins": ["American"],
                "time": "30",
                "promoted": true,
                "logo": "https://example.com/logo1.png"
            }
        },
        {
            "id": "2",
            "data": {
                "name": "McDonald's",
                "rating": 4.2,
                "cuisins": ["Fast Food"],
                "time": "25",
                "promoted": false,
                "logo": "https://example.com/logo2.png"
            }
        }
        // Add more mock restaurant data as needed
    ]
    ```

2. **Mock `fetch` Globally:**

    ```javascript
    // Body.test.jsx
    import { fireEvent, render, screen } from "@testing-library/react";
    import Body from "../Body";
    import REST_LIST_DATA from "../__mocks__/resListData.json";
    import { act } from "react-dom/test-utils";
    import '@testing-library/jest-dom';
    import { Provider } from "react-redux";
    import store from "../../store";
    import { BrowserRouter } from "react-router-dom";

    // Mock Fetch API globally
    global.fetch = jest.fn(() =>
        Promise.resolve({
            json: () => Promise.resolve(REST_LIST_DATA),
        })
    );
    ```

    **Why Define `global.fetch`:**

    - **Isolation:** Ensures tests do not make real API calls, which can be slow and flaky.
    - **Control:** Allows you to define exact responses, making tests predictable and repeatable.
    - **Performance:** Speeds up tests by avoiding network latency.

##### ⚙️ Creating a Wrapper for Rendering

**Why:** Provides necessary context (Redux store and Router) to the component being tested.

```javascript
const BodyWrapper = () => (
    <BrowserRouter>
        <Provider store={store}>
            <Body />
        </Provider>
    </BrowserRouter>
);
```

##### 🔄 Writing Test Cases

```javascript
describe("Body Component", () => {
    beforeEach(() => {
        fetch.mockClear(); // Clears previous mock calls
    });

    test("loads restaurant list after API call", async () => {
        await act(async () => {
            render(<BodyWrapper />);
        });
        const restaurantCards = await screen.findAllByTestId("resCard");
        expect(restaurantCards.length).toBe(REST_LIST_DATA.length);
    });

    test("searches for a specific restaurant", async () => {
        await act(async () => {
            render(<BodyWrapper />);
        });

        const searchInput = screen.getByPlaceholderText(/search restaurants/i);
        fireEvent.change(searchInput, { target: { value: "Burger" } });
        const searchButton = screen.getByRole("button", { name: /search/i });
        fireEvent.click(searchButton);

        const searchResults = await screen.findAllByTestId("resCard");
        const filtered = REST_LIST_DATA.filter(restaurant =>
            restaurant.data.name.toLowerCase().includes("burger".toLowerCase())
        );
        expect(searchResults.length).toBe(filtered.length);
    });

    test("filters top-rated restaurants", async () => {
        await act(async () => {
            render(<BodyWrapper />);
        });

        const topRatedButton = screen.getByRole("button", { name: /top rated restaurants/i });
        fireEvent.click(topRatedButton);

        const filteredResults = await screen.findAllByTestId("resCard");
        const expected = REST_LIST_DATA.filter(restaurant => restaurant.data.rating > 4);
        expect(filteredResults.length).toBe(expected.length);
    });
});
```

## 🔄 Why Use `async` and `await` with `act`?

- **Purpose of `act`:** Ensures that all updates related to component rendering and state changes are processed before making assertions.
- **Why `async/await`:**
  - **Asynchronous Operations:** Components often perform async tasks (e.g., API calls) that need to complete before tests proceed.
  - **Prevent Race Conditions:** Using `async/await` with `act` ensures that the component has fully rendered before assertions run.

## 📜 What is `import { act } from "react-dom/test-utils"`?

- **Purpose:** Batches state updates and effects, ensuring that component rendering is completed before assertions.
- **Usage:** Wrap asynchronous operations that trigger state updates within `act` to simulate real user interactions accurately.

```javascript
import { act } from "react-dom/test-utils";

await act(async () => {
    render(<Component />);
});
```

---

## 🟢 Using `getByPlaceholderText` vs `getAllByPlaceholderText`

- **`getByPlaceholderText`**:
  - **Use When:** You need to select a single input element by its placeholder text.
  - **Example:**
    ```javascript
    const searchInput = screen.getByPlaceholderText(/search restaurants/i);
    ```

- **`getAllByPlaceholderText`**:
  - **Use When:** You expect multiple input elements sharing the same placeholder text and want to select all of them.
  - **Example:**
    ```javascript
    const inputs = screen.getAllByPlaceholderText(/enter your name/i);
    expect(inputs.length).toBe(2);
    ```

- **Difference:**
  - `getByPlaceholderText` throws an error if no element or multiple elements match.
  - `getAllByPlaceholderText` returns an array of all matching elements and throws an error if none are found.

---

## 🟢 Using `findByTestId` vs `findAllByTestId`

- **`findByTestId`**:
  - **Use When:** You need to asynchronously find a single element by its `data-testid`.
  - **Returns:** A Promise that resolves to the found element.
  - **Example:**
    ```javascript
    const element = await screen.findByTestId('submit-button');
    expect(element).toBeInTheDocument();
    ```

- **`findAllByTestId`**:
  - **Use When:** You need to asynchronously find all elements matching a `data-testid`.
  - **Returns:** A Promise that resolves to an array of found elements.
  - **Example:**
    ```javascript
    const elements = await screen.findAllByTestId('item');
    expect(elements.length).toBe(3);
    ```

- **Difference:**
  - `findByTestId` is for a single element, while `findAllByTestId` is for multiple elements.
  - Both are asynchronous and useful for elements that appear after certain actions or delays.

---

## 🛠 Helper Functions in Jest

Jest provides functions to run code at specific stages of the testing lifecycle:

- **`beforeAll()`**: Runs once before all tests in a suite.
- **`afterAll()`**: Runs once after all tests in a suite.
- **`beforeEach()`**: Runs before each test.
- **`afterEach()`**: Runs after each test.

**Example:**

```javascript
describe("Test Suite", () => {
    beforeAll(() => {
        // Setup before all tests
    });

    afterAll(() => {
        // Cleanup after all tests
    });

    beforeEach(() => {
        // Setup before each test
    });

    afterEach(() => {
        // Cleanup after each test
    });

    test("test case 1", () => {
        // Test implementation
    });

    test("test case 2", () => {
        // Test implementation
    });
});
```

**Why Use Helper Functions:**

- **Code Reusability:** Avoids repetition by setting up common prerequisites.
- **Maintainability:** Centralizes setup and teardown logic, making tests cleaner.

---

## 💡 Additional Testing Tips

### 🎯 Using `getByRole` vs `getAllByRole`

- **`getByRole`**:
  - **Use When:** Selecting a single element by its ARIA role.
  - **Example:**
    ```javascript
    const submitButton = screen.getByRole('button', { name: /submit/i });
    ```

- **`getAllByRole`**:
  - **Use When:** Selecting multiple elements sharing the same ARIA role.
  - **Example:**
    ```javascript
    const buttons = screen.getAllByRole('button');
    expect(buttons.length).toBeGreaterThan(1);
    ```

**Recommendation:** Prefer `getByRole` for better accessibility and specificity. Use `getAllByRole` when multiple elements match the role.

---

## 🔄 Firing Events

**Why:** Simulates user interactions to test component responses.

**Example:**

```javascript
const button = screen.getByRole('button', { name: /login/i });
fireEvent.click(button);
```

**Common Events:**
- `click`: Simulates a mouse click.
- `change`: Simulates a value change in input elements.
- `submit`: Simulates form submission.

---

## 🔁 Testing Components with Redux

**Why:** Ensures components interacting with the global state behave correctly.

**How:**

1. **Wrap Component with `Provider`:**

    ```javascript
    import { Provider } from 'react-redux';
    import store from '../../store';

    render(
        <Provider store={store}>
            <Header />
        </Provider>
    );
    ```

2. **Why:**
    - **State Access:** Components using Redux hooks (`useSelector`, `useDispatch`) require access to the Redux store.
    - **Consistency:** Mimics the actual app environment where the store is provided.

**Example Test:**

```javascript
test('renders Header with Cart count from Redux store', () => {
    render(
        <Provider store={store}>
            <Header />
        </Provider>
    );
    const cartLink = screen.getByText(/cart/i);
    expect(cartLink).toHaveTextContent(`Cart (${store.getState().cart.items.length})`);
});
```

---

## 🔀 Testing Components with React Router

**Why:** Verifies navigation-related functionalities work as intended.

**How:**

1. **Wrap Component with `BrowserRouter`:**

    ```javascript
    import { BrowserRouter } from 'react-router-dom';

    render(
        <BrowserRouter>
            <Header />
        </BrowserRouter>
    );
    ```

2. **Why:**
    - **Routing Context:** Components using `Link`, `Route`, or other router features need routing context to function.
    - **Prevent Errors:** Avoids errors related to missing router context during tests.

**Example Test:**

```javascript
test('navigates to home page on Home link click', () => {
    render(
        <BrowserRouter>
            <Header />
        </BrowserRouter>
    );
    const homeLink = screen.getByText(/home/i);
    expect(homeLink).toHaveAttribute('href', '/');
});
```

---

## 🧩 Testing Components with Props

**Why:** Ensures components correctly handle and display received data.

**Example:**

```jsx
// ItemCard.jsx
const ItemCard = ({ name, price }) => (
    <div>
        <h2>{name}</h2>
        <p>{price}</p>
    </div>
);

export default ItemCard;
```

**Test Case:**

```javascript
// ItemCard.test.jsx
import { render, screen } from '@testing-library/react';
import ItemCard from './ItemCard';

test('renders ItemCard with props', () => {
    const mockProps = { name: 'Sample Item', price: '$10' };
    render(<ItemCard {...mockProps} />);
    expect(screen.getByText('Sample Item')).toBeInTheDocument();
    expect(screen.getByText('$10')).toBeInTheDocument();
});
```

---

## 🔍 Understanding `findBy` and `findAllBy`

- **`findBy`**:
  - **Use When:** You need to asynchronously find a single element.
  - **Returns:** A Promise that resolves to the found element.
  - **Example:**
    ```javascript
    const element = await screen.findByText(/loading/i);
    expect(element).toBeInTheDocument();
    ```

- **`findAllBy`**:
  - **Use When:** You need to asynchronously find multiple elements.
  - **Returns:** A Promise that resolves to an array of found elements.
  - **Example:**
    ```javascript
    const items = await screen.findAllByTestId('item');
    expect(items.length).toBeGreaterThan(0);
    ```

**Key Points:**

- Both are asynchronous and return Promises.
- Use them for elements that appear after asynchronous actions (e.g., API calls).
- Handle them with `async/await` in your tests.

---

## 📁 Git Configuration

### 🚫 Ignoring the Coverage Folder

After running tests, a `coverage` folder is generated. To prevent cluttering your Git repository:

1. **Add to `.gitignore`:**

    ```bash
    echo "coverage/" >> .gitignore
    ```

---

## 📜 Summary

- **Unit Testing**:
    - **Focus:** Individual components.
    - **Tools:** Jest, React Testing Library.
    - **Practices:** Use semantic selectors (`getByRole`), mock dependencies, group tests with `describe`.

- **Integration Testing**:
    - **Focus:** How components work together.
    - **Tools:** Jest, React Testing Library.
    - **Practices:** Mock API calls, provide necessary context (Redux, Router), test user interactions.

- **Best Practices**:
    - **Use Semantic Selectors:** Enhances accessibility and test reliability (`getByRole` over `getByTestId` when possible).
    - **Mock External Services:** Isolate tests and ensure consistency (`global.fetch` mocking).
    - **Organize Tests:** Group related tests using `describe` for clarity.
    - **Utilize Helper Functions:** Streamline setup and teardown processes (`beforeEach`, `afterEach`).
    - **Handle Asynchronous Code Properly:** Use `act`, `async/await` to manage state updates and async operations.
    - **Keep Tests Focused:** Ensure each test checks a specific functionality for better maintainability.