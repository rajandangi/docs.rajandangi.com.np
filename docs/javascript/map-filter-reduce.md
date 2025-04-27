---
icon: material/arrow-right
---

# Higher-Order Functions: Filter, Map, Reduce

JavaScript offers powerful built-in methods called **higher-order functions** that operate on arrays.

!!! question "What makes them "higher-order"?"

They take *other functions* as arguments, allowing you to write expressive and concise code for common data manipulation tasks. Let's dive into three of the most frequently used ones: `filter`, `map`, and `reduce`.

Remember, `filter`, `map`, and `reduce` are all higher-order functions because they accept another function as an argument to perform their specific task.

## :material-filter: `filter`

Ever found yourself needing to sift through a list (an array) and pick out only the items that meet a specific criterion? Maybe you want only the even numbers, or only the users who are active. That's precisely what the `filter()` method is designed for!

{==

`filter()` doesn't change your original array. Instead, it creates and returns a *new* array containing only the elements from the original array that pass the test you provide in your function.

==}

**Example:** Let's say we have an array of numbers and want a new array containing only numbers greater than 2.

```js
const arr = [2, 5, 10, 15];

// The function passed to filter() is called for each element (num).
// It should return true to keep the element, or false to discard it.
const output = arr.filter((num) => num > 2);

console.log(output); // Output: [ 5, 10, 15 ]
console.log(arr);    // Output: [ 2, 5, 10, 15 ] (Original array is untouched)
```
See? The original `arr` remains unchanged. `filter` gives us a fresh array with just the elements we wanted.

## :map: `map`

What if you have an array, but you need to transform *each* item in that array into something else? Perhaps you have an array of user objects, but you only need an array of their email addresses. Or maybe you have a list of prices and need to apply a discount to each one. This is where `map()` shines.

{==

The `map()` method creates a *new* array by taking each element from the original array and applying a transformation function to it. The result of this function for each element becomes an item in the new array.

==}

**Example:** Given a list of user objects, let's create a new array containing just their full names.

```js
const users = [
    { firstname: 'John', lastname: 'Doe', age: 30 },
    { firstname: 'Jane', lastname: 'Doe', age: 25 },
    { firstname: 'Jim', lastname: 'Carrey', age: 50 },
    { firstname: 'David', lastname: 'Doe', age: 30 }
];

// The function passed to map() defines how each 'user' object
// is transformed into a string (their full name).
const fullNames = users.map((user) => {
    let name = user.firstname + ' ' + user.lastname;
    return name;
});

console.log(fullNames); // Output: [ 'John Doe', 'Jane Doe', 'Jim Carrey', 'David Doe' ]
console.log(users);     // Original 'users' array remains unchanged.
```

!!! tip "Just like `filter`, `map` is non-destructive. It returns a brand new array reflecting the transformations."

## :material-filter-minus: `reduce`

Imagine you have a whole list of items, and your goal is to boil it all down to *one single value*. This could be:

*   Finding the sum of all numbers in an array.
*   Finding the maximum or minimum value.
*   Counting occurrences of items.
*   Grouping complex objects based on a property.

{==

This is the superpower of the `reduce()` method. It iterates through an array and accumulates a single result based on a function you provide.

==}

**How it works:**

The `reduce()` method executes a "reducer" function on each element of the array.

*   **The Reducer Function:** This function typically takes two main arguments:
    *   `accumulator` (`acc`): This is the value that gets carried over from one iteration to the next. It accumulates the result. Think of it as the "result so far".
    *   `currentValue` (`curr`): This is the current element from the array being processed.
*   **The Initial Value (Optional but Recommended):** `reduce()` also takes a second argument, which is the *initial value* for the `accumulator`. If you omit this, the first element of the array becomes the initial accumulator, and iteration starts from the second element. Providing an initial value is often clearer and safer.

!!! example "Example 1: Summing Array Elements"

Let's find the sum of numbers in an array. The classic way involves a loop:

```js
function findSum(arr) {
    let sum = 0; // Initialize sum
    for (let i = 0; i < arr.length; i++) {
        sum += arr[i]; // Accumulate in each step
    }
    return sum;
}

const arr = [2, 5, 10, 15];
console.log(findSum(arr)); // Output: 32
```

Now, let's achieve the same result elegantly using `reduce`:

