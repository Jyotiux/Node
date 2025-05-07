URL shortener app using **Node.js**, **Express**, **MongoDB (Mongoose)**, and **shortid**

---



### Features:

1. POST `/url`: Generates a short URL for a given `redirectURL`.
2. GET `/:shortId`: Redirects to the original URL and tracks click timestamps.
3. GET `/url/analytics/:shortId`: Shows total clicks and timestamp history.

---

## 📁 Project Structure

```
project/
│
├── controllers/
│   └── url.js
├── models/
│   └── url.js
├── routes/
│   └── url.js
├── connect.js
├── index.js
└── package.json
```

---

## 🔧 Fixed & Explained Code

### 1. **connect.js**

```js
const mongoose = require("mongoose");

async function connectToMongoDB(url) {
    return mongoose.connect(url, {
        useNewUrlParser: true,
        useUnifiedTopology: true
    });
}

module.exports = { connectToMongoDB };
```

---

### 2. **models/url.js**

```js
const mongoose = require("mongoose");

const urlSchema = new mongoose.Schema({
    shortId: {
        type: String,
        required: true,
        unique: true
    },
    redirectURL: {
        type: String,
        required: true
    },
    visitHistory: [
        { timestamp: { type: Date, default: Date.now } }
    ]
});

const URL = mongoose.model("URL", urlSchema); // Model name must match usage
module.exports = URL;
```

---

### 3. **controllers/url.js**

```js
const shortid = require("shortid");
const URL = require("../models/url");

async function handleGenerateNewShortURL(req, res) {
    const body = req.body;
    if (!body.url) return res.status(400).json({ error: 'URL is required' });

    const shortID = shortid.generate();
    await URL.create({
        shortId: shortID,
        redirectURL: body.url,
        visitHistory: []
    });

    return res.json({ id: shortID });
}

async function handleGetAnalytics(req, res) {
    const shortId = req.params.shortId;
    const result = await URL.findOne({ shortId });

    if (!result) return res.status(404).json({ error: "Not found" });

    return res.json({
        totalClicks: result.visitHistory.length,
        analytics: result.visitHistory
    });
}

module.exports = {
    handleGenerateNewShortURL,
    handleGetAnalytics
};
```

---

### 4. **routes/url.js**

```js
const express = require('express');
const {
    handleGenerateNewShortURL,
    handleGetAnalytics
} = require("../controllers/url");

const router = express.Router();

router.post('/', handleGenerateNewShortURL);
router.get('/analytics/:shortId', handleGetAnalytics);

module.exports = router;
```

---

### 5. **index.js**

```js
const express = require("express");
const { connectToMongoDB } = require('./connect');
const urlRoute = require("./routes/url");
const URL = require("./models/url");

const app = express();
const PORT = 8001;

connectToMongoDB('mongodb://localhost:27017/short-url')
    .then(() => console.log("MongoDB connected"));

app.use(express.json());

// Routes
app.use("/url", urlRoute);

// Redirection route
app.get('/:shortId', async (req, res) => {
    const shortId = req.params.shortId;
    const entry = await URL.findOneAndUpdate(
        { shortId },
        {
            $push: {
                visitHistory: {
                    timestamp: Date.now()
                }
            }
        },
        { new: true }
    );

    if (!entry) return res.status(404).json({ error: "Short URL not found" });

    const redirectURL = entry.redirectURL.startsWith("http")
        ? entry.redirectURL
        : `https://${entry.redirectURL}`;

    return res.redirect(redirectURL);
});

app.listen(PORT, () => console.log(`Server started at PORT ${PORT}`));
```

---

## ▶️ How to Build and Run It

### Step 1: Install dependencies

```bash
npm init -y
npm install express mongoose shortid
```

### Step 2: Run MongoDB

Make sure MongoDB is running locally on port `27017`.

### Step 3: Start the Server

```bash
node index.js
```

You should see:

```
MongoDB connected
Server started at PORT 8001
```

---

## 📬 How to Test (using Postman)

### 🔹 POST a new URL:

```
POST http://localhost:8001/url
Body: JSON
{
    "url": "www.yahoo.com"
}
```

Response:

```json
{
  "id": "qSs6FsWU9"
}
```

### 🔹 Visit the short URL:

```
GET http://localhost:8001/qSs6FsWU9
```

✅ Redirects to [https://www.yahoo.com](https://www.yahoo.com)
✅ Logs timestamp in `visitHistory`

### 🔹 View analytics:

```
GET http://localhost:8001/url/analytics/qSs6FsWU9
```

Response:

```json
{
  "totalClicks": 3,
  "analytics": [
    { "timestamp": "2025-05-07T12:21:09.937Z" },
    ...
  ]
}
```


