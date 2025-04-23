

##  **HTTP Status Code Categories**

| Category | Code Range | Meaning | Examples |
|----------|------------|---------|----------|
| **1xx**  | `100–199`  | *Informational* – Request received, continuing process | `100 Continue`, `101 Switching Protocols` |
| **2xx**  | `200–299`  | *Success* – Request was received, understood, and accepted | `200 OK`, `201 Created`, `204 No Content` |
| **3xx**  | `300–399`  | *Redirection* – Further action must be taken | `301 Moved Permanently`, `302 Found` |
| **4xx**  | `400–499`  | *Client Errors* – Problem with the request | `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found` |
| **5xx**  | `500–599`  | *Server Errors* – Server failed to fulfill a valid request | `500 Internal Server Error`, `502 Bad Gateway`, `503 Service Unavailable` |

---

###  **Important Status Code Examples Used in Your Code:**

| Code | Usage |
|------|-------|
| `200 OK` | Default response for successful GET or PATCH |
| `201 Created` | Used after creating a new resource (`POST`) |
| `400 Bad Request` | If input is invalid (not shown in your code, but good to add!) |
| `404 Not Found` | When user not found in `GET`, `PATCH`, `DELETE` |
| `500 Internal Server Error` | Used when file write fails (`fs.writeFile`) |

---

##  **Using `nodemon` for Auto-Restarting the Server**

###  What is `nodemon`?
- It watches for file changes and **automatically restarts** the Node.js server.
- Saves you from manually stopping and starting the server after edits.

---

###  Install it locally:
```bash
npm install --save-dev nodemon
```

###  Update `package.json` scripts:
```json
"scripts": {
  "start": "nodemon index.js"
}
```

> If you don't want it as dev dependency:  
> `npm install -g nodemon` (global install)  
> Then you can run directly using `nodemon index.js`

---

###  Run the server:
```bash
npm start
```
or if installed globally:
```bash
nodemon index.js
```

---

###  Best Practices Recap

- **Custom headers** → `res.setHeader('X-Custom', 'value')`
- **Use `res.status(code).json({...})`** for clarity.
- **Use `nodemon`** during development, **use `node`** in production.
- **Log requests** using middleware like you're doing (`log.txt`).
- **Avoid duplicate routes** (you had `GET /api/users/:id` twice).


## Example

```js

app.get("/api/users/:id",(req,res)=>{
    const id =Number(req.params.id);
    const user =users.find((user)=>user.id===id);
    if(!user)return res.status(404).json({error:'user not found'});
    return res.json(user);
});

```
