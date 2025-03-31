### **HTTP Methods: What They Are & How They Work**
HTTP (HyperText Transfer Protocol) methods define actions that clients (browsers, mobile apps, etc.) can request from a server. These methods allow interaction with a web server by sending and retrieving data.

---

## **1. GET Method**
- **Purpose:** Fetch data from the server.
- **How it works:** The browser sends a request, and the server responds with data.
- **Example Use Cases:** 
  - Retrieving a web page
  - Fetching user profile details

**Example GET request:**
```http
GET /users/101 HTTP/1.1
Host: example.com
```
**Response:**
```http
200 OK
Content-Type: application/json

{
    "id": 101,
    "name": "Jyoti",
    "email": "jyoti@example.com"
}
```

---

## **2. POST Method**
- **Purpose:** Send data to the server (e.g., form submission, creating a new record).
- **How it works:** The client sends data in the request body, and the server processes it.
- **Example Use Cases:** 
  - User registration
  - Submitting a comment

**Example POST request:**
```http
POST /users HTTP/1.1
Host: example.com
Content-Type: application/json

{
    "name": "Jyoti",
    "email": "jyoti@example.com"
}
```
**Response:**
```http
201 Created
```

---

## **3. PUT Method**
- **Purpose:** Update an existing resource or create a new one if it doesn’t exist.
- **How it works:** The request includes the full updated data.
- **Example Use Cases:** 
  - Updating user profile details
  - Uploading a profile picture

**Example PUT request:**
```http
PUT /users/101 HTTP/1.1
Host: example.com
Content-Type: application/json

{
    "name": "Jyoti Sharma",
    "email": "jyoti.sharma@example.com"
}
```
**Response:**
```http
200 OK
```

---

## **4. PATCH Method**
- **Purpose:** Partially update an existing resource.
- **How it works:** Only the modified fields are sent.
- **Example Use Cases:** 
  - Updating only an email address without affecting other details

**Example PATCH request:**
```http
PATCH /users/101 HTTP/1.1
Host: example.com
Content-Type: application/json

{
    "email": "newemail@example.com"
}
```
**Response:**
```http
200 OK
```

---

## **5. DELETE Method**
- **Purpose:** Remove a resource from the server.
- **How it works:** The server deletes the specified resource.
- **Example Use Cases:** 
  - Deleting an account
  - Removing a comment

**Example DELETE request:**
```http
DELETE /users/101 HTTP/1.1
Host: example.com
```
**Response:**
```http
204 No Content
```

---

## **How to Handle HTTP Methods in Node.js**
### **Example: Implementing GET, POST, and Other Methods in a Server**
```javascript
const http = require("http");
const fs = require("fs");
const url = require("url");

const myServer = http.createServer((req, res) => {
    const log = `${Date.now()}: ${req.method} New Request Received\n`;
    const myUrl = url.parse(req.url, true);

    // Append log asynchronously (non-blocking)
    fs.appendFile("log.txt", log, (err) => {
        if (err) console.error(err);

        switch (myUrl.pathname) {
            case "/":
                res.end("Home Page");
                break;

            case "/about":
                const username = myUrl.query.myname;
                res.end(`Hi, ${username}`);
                break;

            case "/search":
                const search = myUrl.query.search_query;
                res.end("Results are: " + search);
                break;

            case "/signup":
                if (req.method === "GET") {
                    res.end("This is a signup form");
                } else if (req.method === "POST") {
                    res.end("Signup successful");
                }
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

### **Explanation of Code**
- Logs every incoming request type (`GET`, `POST`, etc.) into `log.txt`.
- Handles different routes (`/`, `/about`, `/search`, `/signup`).
- Implements **GET** (fetching a signup form) and **POST** (handling form submission).
- Uses `fs.appendFile()` for non-blocking file writing.

---

## **Key Takeaways**
| **HTTP Method** | **Purpose** | **Use Case** | **Idempotent?** |
|---------------|------------|-------------|---------------|
| **GET** | Retrieve data | Fetching a webpage, API request | ✅ Yes |
| **POST** | Create new data | User signup, adding comments | ❌ No |
| **PUT** | Update entire resource | Changing profile details | ✅ Yes |
| **PATCH** | Partially update a resource | Changing only the email | ❌ No |
| **DELETE** | Remove a resource | Deleting a user | ✅ Yes |

**Idempotent?** → If repeated requests produce the same result, it's idempotent.

---

## **Common Mistakes**
❌ **Using GET for sensitive data (e.g., login credentials)**
   - GET URLs are stored in browser history and can be cached.
   
❌ **Using POST for updates instead of PUT/PATCH**
   - PUT should be used for full updates, PATCH for partial updates.

---

By understanding and using these methods correctly, we can build efficient and scalable web applications. 

Handling multiple HTTP methods (GET, POST, PUT, PATCH, DELETE) for different routes using plain Node.js requires multiple if-else or switch statements, making code complex and less structured.

Express.js simplifies this by providing a more structured and readable way to define routes and handle requests.
