### **Node.js Event Loop and Thread Pool**

---

## **1. Understanding the Event Queue and Event Loop**
- When a request is received, it is **queued** in the event queue.
- The **event loop** continuously checks for new requests and processes them in a **FIFO (First In, First Out)** manner.
- The event loop follows two paths based on the type of operation:
  1. **Non-blocking operations** → Directly processed and response sent back.
  2. **Blocking operations** → Sent to the **thread pool**.

---

## **2. Blocking vs Non-Blocking Operations**
- **Blocking (Synchronous) Operations**
  - A blocking request waits for the operation to complete before moving to the next task.
  - Example: File reading using `fs.readFileSync()`
  - Uses **synchronous execution**.

- **Non-Blocking (Asynchronous) Operations**
  - The request does not wait for the operation to complete.
  - Example: File reading using `fs.readFile()`
  - Uses **callback functions** or **Promises**.
  - Helps in **scalability**.

---

## **3. Thread Pool in Node.js**
- The **thread pool** is responsible for handling blocking operations in Node.js.
- It consists of worker threads that execute tasks.
- If a worker thread is busy, the task waits for an available thread.
- Default thread pool size in Node.js = **4**.

### **Finding the Maximum Number of CPU Cores**
```javascript
const os = require('os');
console.log(os.cpus().length);  // Outputs the number of CPU cores
```
- More CPU cores mean better parallel processing.

---

## **4. Creating an HTTP Web Server in Node.js**
### **Steps to Create a Server**
1. Initialize an npm project:
   ```sh
   npm init
   ```
2. Create an `index.js` file (good practice for the entry point).
3. Set `"main": "index.js"` in `package.json`.

### **Basic HTTP Server in Node.js**
```javascript
const http = require("http");

const myServer = http.createServer((req, res) => {
    console.log("New Request Received");
    res.end("Hello from server");
});

myServer.listen(8000, () => {
    console.log("Server started on port 8000");
});
```
- **`http.createServer(callback)`** → Expects a function that executes when a request is received.
- **`listen(port, callback)`** → Starts the server on the given port.

---

## **5. Understanding Request Headers**
- To get request details, use:
```javascript
const http = require("http");

const myServer = http.createServer((req, res) => {
    console.log(req.headers);
    res.end("Hello from server");
});

myServer.listen(8000, () => {
    console.log("Server started on port 8000");
});
```
- This prints the **request headers** (browser, user-agent, etc.).

---

## **6. Handling Routes in Node.js**
- Implement routing by checking `req.url`.

```javascript
const http = require("http");
const fs = require("fs");

const myServer = http.createServer((req, res) => {
    const log = `${Date.now()}: New Request Received\n`;

    // Append log asynchronously (non-blocking)
    fs.appendFile("log.txt", log, (err) => {
        if (err) console.error(err);

        switch (req.url) {
            case "/":
                res.end("Home Page");
                break;
            case "/about":
                res.end("I am a developer");
                break;
            default:
                res.end("Not Found");
        }
    });
});

myServer.listen(8000, () => {
    console.log("Server started on port 8000");
});
```
### **Key Points**
- **`fs.appendFile("log.txt", log, callback)`** → Logs request timestamps asynchronously.
- `switch(req.url)` is used for **routing**:
  - `/` → Home Page
  - `/about` → About Page
  - Any other route → Not Found

### **Why Non-Blocking I/O?**
- If we used `fs.appendFileSync()`, the server **blocks** while writing logs.
- Non-blocking `fs.appendFile()` ensures **better performance**.

---

## **7. Summary**
| Concept | Explanation |
|---------|-------------|
| **Event Loop** | Processes requests in FIFO order. |
| **Blocking (Sync)** | Tasks that wait until completion (e.g., `fs.readFileSync()`). |
| **Non-Blocking (Async)** | Tasks that execute asynchronously using callbacks (e.g., `fs.readFile()`). |
| **Thread Pool** | Handles blocking operations. Default size = 4. |
| **Node.js Server** | Uses `http.createServer()` to create an HTTP server. |
| **Routing** | Handled using `req.url` inside `createServer()`. |
| **Logging Requests** | Done using `fs.appendFile()` (non-blocking). |

