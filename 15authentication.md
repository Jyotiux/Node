
## 🗂️ Project Structure 

```
├── controllers/
│   ├── url.js                <-- Handles URL shortening and analytics
│   └── user.js               <-- Handles user signup and login
│
├── middlewares/
│   └── auth.js               <-- Middleware for login-check and token validation
│
├── models/
│   ├── url.js                <-- URL schema for MongoDB
│   └── user.js               <-- User schema for MongoDB
│
├── routes/
│   ├── staticRoutes.js       <-- Frontend-facing routes (EJS views like /login, /signup)
│   ├── url.js                <-- API for URL shortening (POST /)
│   └── user.js               <-- API for user actions (POST /login, POST /signup)
│
├── service/
│   └── auth.js               <-- JWT/token or session management logic
│
├── views/
│   └── *.ejs                 <-- EJS templates (login.ejs, signup.ejs, home.ejs)
│
├── index.js                 <-- Main entry point
├── package.json             <-- NPM metadata
```

---

## 🔒 Auth: `service/auth.js`

This is the **core utility** to manage **JWT tokens**.

### Stateless Auth (JWT):

```js
function setUser(user) {
    return jwt.sign({ name: user.name, email: user.email }, secret);
}

function getUser(token) {
    if (!token) return null;
    return jwt.verify(token, secret);
}
```

* `setUser()` signs and returns a token for a logged-in user.
* `getUser()` decodes the token back to user data (used in middleware).

✅ **Why this is better**:

* No need to store session in DB/server.
* Works great with APIs and scalable apps.

---

## 🛡️ Middleware: `middlewares/auth.js`

Two middlewares:

### `restrictedToLoggedinUserOnly`

* Used to protect backend routes (like `/url`) that only logged-in users should access.
* Checks cookie `uid` (JWT token) → verifies user → attaches user to `req.user`.

### `checkAuth`

* Attaches user (if available) to `req.user` **without enforcing login**.
* Useful for optional access control (like displaying logged-in user's data on `/`).
* Looks in the `Authorization` header → `Bearer <token>`.

---

## 📦 Controllers

### `controllers/user.js`

Handles:

* Signup → `User.create(...)`
* Login:

  * Validates user.
  * Returns **JWT token**.
  * Optionally sets cookie (commented out for API-based login).

### `controllers/url.js`

Handles:

* URL creation (`POST /`)
* Analytics (`GET /analytics/:shortId`)

---

## 🌐 Routes

### `routes/staticRoutes.js`

* Routes like `/login`, `/signup` → returns HTML via `res.render(...)`.

### `routes/user.js`

* API routes like:

  * `POST /user/` → signup
  * `POST /user/login` → login (Postman/JS fetch)

### `routes/url.js`

* Authenticated routes like:

  * `POST /url` → shorten a URL

---

## 🎨 Views

`views/*.ejs` — render frontend pages like `login`, `signup`, `home`.

---

## 🧪 Why `/login` in browser and `/user/login` in Postman?

| Route         | Purpose                       | Triggered From   | Render/API |
| ------------- | ----------------------------- | ---------------- | ---------- |
| `/login`      | Display login form (HTML)     | Browser          | EJS View   |
| `/user/login` | Login POST API (JSON or form) | JS fetch/Postman | API Route  |

---

## 📝 How to Document on GitHub (README.md)

You can add a `README.md` like this:

````md
# 🔗 URL Shortener with Authentication (Node.js + MongoDB + EJS)

A full-stack URL shortener application with:
- JWT-based stateless authentication
- EJS templating engine for frontend
- MongoDB for persistent storage

---

## 🚀 Features

- Signup / Login system
- JWT authentication (cookie or header-based)
- Shorten any URL
- View total clicks and visit history
- EJS-based UI
- MongoDB-based analytics

---

## 📁 Project Structure

- `/controllers`: Logic for user and URL actions
- `/middlewares`: Auth middleware (JWT/cookies)
- `/models`: Mongoose schemas
- `/routes`: Routes for API and views
- `/views`: EJS templates (login, signup, home)
- `/service/auth.js`: JWT token creation/validation
- `index.js`: Main app setup

---

## 🔐 Auth Explained

- **Stateless Auth**: Uses JWT tokens
- **Login Flow**:
  1. User logs in → `POST /user/login`
  2. Server sends token
  3. Client stores in cookie or header

---

## 🧪 Testing

Use Postman:
- `POST /user/login` → Login and get token
- `POST /url` (with `Authorization: Bearer <token>`) → Shorten URL
- `GET /analytics/:shortId` → See analytics

Or use browser:
- Visit `/login` and `/signup`

---

## 🛠️ Tech Stack

- Node.js
- Express.js
- MongoDB + Mongoose
- EJS
- JWT
- Cookie-parser

---
