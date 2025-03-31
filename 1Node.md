# Node.js - 

## What is Node.js?

- **Node.js** is an open-source, cross-platform runtime environment for JavaScript.
- It allows JavaScript to run outside the browser.
- Uses **Chrome V8 engine**, which was extracted from the browser and embedded with C++.
- Enables JavaScript to interact with the native machine, providing capabilities such as file handling, encryption, etc.
- **Not a programming language** but a runtime environment.

## JavaScript Engines

- **JavaScript** was originally designed to run inside web browsers for interactivity.
- Various browsers have different JavaScript engines:
  - **Chrome:** V8 engine (most popular)
  - **Firefox:** SpiderMonkey
  - **Safari:** JavaScriptCore (Apple's JS engine)
- **Node.js is built on the V8 engine**, which allows JavaScript to run outside the browser.

## Features of Node.js

- **Single-threaded, event-driven architecture** (efficient for handling I/O operations).
- **Non-blocking I/O operations** (asynchronous processing for better performance).
- **Cross-platform compatibility** (works on Windows, macOS, Linux, etc.).
- **Removes DOM-related elements** (like `window`, `alert`, `prompt` which are specific to browsers).
- **Adds system-level functionalities** (like file handling, encryption, and more).

## Node.js Versioning

- Node.js follows a versioning system where:
  - **Even-numbered versions** (e.g., 16, 18, 20) are **LTS (Long-Term Support)**.
  - **Odd-numbered versions** (e.g., 17, 19, 21) are **experimental** and receive fewer updates.

---

## Setting Up a Node.js Project

### Initializing a Node.js Project

```sh
npm init
```
- Creates a **package.json** file, which is a configuration file containing:
  - Project name, author, dependencies, scripts, etc.
- Alternative: Use `npm init -y` to create the file with default values.

### Running a Node.js File

```sh
node filename.js
```
- This command executes a JavaScript file using Node.js.

### Using `package.json` for Running Scripts

- Add a script in `package.json`:
  ```json
  "scripts": {
    "start": "node filename.js"
  }
  ```
- Now run the project using:
  ```sh
  npm run start
  ```
  or simply:
  ```sh
  npm start
  ```

---

## Modules in Node.js

- **Modules** allow breaking down code into reusable components.
- Modules can be:
  1. **Built-in modules** (provided by Node.js, e.g., `fs`, `http`, `crypto`).
  2. **User-defined modules** (custom modules created by developers).
  3. **Third-party modules** (installed via npm, e.g., `express`).

### Creating a Custom Module

#### 1. Using `module.exports`

**math.js**
```js
function add(a, b) {
    return a + b;
}
module.exports = add;
```

**main.js**
```js
const add = require('./math');
console.log(add(2, 5));
```

#### 2. Exporting Multiple Functions

**math.js**
```js
module.exports = {
    addFn: (a, b) => a + b,
    subFn: (a, b) => a - b
};
```

**main.js**
```js
const math = require('./math');
console.log(math.addFn(2, 5));
```

#### 3. Using `exports`

**math.js**
```js
exports.add = (a, b) => a + b;
exports.sub = (a, b) => a - b;
```

**main.js**
```js
const { add, sub } = require('./math');
console.log(add(2, 5));
console.log(sub(5, 2));
```

---

## Built-in Modules in Node.js

Node.js provides several built-in modules, including:

### `http` (for creating servers)
```js
const http = require("http");
const server = http.createServer((req, res) => {
    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Hello, World!");
});
server.listen(3000, () => console.log("Server running on port 3000"));
```

### `fs` (File System Module)

#### Writing to a File
- **Synchronous** (Blocking Execution)
```js
const fs = require("fs");
fs.writeFileSync("test.txt", "Hey There");
```

- **Asynchronous** (Non-Blocking Execution)
```js
fs.writeFile("test.txt", "Hey There", (err) => {
    if (err) console.log("Error:", err);
});
```

#### Reading a File
- **Synchronous**
```js
const result = fs.readFileSync("test.txt", "utf-8");
console.log(result);
```

- **Asynchronous**
```js
fs.readFile("test.txt", "utf-8", (err, data) => {
    if (err) {
        console.log("Error:", err);
    } else {
        console.log(data);
    }
});
```

#### Appending to a File
```js
fs.appendFileSync("test.txt", "\nNew Data");
```

#### Copying a File
```js
fs.cpSync("test.txt", "copy.txt");
```

#### Deleting a File
```js
fs.unlinkSync("copy.txt");
```

#### Getting File Statistics
```js
console.log(fs.statSync("test.txt"));
```

---

## Summary

| Feature | Description |
|---------|-------------|
| **Node.js** | A JavaScript runtime built on Chrome's V8 engine |
| **npm** | Node Package Manager for installing packages |
| **Modules** | Helps organize code into reusable components |
| **Built-in Modules** | `fs`, `http`, `crypto`, etc. |
| **File Handling** | Read, write, append, delete files |
| **Event-Driven** | Uses callbacks and event loops |

Node.js is powerful for backend development, handling APIs, databases, and building scalable applications efficiently.
