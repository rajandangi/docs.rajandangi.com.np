---
icon: material/arrow-right
---

# Debouncing and Throttling

Debouncing and throttling are two powerful techniques used to optimize performance in JavaScript applications, especially when dealing with events that can fire rapidly (like scrolling, resizing, or input events).

## Debouncing

Debouncing delays the execution of a function until after a specified time has passed since it was last invoked. It's like saying, "Wait until the user stops typing before making the API call."

### How It Works

```javascript linenums="1" title="debounce.js"
const debounce = (fn, delay) => {
    let timer;
    return function() {
        const context = this;
        const args = arguments;

        clearTimeout(timer);

        timer = setTimeout(() => {
            fn.apply(context, args);
        }, delay);
    }
}
```

### Real-World Example

```javascript linenums="1" title="debounce-example.js"
// Function we want to debounce
const getData = (searchTerm) => {
    console.log('Fetching:', searchTerm);
    // API call would go here
}

// Create debounced version
const debouncedGetData = debounce(getData, 300);

// Use it with an input field
document.getElementById('search').addEventListener('input', function(e) {
    debouncedGetData(e.target.value);
});
```

In this example, the API call is only made 300ms after the user stops typing.

## Throttling

Throttling limits how often a function can be called in a given time period. It's like saying, "No matter how many times this event fires, only execute the function once every X milliseconds."

### How It Works

```javascript linenums="1" title="throttle.js"
const throttle = (fn, limit) => {
    let flag = true;
    return function() {
        const context = this;
        const args = arguments;

        if (flag) {
            fn.apply(context, args);
            flag = false;

            setTimeout(() => {
                flag = true;
            }, limit);
        }
    }
}
```

### Real-World Example

```javascript linenums="1" title="throttle-example.js"
// Create throttled version of getData
const throttledGetData = throttle(getData, 400);

// Use it with an input field
document.getElementById('search2').addEventListener('input', function(e) {
    throttledGetData(e.target.value);
});
```

With throttling, the function executes immediately, then waits for the specified time before it can execute again.

## :thinking: When to Use Which?

- **Use debouncing** when you want to wait for a pause in activity before taking action (search inputs, window resizing)
- **Use throttling** when you want to ensure an action happens at a regular interval regardless of event frequency (infinite scrolling, game controls)

## :bulb: Key Differences

| Debouncing | Throttling |
|------------|------------|
| Delays function execution until after a period of inactivity | Executes function at a regular interval regardless of how many times the event is fired |
| Ideal for search inputs, form validation | Ideal for scroll events, mousemove, game input |
| Function might never execute if events keep firing | Function will execute at least once within the time interval |

Both techniques are essential tools for frontend developers to create responsive and performant user interfaces.