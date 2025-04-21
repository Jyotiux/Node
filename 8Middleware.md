

##  Express Middleware 

###  What is Middleware?

- A **middleware** is a **function** that has access to:
  - `req` (Request object)
  - `res` (Response object)
  - `next()` (Function to pass control to the next middleware)

- Syntax:
  ```js
  app.use((req, res, next) => {
    // Do something here
  });
  ```

---

###  Middleware Flow in Express

Let’s say a user sends a **GET** request to `/users`.

```js
app.get("/users", (req, res) => {
    res.send("All users");
});
```

If there is middleware defined before this route, Express will **first** run the middleware before reaching the handler.

---

###  Middleware Decision Flow

- **Client** → Middleware(s) → Route Handler
- Middleware can:
  - Allow request to proceed (`next()`)
  - **Interrupt** and **return response** immediately (e.g. for logging, validation, auth, etc.)

---

###  Middleware Chain

We can define multiple middlewares. Execution happens in the order they are declared:

```js
app.use((req, res, next) => {
    console.log("Middleware 1");
    next(); // Pass to next middleware
});

app.use((req, res, next) => {
    console.log("Middleware 2");
    next();
});
```

Flow:
```
Client -> M1 -> M2 -> Route handler
```

---

###  Example Middleware (Your Code)

```js
app.use((req, res, next) => {
    console.log("Hello from middleware 1");
    return res.json({ msgs: "Hello from middleware 1" });
});
```

 In this case:
- The middleware does **not call `next()`**
- So **no route handler will execute**
- The client gets JSON `{ msgs: "Hello from middleware 1" }` immediately

---

###  Real Use Cases of Middleware:
- Logging
- Authentication
- Input validation
- Error handling
- CORS setup
- Body parsing (e.g. `express.json()`)

---

###  TL;DR

- Middleware runs **before route handlers**
- You can have **multiple middlewares**
- Use `next()` to move to next step
- Middleware can also send responses directly

------------------------------------------------------------------


- `1.js` → Main server file
- `MOCK_DATA.json` → Stores user data as an array of objects
- Uses Express + `fs` + `path`

---

### 🧾 All API Routes Summary

| Method | Route               | Description                      |
|--------|---------------------|----------------------------------|
| GET    | `/api/users`        | Get list of all users (JSON)     |
| GET    | `/api/users/:id`    | Get single user by ID            |
| POST   | `/api/users`        | Add new user                     |
| PATCH  | `/api/users/:id`    | Update existing user by ID       |
| DELETE | `/api/users/:id`    | Delete a user by ID              |

---

### Code with Comments

```js
const express = require('express');
const users = require('./MOCK_DATA.json');
const path = require("path");
const fs = require('fs');

const app = express();
const PORT = 8000;

// middleware to parse url-encoded form data
app.use(express.urlencoded({ extended: false }));

// custom middleware for logging
app.use((req, res, next) => {
    console.log("Hello from middleware 1");
    next();
});

app.use((req, res, next) => {
    console.log("Hello from middleware 2");
    next();
});

// ==================== ROUTES ====================

// Get all users
app.get("/api/users", (req, res) => {
    return res.json(users);
});

// Get a single user by ID
app.get("/api/users/:id", (req, res) => {
    const id = Number(req.params.id);
    const user = users.find((user) => user.id === id);
    return res.json(user);
});

// Add a new user
app.post("/api/users", (req, res) => {
    const body = req.body;
    users.push({ ...body, id: users.length + 1 });

    fs.writeFile("./MOCK_DATA.json", JSON.stringify(users, null, 2), (err) => {
        if (err) {
            return res.status(500).json({ error: "Failed to write file" });
        }
        console.log("Body", body);
        return res.json({ status: "pending", user: body });
    });
});

// Update a user by ID
app.patch("/api/users/:id", (req, res) => {
    const id = Number(req.params.id);
    const body = req.body;

    const userIndex = users.findIndex((user) => user.id === id);
    if (userIndex === -1) {
        return res.status(404).json({ error: "User not found" });
    }

    users[userIndex] = { ...users[userIndex], ...body };

    fs.writeFile(
        path.join(__dirname, "./MOCK_DATA.json"),
        JSON.stringify(users, null, 2),
        (err) => {
            if (err) {
                return res.status(500).json({ error: "Failed to write to file" });
            }

            return res.json({
                status: "User updated",
                user: users[userIndex],
            });
        }
    );
});

// Delete a user by ID
app.delete("/api/users/:id", (req, res) => {
    const id = Number(req.params.id);
    const userIndex = users.findIndex(user => user.id === id);

    if (userIndex === -1) {
        return res.status(404).json({ error: "User not found" });
    }

    const deletedUser = users.splice(userIndex, 1);

    fs.writeFile(
        path.join(__dirname, "MOCK_DATA.json"),
        JSON.stringify(users, null, 2),
        (err) => {
            if (err) {
                return res.status(500).json({ error: "Failed to update file" });
            }

            return res.json({
                status: "User deleted successfully",
                user: deletedUser[0]
            });
        }
    );
});

// Start the server
app.listen(PORT, () => console.log(`Server started at Port: ${PORT}`));
```

---

### 🔧 Postman Setup

#### 🔹 `GET /api/users`
- Returns full list of users

#### 🔹 `GET /api/users/3`
- Returns user with ID 3

#### 🔹 `POST /api/users`
- **Body type**: x-www-form-urlencoded
- Example Body:
  ```
  first_name: J
  last_name: S
  email: j@example.com
  ```

#### 🔹 `PATCH /api/users/3`
- **Body type**: x-www-form-urlencoded
- Example Body:
  ```
  email: newemail@example.com
  ```

#### 🔹 `DELETE /api/users/3`
- Deletes user with ID 3


----------------------------------------------------------------------


