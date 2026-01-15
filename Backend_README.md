
# Node.js Backend Learning Process (Zero → Hero)

## 🎯 Main Goal
This module teaches  **how to build a backend with Node.js, Express, and MySQL**.  
Focus is on **practical skills**, preparing students for **TVET national practical exams** and fullstack projects.

By the end, students will:
- Understand **Node.js backend structure (MVC)**
- Set up a backend project
- Build CRUD APIs for a sample TODO list
- Be able to connect frontend later

---

## 1. Node.js Backend Structure (MVC)

### 1.1 Understanding MVC
**MVC** = Model, View, Controller (in backend we usually use **Model + Controller + Routes**)  

- **Model**: Handles database interactions. Defines tables, fields, and how data is saved or retrieved.  
- **Controller**: Contains logic for handling requests. Decides **what happens when a request comes in**.  
- **Routes**: Define URLs that clients call (e.g., /api/todos). Routes link **requests to controllers**.

**Why this structure?**  
- Keeps code organized and readable  
- Makes debugging easier  
- Helps reuse code in multiple routes  
- Exam-friendly structure

### How Controllers Work
- Controller functions **receive request data**, process it, and **send response** back.  
- Example: Add TODO item, fetch all TODO items, delete item, update item.  

**Routes connect controllers to URLs:**  
```text
POST /api/todos → calls controller.addTodo
GET /api/todos → calls controller.getTodos
```

---

## 1.2 Setting Up the Project

### Step 1: Create Project Folder
```bash
mkdir todo-backend
cd todo-backend
```

### Step 2: Initialize Node Project
```bash
npm init -y
```

### Step 3: Install Required Packages
```bash
npm install express mysql2 cors dotenv
npm install nodemon --save-dev
```

**Explanation of packages:**
- express → server
- mysql2 → connect to MySQL
- cors → allow frontend to call backend
- dotenv → store secrets (like DB password)
- nodemon → auto-restart server on changes

### Step 4: Folder Structure
```text
todo-backend/
├── src/
│   ├── app.js
│   ├── server.js
│   ├── config/
│   │   └── db.js
│   ├── routes/
│   │   └── todo.routes.js
│   └── controllers/
│       └── todo.controller.js
├── .env
├── package.json
```
- `config/db.js` → MySQL connection  
- `controllers/` → contains functions for requests  
- `routes/` → maps URL paths to controllers  

---

## 2. Build Sample Backend for TODO List

### 2.1 Create MySQL Database & Table
```sql
CREATE DATABASE todo_db;
USE todo_db;

CREATE TABLE todos (
  id INT AUTO_INCREMENT PRIMARY KEY,
  task VARCHAR(255),
  status VARCHAR(20)
);
```
- `task` → name of the todo item  
- `status` → pending / done  

### 2.2 Connect Node.js to MySQL (`src/config/db.js`)
```js
const mysql = require("mysql2");

const db = mysql.createConnection({
  host: "localhost",
  user: "root",
  password: "",
  database: "todo_db"
});

db.connect(err => {
  if(err) console.log("Database connection failed", err);
  else console.log("Connected to MySQL");
});

module.exports = db;
```

### 2.3 Setup Server (`src/app.js` & `src/server.js`)
`src/app.js`
```js
const express = require("express");
const cors = require("cors");
const app = express();

app.use(cors());
app.use(express.json());

module.exports = app;
```
`src/server.js`
```js
const app = require("./app");
const PORT = 5000;

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```
Run server:
```bash
npx nodemon src/server.js
```

### 2.4 Create TODO Controller (`src/controllers/todo.controller.js`)
```js
const db = require("../config/db");

exports.getTodos = (req, res) => {
  db.query("SELECT * FROM todos", (err, results) => {
    if(err) return res.status(500).json(err);
    res.json(results);
  });
};

exports.addTodo = (req, res) => {
  const { task, status } = req.body;
  db.query("INSERT INTO todos SET ?", { task, status }, err => {
    if(err) return res.status(500).json(err);
    res.json({ message: "Todo added successfully" });
  });
};

exports.updateTodo = (req, res) => {
  const { id } = req.params;
  const { task, status } = req.body;
  db.query("UPDATE todos SET ? WHERE id = ?", [{ task, status }, id], err => {
    if(err) return res.status(500).json(err);
    res.json({ message: "Todo updated" });
  });
};

exports.deleteTodo = (req, res) => {
  const { id } = req.params;
  db.query("DELETE FROM todos WHERE id = ?", id, err => {
    if(err) return res.status(500).json(err);
    res.json({ message: "Todo deleted" });
  });
};
```

### 2.5 Create Routes (`src/routes/todo.routes.js`)
```js
const express = require("express");
const router = express.Router();
const controller = require("../controllers/todo.controller");

router.get("/", controller.getTodos);
router.post("/", controller.addTodo);
router.put("/:id", controller.updateTodo);
router.delete("/:id", controller.deleteTodo);

module.exports = router;
```

### 2.6 Connect Routes in `app.js`
```js
const todoRoutes = require("./routes/todo.routes");
app.use("/api/todos", todoRoutes);
```

### 2.7 Test API with Postman
- GET /api/todos → list all todos  
- POST /api/todos → add a new todo  
- PUT /api/todos/:id → update a todo  
- DELETE /api/todos/:id → delete a todo  

---

## ✅ Outcome
Students will learn to:
- Understand MVC backend structure
- Set up Node.js + Express project
- Connect to MySQL database
- Build CRUD APIs for a sample TODO list
- Test APIs using Postman
- Be ready to connect frontend later
