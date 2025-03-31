# **URL in Node.js**

## **1. What is a URL?**
A **URL (Uniform Resource Locator)** is the address used to access a resource on the internet. It consists of multiple components that define how the resource should be located and accessed.

### **Example of a URL**
```
https://www.jyoti.dev/path/to/page?search=hello&sort=asc
```

### **Components of a URL**
| Component | Example | Description |
|-----------|---------|-------------|
| **Protocol** | `https://` | The set of rules used for communication (HTTP, HTTPS, FTP, etc.). |
| **Domain (Host)** | `www.jyoti.dev` | The human-readable name mapped to an IP address. |
| **Path** | `/path/to/page` | Specifies the resource location on the server. |
| **Query Parameters** | `?search=hello&sort=asc` | Key-value pairs used to pass information to the server. |

---

## **2. Parsing a URL in Node.js**
- The `url` module in Node.js allows parsing and handling URLs.
- Install the module using:
  ```sh
  npm i url
  ```
  - This installs the `url` package inside the `node_modules` directory.

### **Example: Parsing a URL**
```javascript
const url = require("url");

const myUrl = url.parse("https://www.jyoti.dev/path/to/page?search=hello&sort=asc", true);
console.log(myUrl);
```
### **Output**
```json
{
  "protocol": "https:",
  "slashes": true,
  "host": "www.jyoti.dev",
  "hostname": "www.jyoti.dev",
  "pathname": "/path/to/page",
  "search": "?search=hello&sort=asc",
  "query": {
    "search": "hello",
    "sort": "asc"
  }
}
```

### **Breakdown**
- `protocol`: `"https:"` → The communication protocol.
- `host`: `"www.jyoti.dev"` → The full domain (including subdomains).
- `pathname`: `"/path/to/page"` → The resource path.
- `query`: `{ "search": "hello", "sort": "asc" }` → Extracted key-value pairs.

---

## **3. Using URL Parsing in a Node.js Server**
### **Example: Handling Different Routes**
```javascript
const http = require("http");
const fs = require("fs");
const url = require("url");  // Importing URL module

const myServer = http.createServer((req, res) => {
    // Create a log entry
    const log = `${Date.now()}: New Request Received\n`;

    // Parse the request URL
    const myUrl = url.parse(req.url, true);
    console.log(myUrl);

    // Append log asynchronously (non-blocking)
    fs.appendFile("log.txt", log, (err) => {
        if (err) console.error(err);

        // Handling different routes
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
                res.end("Results for: " + searchQuery);
                break;
            
            default:
                res.end("Not Found");
        }
    });
});

// Start the server
myServer.listen(8000, () => {
    console.log("Server started on port 8000");
});
```

### **How This Works**
1. **Parsing the URL**
   ```javascript
   const myUrl = url.parse(req.url, true);
   ```
   - `req.url` contains the requested URL.
   - `url.parse(req.url, true)` extracts components (path, query parameters).

2. **Handling Routes**
   - **`/` (Home Page)** → Responds with `"Home Page"`.
   - **`/about?myname=Jyoti`** → Extracts the `myname` parameter and responds with `"Hi, Jyoti"`.
   - **`/search?search_query=NodeJS`** → Extracts `search_query` and returns `"Results for: NodeJS"`.
   - **Unknown routes** return `"Not Found"`.

---

## **4. Testing the Server**
### **Example Requests and Responses**
| Request URL | Parsed Query | Response |
|-------------|-------------|----------|
| `http://localhost:8000/` | `{}` | `Home Page` |
| `http://localhost:8000/about?myname=Jyoti` | `{ myname: "Jyoti" }` | `Hi, Jyoti` |
| `http://localhost:8000/search?search_query=NodeJS` | `{ search_query: "NodeJS" }` | `Results for: NodeJS` |
| `http://localhost:8000/unknown` | `{}` | `Not Found` |

---

## **5. Key Takeaways**
| Concept | Explanation |
|---------|-------------|
| **URL Parsing** | `url.parse(req.url, true)` extracts URL components. |
| **Protocol** | Defines how the browser communicates (HTTP, HTTPS). |
| **Domain (Host)** | User-friendly representation of an IP address. |
| **Path** | Specifies the location of a resource on the server. |
| **Query Parameters** | Passed as key-value pairs after `?`, useful for searches and filters. |
| **Routing** | `switch(myUrl.pathname)` handles different URL paths. |

Using the **URL module**, we can extract query parameters, route requests efficiently, and build scalable web applications in Node.js. 
