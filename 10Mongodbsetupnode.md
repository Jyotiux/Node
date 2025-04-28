 **MongoDB setup** is done mainly through **these parts**:

---

### 1. **Import mongoose**
```javascript
const mongoose = require("mongoose");
```

---

### 2. **Connect to MongoDB database**
```javascript
mongoose
    .connect("mongodb://localhost:27017/youtubedb")
    .then(() => console.log("Mongoose Connected"))
    .catch((err) => console.log("Mongo Error", err));
```
- Here, you are connecting to a **local MongoDB server**.
- `youtubedb` is the **database name** (MongoDB will create it automatically if it doesn't exist).

---

### 3. **Define a Mongoose Schema**
```javascript
const userSchema = new mongoose.Schema({
    first_name: {
        type: String,
        required: true
    },
    last_name: {
        type: String,
    },
    email: {
        type: String,
        required: true,
        unique: true,
    },
    jobTitle: {
        type: String,
    },
    gender: {
        type: String,
    },
});
```
- `userSchema` defines **what fields your MongoDB documents will have** inside the `user` collection.

---

### 4. **Create a Mongoose Model**
```javascript
const User = mongoose.model("user", userSchema);
```
- Now, `User` is your **MongoDB collection model**.
- You can now use it to **create**, **read**, **update**, and **delete** users in MongoDB.

---

### 5. **Save Data into MongoDB**
```javascript
app.post("/api/users", async (req, res) => {
    const body = req.body;
    if (
        !body ||
        !body.first_name ||
        !body.last_name ||
        !body.email ||
        !body.gender ||
        !body.job_title
    ) {
        return res.status(400).json({ msg: "All fields are required" });
    }
    
    const result = await User.create({
        first_name: body.first_name,
        last_name: body.last_name,
        email: body.email,
        gender: body.gender,
        job_title: body.job_title,
    });

    console.log("result", result);
    return res.status(201).json({ msg: "success" });
});
```
- Here, you are **taking input** from Postman (or frontend) and **saving it to MongoDB** using `User.create()`.

---

#  Summary:
| Step | Code | Purpose |
|:----|:------|:--------|
| 1 | `const mongoose = require('mongoose')` | Load mongoose |
| 2 | `mongoose.connect(...)` | Connect to MongoDB |
| 3 | `new mongoose.Schema({})` | Define how user data will look |
| 4 | `mongoose.model('user', userSchema)` | Create model to interact |
| 5 | `User.create({...})` | Insert new documents into database |

---



```js
const express = require('express');
const users= require('./MOCK_DATA.json');
const path = require("path");
const fs=require('fs');
const mongoose = require("mongoose");
const { type } = require('os');
const app =express();
const PORT = 8000;

mongoose
    .connect("mongodb://localhost:27017/youtubedb")
    .then(() => console.log("Mongoose Connected"))
    .catch((err) => console.log("Mongo Error",err));  
    
// Schema
const userSchema = new mongoose.Schema({
    first_name:{
        type: String,
        required: true
    },
    last_name:{
        type: String,
    },
    email: {
        type: String,
        required: true,
        unique: true,
    },
    jobTitle: {
        type:String,
    },
    gender:{
        type: String,
    },
});

const User = mongoose.model("user",userSchema);

// middleware plugin
app.use(express.urlencoded({extended: false}));

// Middlewares
app.use((req,res,next)=>{
    console.log("Hello from middleware 1");
    // return res.json({msgs:"Hello from middleware 1"});
    fs.appendFile("log.txt",
        `${Date.now()}: ${req.method}: ${req.path}\n`,
        (err,data)=>{
            next();
        }
    );
   });
   
// If we keep till console.log then postman gets stuck in get request also

app.use((req,res,next)=>{
    console.log("Hello from middleware 2");
    next();
})



// Routes
app.get("/api/users",(req,res)=>{
    res.setHeader("X-MyName","Jyoti");
    // Good practice to add custom header with X.
    return res.json(users);
});

// app.get("/users",(req,res)=>{
//     const html=`
//     <ul>
//     ${users.map((user)=>`<li>${user.first_name}</li>`).join("")}
//     </ul>
//     `;
//     res.send(html);
// });

app.get("/api/users/:id",(req,res)=>{
    const id =Number(req.params.id);
    const user =users.find((user)=>user.id===id);
    return res.json(user);
});

// app.post("/api/users",(req,res)=>{
//     //Create new user
//     return res.json({status:"pending"});
// });


app.patch("/api/users/:id", (req, res) => {
    const id = Number(req.params.id);
    const body = req.body;

    // Find index of the user to update
    const userIndex = users.findIndex((user) => user.id === id);

    if (userIndex === -1) {
        return res.status(404).json({ error: "User not found" });
    }

    // Update user fields with new data
    users[userIndex] = { ...users[userIndex], ...body };

    // Write updated users array to file
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


// app.patch("/api/users/:id",(req,res)=>{
//     //Edit the user with id
//     fs.writeFile("./MOCK_DATA.json",JSON.stringify(users),(err, data)=>{
//         return res.json({status:"pending", user: body});
//     })
//     return res.json({status:"pending"});
// });

app.delete("/api/users/:id", (req, res) => {
    const id = Number(req.params.id);

    // Find index of the user to delete
    const userIndex = users.findIndex(user => user.id === id);

    if (userIndex === -1) {
        return res.status(404).json({ error: "User not found" });
    }

    // Remove user from array
    const deletedUser = users.splice(userIndex, 1);

    // Write updated users array to file
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

app.get("/api/users/:id",(req,res)=>{
    const id =Number(req.params.id);
    const user =users.find((user)=>user.id===id);
    if(!user)return res.status(404).json({error:'user not found'});
    return res.json(user);
});

app.post("/api/users",async(req,res)=>{
    const body = req.body;
    if(
        !body ||
        !body.first_name ||
        !body.last_name ||
        !body.email ||
        !body.gender ||
        !body.job_title
    ){
        return res.status(400).json({msg:"All fields are required"});
    }
    // users.push({...body, id: users.length+1});
    // fs.writeFile("./MOCK_DATA.json",JSON.stringify(users),(err, data)=>{
    //     return res.status(201).json({status:"pending", user: body});
    // })
    // console.log("Body",body);
    // return res.json({ status: "User received", user: body });
    const result = await User.create({
        first_name: body.first_name,
        last_name: body.last_name,
        email: body.email,
        gender: body.gender,
        job_title: body.job_title,
    });
    console.log("result",result);
    return res.status(201).json({msg:"success"});
});

// this gives 201 as response status, created

app.listen(PORT,()=>console.log(`Server started at Port: ${PORT}`));
```
