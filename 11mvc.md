

You're building a **RESTful User Management System** using:

* **Express.js** for server-side framework
* **MongoDB** with Mongoose ODM
* **MVC Architecture** (Model, View, Controller)
* **Middleware** for logging requests
* **HTML rendering** for basic frontend view

---

## 📁 Project Folder Structure

```
project/
│
├── controllers/        # Business logic
│   └── user.js
│
├── models/             # Mongoose schema
│   └── user.js
│
├── routes/             # Route handling
│   └── user.js
│
├── middleware/         # Custom middleware
│   └── index.js
│
├── views/              # (Optional) HTML views
│
├── MOCK_DATA.json      # Sample data (optional)
├── connection.js       # MongoDB connection setup
├── index.js            # Main entry point
```

---

## 🔌 1. `connection.js` – Connect to MongoDB

```js
const mongoose = require("mongoose");

function connectMongoDb(url) {
    return mongoose.connect(url);
}

module.exports = { connectMongoDb };
```

---

## 🧠 2. `models/user.js` – User Schema

```js
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
    first_name: { type: String, required: true },
    last_name: { type: String },
    email: { type: String, required: true, unique: true },
    gender: { type: String },
    job_title: { type: String },  // Don't forget this if used in controller
});

const User = mongoose.model("user", userSchema);
module.exports = User;
```

---

## 🔁 3. `middleware/index.js` – Logger

```js
const fs = require('fs');

function logReqRes(filename) {
    return (req, res, next) => {
        fs.appendFile(filename,
            `${Date.now()}: ${req.method}: ${req.path}\n`,
            () => next()
        );
    };
}

module.exports = { logReqRes };
```

---

## 🧭 4. `routes/user.js` – Route Definitions

```js
const express = require('express');
const {
    handleGetAllUsers,
    handleGetUserById,
    handleUpdateUserById,
    handleDeleteUserById,
    handleCreateNewUser
} = require("../controllers/user");

const router = express.Router();

router.route("/")
    .get(handleGetAllUsers)
    .post(handleCreateNewUser);

router.route("/:id")
    .get(handleGetUserById)
    .patch(handleUpdateUserById)
    .delete(handleDeleteUserById);

module.exports = router;
```

---

## 🧩 5. `controllers/user.js` – Controller Logic

```js
const User = require("../models/user");

async function handleGetAllUsers(req, res) {
    try {
        const allDbUsers = await User.find();
        const html = `
        <ul>
        ${allDbUsers.map(user => `<li>${user.first_name}</li>`).join("")}
        </ul>
        `;
        res.send(html);
    } catch (err) {
        res.status(500).send("Internal Server Error");
    }
}

async function handleGetUserById(req, res) {
    const user = await User.findById(req.params.id);
    if (!user) return res.status(404).json({ error: "User not found" });
    return res.json(user);
}

async function handleCreateNewUser(req, res) {
    const { first_name, last_name, email, gender, job_title } = req.body;
    if (!first_name || !last_name || !email || !gender || !job_title) {
        return res.status(400).json({ msg: "All fields are required" });
    }
    const result = await User.create({ first_name, last_name, email, gender, job_title });
    return res.status(201).json({ msg: "success", id: result._id });
}

async function handleUpdateUserById(req, res) {
    await User.findByIdAndUpdate(req.params.id, { last_name: "Changed" });
    return res.json({ status: "Success" });
}

async function handleDeleteUserById(req, res) {
    await User.findByIdAndDelete(req.params.id);
    return res.json({ status: "Success" });
}

module.exports = {
    handleGetAllUsers,
    handleGetUserById,
    handleUpdateUserById,
    handleDeleteUserById,
    handleCreateNewUser,
};
```

---

## 🚀 6. `index.js` – App Entry Point

```js
const express = require('express');
const { connectMongoDb } = require("./connection");
const { logReqRes } = require("./middleware");
const userRouter = require('./routes/user');

const app = express();
const PORT = 8000;

connectMongoDb("mongodb://localhost:27017/youtubedb")
    .then(() => console.log("MongoDB connected"));

app.use(express.json()); // to parse JSON body
app.use(logReqRes("log.txt"));
app.use("/user", userRouter);

app.listen(PORT, () => console.log(`Server started at Port: ${PORT}`));
```

---

## 🧠 Key Concepts Recap

| Part                         | Role                                                             |
| ---------------------------- | ---------------------------------------------------------------- |
| **Model**                    | Mongoose schema defines data structure and DB interaction        |
| **View**                     | HTML string (or EJS/Pug/React optionally) returned in controller |
| **Controller**               | Handles HTTP logic (GET, POST, etc.) and talks to Model          |
| **Middleware**               | Custom logic run before controller (like logging)                |
| **Routes**                   | Registers URL paths and assigns them to controllers              |
| **Entry Point (`index.js`)** | Initializes MongoDB, middleware, and routes                      |

---

## ✅ Tips for Rebuilding

* Start from `index.js` — make sure MongoDB connects and middleware loads.
* Define your schema carefully in `models`.
* Use Postman or frontend HTML forms to test `POST`, `PATCH`, `DELETE`.
* Use `console.log()` inside each controller to debug.


