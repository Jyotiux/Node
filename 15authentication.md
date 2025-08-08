
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