```js
const arr = [2, 5, 10, 15];

// acc: accumulator (starts at 0)
// curr: current value being processed
// 0: initial value for the accumulator
const sum = arr.reduce((acc, curr) => {
    // The return value of this function becomes the 'acc' for the next iteration
    return acc + curr;
}, 0); // Start the accumulator at 0

console.log("Sum by Reduce:", sum); // Output: Sum by Reduce: 32
```
Much cleaner, right? The logic of "accumulating" is captured directly by `reduce`.

!!! example "Example 2: Grouping Data (Advanced Use Case)"

`reduce` isn't limited to simple calculations. It's incredibly versatile for restructuring data. Let's tackle a common problem: counting how many users belong to each age group. We want an output object like `{ '25': 1, '30': 2, '50': 1 }`.

```js
const users = [
    { firstname: 'John', lastname: 'Doe', age: 30 },
    { firstname: 'Jane', lastname: 'Doe', age: 25 },
    { firstname: 'Jim', lastname: 'Carrey', age: 50 },
    { firstname: 'David', lastname: 'Doe', age: 30 }
];

// Here, the initial value for the accumulator is an empty object {}
const ageGroupCount = users.reduce((acc, curr) => {
    const age = curr.age; // Get the age of the current user

    // Check if this age already exists as a key in our accumulator object
    if (acc[age]) {
        // If yes, increment its count
        acc[age]++;
    } else {
        // If no, initialize the count for this new age to 1
        acc[age] = 1;
    }

    // IMPORTANT: Always return the accumulator for the next iteration!
    return acc;
}, {}); // Start with an empty object {}

console.log(ageGroupCount); // Output: { '25': 1, '30': 2, '50': 1 }
```
In this case, the accumulator (`acc`) starts as `{}` and we progressively build this object during each iteration based on the `currentUser`.

## :chains: Chaining Methods

One of the most elegant aspects of `filter`, `map`, (and other array methods that return arrays) is that you can **chain** them together. Since `filter` returns a new array, you can immediately call `map` on that result, and so on. This allows for creating complex data transformation pipelines that remain highly readable.

**Problem:** Get the first names of all users who are younger than 31.

**Solution using Chaining `filter` and `map`:**

1.  **Filter:** First, select only the users whose `age` is less than 31.
2.  **Map:** Then, take the resulting array of younger users and transform it into an array containing only their `firstname`.

```js
const users = [
    { firstname: 'John', lastname: 'Doe', age: 30 },
    { firstname: 'Jane', lastname: 'Doe', age: 25 },
    { firstname: 'Jim', lastname: 'Carrey', age: 50 },
    { firstname: 'David', lastname: 'Doe', age: 30 }
];

// Chain filter() and map() together
const youngUserFirstNames = users
    .filter((user) => user.age < 31) // Step 1: Returns an array of users younger than 31
    .map(user => user.firstname);    // Step 2: Maps that array to an array of first names

console.log(youngUserFirstNames); // Output: [ 'John', 'Jane', 'David' ]
```
This reads almost like plain English: "Take users, filter by age less than 31, then map to first name."

**Alternative using only `reduce`:**

While chaining is often clearer for sequential filtering and mapping, you *can* achieve the same result using only `reduce`. This demonstrates its flexibility, though it might be slightly less intuitive for this specific problem compared to the chain.

```js
const users = [
    { firstname: 'John', lastname: 'Doe', age: 30 },
    { firstname: 'Jane', lastname: 'Doe', age: 25 },
    { firstname: 'Jim', lastname: 'Carrey', age: 50 },
    { firstname: 'David', lastname: 'Doe', age: 30 }
];

const youngUsersAgain = users.reduce((acc, currentUser) => {
    // Check the condition directly within the reducer
    if (currentUser.age < 31) {
        // If the user is young enough, push their firstname onto the accumulator array
        acc.push(currentUser.firstname);
    }
    // Return the accumulator (our growing list of names) for the next iteration
    return acc;
}, []); // Start with an empty array [] as the initial accumulator

console.log(youngUsersAgain); // Output: [ 'John', 'Jane', 'David' ]
```

Mastering `filter`, `map`, and `reduce` unlocks a more functional and expressive way to work with arrays in JavaScript, often leading to cleaner and more maintainable code.