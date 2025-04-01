**RESTful API Overview**

### 1. Understanding RESTful API
A **RESTful API** follows a set of rules for request and response handling between a server and a client (browser, mobile app, Alexa, etc.).

#### Key Concepts:
1. **Server-Client Architecture**
   - The server provides responses in **text, image, HTML, or JSON** format.
   - **SSR (Server-Side Rendering):** HTML generated on the server.
   - **CSR (Client-Side Rendering):** JSON sent to the client for rendering.

2. **HTTP Methods**
   - **GET:** Retrieve data
   - **POST:** Create new resource
   - **PUT/PATCH:** Update resource
   - **DELETE:** Remove resource

3. **Stateless Communication**
   - Authentication & Authorization handled separately.
   - Each request is independent (does not retain state).

4. **Response Handling in Express**
   - `res.send()`, `res.json()`, `res.render()`

---

### 2. Setting Up an Express Server
#### Install Dependencies
```sh
npm init -y
npm install express
```

#### Basic Server Setup
```javascript
const express = require("express");
const app = express();
const PORT = 8000;

app.listen(PORT, () => console.log(`Server started on port: ${PORT}`));
```

Modify `package.json` to set `"start": "node index.js"` for easy execution with `npm start`.

---

### 3. Creating a REST API
#### Defining API Routes
```javascript
const users = require("./MOCK_DATA.json");
const express = require("express");
const app = express();
const PORT = 8000;

// Route to get all users
app.get("/api/users", (req, res) => {
    return res.json(users);
});

// Route to get a specific user by ID
app.get("/api/users/:id", (req, res) => {
    const id = Number(req.params.id);
    const user = users.find((user) => user.id === id);
    return res.json(user);
});

app.listen(PORT, () => console.log(`Server started on port: ${PORT}`));
```

---

### 4. Handling Different HTTP Methods
#### CRUD Operations
```javascript
app.use(express.json()); // Middleware to parse JSON body

app.post("/api/users", (req, res) => {
    return res.json({ status: "User creation pending" });
});

app.patch("/api/users/:id", (req, res) => {
    return res.json({ status: "User update pending" });
});

app.delete("/api/users/:id", (req, res) => {
    return res.json({ status: "User deletion pending" });
});
```

**Route Grouping for Better Readability**
```javascript
app.route("/api/users/:id")
    .get((req, res) => {
        const id = Number(req.params.id);
        const user = users.find((user) => user.id === id);
        return res.json(user);
    })
    .patch((req, res) => {
        return res.json({ status: "User update pending" });
    })
    .delete((req, res) => {
        return res.json({ status: "User deletion pending" });
    });
```

---

### 5. Generating HTML vs. JSON
#### HTML Response Example (Server-Side Rendering - SSR)
```javascript
app.get("/users", (req, res) => {
    const html = `
    <ul>
        ${users.map((user) => `<li>${user.first_name}</li>`).join("")}
    </ul>
    `;
    res.send(html);
});
```

#### JSON API Response Example
```javascript
app.get("/api/users", (req, res) => {
    return res.json(users);
});
```

---

### 6. API Versioning Best Practices
To ensure backward compatibility and smooth updates, we can use versioning in API endpoints:

- **v1:** `/api/v1/users`
- **v2:** `/api/v2/users`

Example:
```javascript
app.get("/api/v1/users", (req, res) => {
    return res.json(users);
});
```

---

### 7. Running the Server
Start the server with:
```sh
npm start
```
Open in browser:
- `http://localhost:8000/api/users` (JSON response)
- `http://localhost:8000/users` (HTML response)

---

### Summary:
- **Express.js simplifies REST API development.**
- **CRUD operations** can be efficiently handled.
- **Middleware** helps in request parsing (e.g., `express.json()`).
- **Versioning ensures API maintainability.**

