---
icon: simple/nodedotjs
---

# Node.js

!!! quote "Jeff Atwood"
    Any application that can be written in JavaScript, will eventually be written in JavaScript.

## Introduction

Node.js is a cross-platform, open-source JavaScript runtime environment that can run on Windows, Linux, Unix, MacOS, and more. Node.js runs on the V8 JavaScript engine and executes JavaScript code outside of a web browser.

- Node.js is a JavaScript runtime environment built on Chrome's V8 JavaScript engine.
- It is maintained by the OpenJS Foundation.
- Node.js allows JavaScript to be executed outside of a web browser.
  - This means that while JavaScript is traditionally associated with the web and
  browsers, Node.js extends its reach, allowing you to run JavaScript code on
  servers, desktops, and even embedded systems. This is why Node.js is often
  described with the phrase "Run JavaScript Everywhere."
- Node.js features an event-driven architecture and supports asynchronous I/O (non-blocking I/O).

!!! note "JS Engine"
    To run JavaScript, you always need a JS engine. Everywhere you write JS code, there is a JS engine that executes the code.
    
## History of Node.js

- Node.js was created by Ryan Dahl in 2009.

- Ryan started with the [SpiderMonkey JavaScript engine](https://spidermonkey.dev), which was the first JavaScript engine to be used in a web browser. SpiderMonkey is Mozilla's JavaScript and WebAssembly Engine, used in Firefox, Servo and various other projects. It is written in C++, Rust and JavaScript. But, after just two days he switched to the [V8 JavaScript engine](https://v8.dev), which is the JavaScript engine used by Google Chrome, and never looked back.

- Initially, Ryan was working independently, but a company named Joyent was working on something similar to Node.js. They hired Ryan to work under them and funded his project, making a significant contribution to Node.js's development.

- The original name of Node.js was actually Web.js, but Ryan renamed it to Node.js because he intended it to be used for more than just web servers.

- Apache HTTP Server was a blocking server, so Ryan wanted to create a non-blocking server, which is why Node.js uses non-blocking I/O. The advantage of a non-blocking server is that it can handle multiple requests with fewer threads.

- In 2010, NPM (Node Package Manager) was introduced. NPM is a package manager for Node.js, and it played a critical role in the success of Node.js. Without NPM, Node.js might not have become as successful as it is today.

- Initially, Node.js was built only for macOS and Linux, but in 2011, it gained support for Microsoft Windows as well.

- In 2012, Isaac Z. Schlueter, the creator of NPM, began maintaining Node.js.

- In 2014, a developer named Fedor created a fork of Node.js called io.js, which led to controversy within the community. As a result, several developers began maintaining the io.js branch.

- In September 2015, Node.js and io.js were merged, and the combined project was named the Node.js Foundation.

- In 2019, the JS Foundation and the Node.js Foundation merged to form the OpenJS Foundation, which now maintains Node.js.

![Nodejs History Continue](./assets/history-summary-1.png){loading="lazy"}
![Nodejs History](./assets/history-summary-2.png){loading="lazy"}
