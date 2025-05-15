
## 🔗 ***URL Shortener Project***

This is a URL Shortener web application built using Node.js, Express, MongoDB, and EJS. Users can input a long URL and receive a short link which redirects to the original URL. Each redirect is tracked with a timestamp, and users can view analytics like the number of clicks. The app uses server-side rendering with EJS templates and provides a simple, user-friendly interface.


## ✅ 1. **Project Structure**

```
url-shortener/
│
├── controllers/
│   └── url.js
├── models/
│   └── url.js
├── routes/
│   ├── staticRouter.js
│   └── url.js
├── views/
│   └── home.ejs
├── .gitignore
├── connect.js
├── index.js
├── package.json
```

---

## ✅ 2. **Step-by-Step Setup**

### Step 1: Initialize Project

```bash
mkdir url-shortener
cd url-shortener
npm init -y
```

### Step 2: Install Required Packages

```bash
npm install express mongoose shortid ejs
npm install --save-dev nodemon
```

---

## ✅ 3. **Code Files**

### 🔹 `index.js` (Entry point)

```js
const express = require("express");
const path = require("path");
const { connectToMongoDB } = require('./connect');
const urlRoute = require("./routes/url");
const staticRouter = require("./routes/staticRouter");
const URL = require("./models/url");

const app = express();
const PORT = 8001;

connectToMongoDB('mongodb://localhost:27017/short-url')
    .then(() => console.log("MongoDB connected"));

app.set("view engine", "ejs");
app.set("views", path.resolve("./views"));

app.use(express.json());
app.use(express.urlencoded({ extended: false }));

app.use("/url", urlRoute);
app.use("/", staticRouter);

app.get('/url/:shortId', async (req, res) => {
    const shortId = req.params.shortId;
    const entry = await URL.findOneAndUpdate(
        { shortId },
        { $push: { visitHistory: { timestamp: Date.now() } } }
    );
    if (!entry) return res.status(404).json({ error: "Short URL not found" });

    const redirectURL = entry.redirectURL.startsWith("http")
        ? entry.redirectURL
        : `https://${entry.redirectURL}`;
    res.redirect(redirectURL);
});

app.listen(PORT, () => console.log(`Server running at http://localhost:${PORT}`));
```

---

### 🔹 `connect.js`

```js
const mongoose = require('mongoose');

function connectToMongoDB(url) {
    return mongoose.connect(url);
}

module.exports = { connectToMongoDB };
```

---

### 🔹 `models/url.js`

```js
const mongoose = require('mongoose');

const urlSchema = new mongoose.Schema({
    shortId: { type: String, required: true, unique: true },
    redirectURL: { type: String, required: true },
    visitHistory: [{ timestamp: { type: Number } }],
}, { timestamps: true });

const URL = mongoose.model('url', urlSchema);
module.exports = URL;
```

---

### 🔹 `controllers/url.js`

```js
const URL = require("../models/url");
const shortid = require("shortid");

async function handleGenerateNewShortURL(req, res) {
    const { url } = req.body;
    if (!url) return res.status(400).json({ error: "url is required" });

    const shortId = shortid();
    await URL.create({
        shortId,
        redirectURL: url,
        visitHistory: [],
    });

    const allUrls = await URL.find({});
    return res.render("home", {
        id: shortId,
        urls: allUrls,
    });
}

async function handleGetAnalytics(req, res) {
    const shortId = req.params.shortId;
    const result = await URL.findOne({ shortId });
    res.json({
        totalClicks: result.visitHistory.length,
        analytics: result.visitHistory,
    });
}

module.exports = {
    handleGenerateNewShortURL,
    handleGetAnalytics,
};
```

---

### 🔹 `routes/url.js`

```js
const express = require("express");
const { handleGenerateNewShortURL, handleGetAnalytics } = require("../controllers/url");

const router = express.Router();

router.post("/", handleGenerateNewShortURL);
router.get("/analytics/:shortId", handleGetAnalytics);

module.exports = router;
```

---

### 🔹 `routes/staticRouter.js`

```js
const express = require("express");
const URL = require("../models/url");

const router = express.Router();

router.get("/", async (req, res) => {
    const allUrls = await URL.find({});
    res.render("home", {
        urls: allUrls,
    });
});

module.exports = router;
```

---

### 🔹 `views/home.ejs`

```ejs
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>URL Shortener</title>
    <style>
        body {
            font-family: 'Courier New', Courier, monospace;
        }
        table, th, td {
            border: 1px solid black;
            border-collapse: collapse;
            padding: 8px;
        }
    </style>
</head>
<body>
    <h1>URL Shortener</h1>

    <% if (typeof id !== "undefined") { %>
        <p>URL Generated: <a href="/url/<%= id %>">http://localhost:8001/url/<%= id %></a></p>
    <% } %>

    <form action="/url" method="POST">
        <label>Enter Your Original URL</label>
        <input type="text" name="url" placeholder="https://example.com" required />
        <button type="submit">Generate</button>
    </form>

    <% if (urls && urls.length > 0) { %>
        <table>
            <thead>
                <tr>
                    <th>S. No</th>
                    <th>ShortID</th>
                    <th>Redirect</th>
                    <th>Clicks</th>
                </tr>
            </thead>
            <tbody>
                <% urls.forEach(function(url, index) { %>
                    <tr>
                        <td><%= index + 1 %></td>
                        <td><%= url.shortId %></td>
                        <td><%= url.redirectURL %></td>
                        <td><%= url.visitHistory.length %></td>
                    </tr>
                <% }); %>
            </tbody>
        </table>
    <% } %>
</body>
</html>
```

---

### 🔹 `package.json`

Already provided by you, just ensure scripts section has:

```json
"scripts": {
  "start": "nodemon index.js"
}
```

---


## ✅ 4. **Run the Project**

Then run your app:

```bash
npm start
```

Visit: `http://localhost:8001`

