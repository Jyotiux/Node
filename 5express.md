# **Building a Node.js Server Using HTTP and Express.js**

## **1. Using the HTTP Module**

### **1.1 Introduction**
The `http` module in Node.js allows us to create a web server that listens for HTTP requests and sends responses. However, handling different routes, methods, and query parameters manually can become complex and inefficient.

### **1.2 Basic HTTP Server**
Below is an example of creating a simple HTTP server using the `http` module.

```javascript
const http = require("http");
const fs = require("fs");
const url = require("url");

function myHandler(req, res) {
    const log = `${Date.now()}: ${req.method} New Request Received\n`;
    fs.appendFile("log.txt", log, (err) => {
        if (err) console.error(err);
    });

    const myUrl = url.parse(req.url, true);
    switch (myUrl.pathname) {
        case "/":
            res.end("Home Page");
            break;
        case "/about":
            const username = myUrl.query.myname;
            res.end(`Hi, ${username}`);
            break;
        case "/search":
            const searchQuery = myUrl.query.search_query;
            res.end("Results are: " + searchQuery);
            break;
        case "/signup":
            if (req.method === "GET") {
                res.end("This is a signup form");
            } else if (req.method === "POST") {
                res.end("Success");
            }
            break;
        default:
            res.end("Not Found");
    }
}

const myServer = http.createServer(myHandler);
myServer.listen(8000, () => {
    console.log("Server started on port 8000");
});
```

### **1.3 Challenges with the HTTP Module**
- Manually handling different HTTP methods (GET, POST, etc.)
- Parsing query parameters using `url.parse`
- Managing request handling for different paths using a switch-case structure
- Handling JSON responses manually
- Writing logs asynchronously

## **2. Using Express.js**

### **2.1 What is Express.js?**
Express.js is a fast, unopinionated, and minimalist web framework for Node.js. It simplifies the process of handling requests, routing, and middleware management.

### **2.2 Installing Express.js**
To use Express.js, first install it using npm:
```sh
npm install express
```

### **2.3 Creating a Basic Express Server**
```javascript
const express = require("express");

const app = express();

// Home route
app.get("/", (req, res) => {
    res.send("Hello from Home Page! Hey " + req.query.name + " " + req.query.age);
});

// About route
app.get("/about", (req, res) => {
    res.send("Hello from About Page");
});

// Start the server
app.listen(8000, () => console.log("Server Started on port 8000!"));
```

### **2.4 Advantages of Using Express.js**
- **Simplifies Routing:** No need for a manual switch-case for different routes.
- **Handles Query Parameters Easily:** Automatically extracts query parameters.
- **Middleware Support:** Easy integration with third-party middleware for logging, authentication, etc.
- **Built-in JSON Handling:** Supports JSON responses out-of-the-box.
- **Cleaner and Modular Code:** Easier to maintain and scale.

## **3. Routing in Express.js**
Routing refers to defining how an application responds to a client request at a specific endpoint with a particular HTTP method.

### **3.1 Syntax of Routing**
```javascript
app.method(path, handler);
```
- **`app`**: An instance of Express.
- **`method`**: HTTP method (`GET`, `POST`, `PUT`, `DELETE`, etc.).
- **`path`**: The URL path.
- **`handler`**: A function executed when the route is matched.

### **3.2 Example of Express Routes**
```javascript
app.get("/", (req, res) => {
    res.send("Welcome to the Home Page");
});

app.get("/about", (req, res) => {
    res.send("About Us Page");
});

app.get("/search", (req, res) => {
    res.send("Search Results: " + req.query.search_query);
});

app.post("/signup", (req, res) => {
    res.send("Signup Successful");
});
```

## **4. Running the Express Server**
After setting up the Express app, you can start the server using:
```sh
npm start
```
Then, visit:
```
http://localhost:8000/about?name=jyoti&age=20
```
This will output:
```
Hello from About Page
```

## **5. Summary**
| Feature          | HTTP Module | Express.js |
|-----------------|------------|------------|
| Routing        | Manual `switch-case` | Built-in `app.get()`, `app.post()`, etc. |
| Query Parsing  | `url.parse(req.url, true)` | `req.query` (automatic) |
| JSON Handling  | Manual JSON parsing | Built-in `res.json()` |
| Middleware Support | None | Extensive middleware support |
| Code Complexity | Higher | Simpler and more modular |

Express.js makes building a server in Node.js cleaner, modular, and efficient by reducing boilerplate code and providing built-in functionality for routing, request handling, and middleware support.

