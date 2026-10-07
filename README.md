# Express.js Easy Setup Guide

A beginner-friendly guide that takes you from **installing Node.js** to running your own **Express.js** server, using the terminal (CMD) step by step. At the end, you can connect it to MySQL and run it in Docker.

## Table of Contents

- [1. Install Node.js](#1-install-nodejs)
- [2. Open Your Terminal](#2-open-your-terminal)
- [3. Create Your Project](#3-create-your-project)
- [4. Install Express and Other Packages](#4-install-express-and-other-packages)
- [5. Create Your First Server](#5-create-your-first-server)
- [6. Run and Test the Server](#6-run-and-test-the-server)
- [7. Auto-Restart on Save (nodemon)](#7-auto-restart-on-save-nodemon)
- [8. Organize Your Code (Routes and Controllers)](#8-organize-your-code-routes-and-controllers)
- [9. Use Environment Variables (.env)](#9-use-environment-variables-env)
- [10. Connect to MySQL (Optional)](#10-connect-to-mysql-optional)
- [11. What NOT to Put on GitHub](#11-what-not-to-put-on-github)
- [12. Run It With Docker](#12-run-it-with-docker)
- [13. Troubleshooting](#13-troubleshooting)

---

## 1. Install Node.js

Express runs on **Node.js**, so install that first.

1. Go to [nodejs.org](https://nodejs.org/)
2. Download the **LTS** version (the stable one)
3. Run the installer and keep clicking **Next** with the default options
4. Restart your terminal after the install finishes

Check that it worked. Each command should print a version number:

```bash
node --version
```

```bash
npm --version
```

> **What is npm?** npm (Node Package Manager) comes with Node.js. It downloads and manages packages like Express.

---

## 2. Open Your Terminal

You can use any of these:

| Terminal | How to open it |
| --- | --- |
| **Command Prompt (CMD)** | Press `Win + R`, type `cmd`, press Enter |
| **PowerShell / Windows Terminal** | Press `Win + X`, then choose *Terminal* |
| **VS Code Terminal** | Open your project in VS Code, then press `` Ctrl + ` `` |

### Basic terminal commands you will need

Show where you are right now:

```bash
cd
```

List the files in the current folder:

```bash
dir
```

Go into a folder:

```bash
cd my-project
```

Go back one folder:

```bash
cd ..
```

Create a new folder:

```bash
mkdir my-project
```

Create an empty file (CMD):

```bash
type nul > server.js
```

Clear the screen:

```bash
cls
```

Stop a running program (for example, your server):

```text
Ctrl + C
```

---

## 3. Create Your Project

Create the project folder and go inside it:

```bash
mkdir my-express-app
cd my-express-app
```

Create the `package.json` file. This file keeps track of your project name, scripts, and installed packages:

```bash
npm init -y
```

Open the folder in VS Code:

```bash
code .
```

---

## 4. Install Express and Other Packages

Install Express:

```bash
npm install express
```

Install the common helper packages:

```bash
npm install dotenv cors
```

Install nodemon as a **dev dependency** (it is only used while you code):

```bash
npm install --save-dev nodemon
```

| Package | What it does |
| --- | --- |
| `express` | The web framework that handles routes and requests |
| `dotenv` | Loads secret settings from a `.env` file |
| `cors` | Lets your frontend (for example React) talk to your backend |
| `nodemon` | Restarts your server automatically when you save a file |

See what is installed:

```bash
npm list --depth=0
```

---

## 5. Create Your First Server

Create the file:

```bash
type nul > server.js
```

Open `server.js` and paste this:

```js
require("dotenv").config();
const express = require("express");
const cors = require("cors");

const app = express();
const PORT = process.env.PORT || 3000;

// Middleware
app.use(cors());
app.use(express.json());

// Routes
app.get("/", (req, res) => {
  res.send("Express server is running!");
});

app.get("/api/health", (req, res) => {
  res.json({ status: "ok" });
});

// 404 handler (when no route matches)
app.use((req, res) => {
  res.status(404).json({ message: "Route not found" });
});

// Error handler
app.use((err, req, res, next) => {
  console.error(err);
  res.status(500).json({ message: "Something went wrong" });
});

app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

Open `package.json` and set up the scripts:

```json
"scripts": {
  "dev": "nodemon server.js",
  "start": "node server.js"
}
```

---

## 6. Run and Test the Server

Start the server:

```bash
node server.js
```

You should see:

```text
Server running on http://localhost:3000
```

Test it in your browser by opening these links:

- `http://localhost:3000`
- `http://localhost:3000/api/health`

Or test it from a second terminal window:

```bash
curl http://localhost:3000/api/health
```

Press `Ctrl + C` in the first terminal to stop the server.

---

## 7. Auto-Restart on Save (nodemon)

Without nodemon, you have to stop and start the server every time you change your code. With nodemon, it restarts by itself when you press **Save**.

Run your server in development mode:

```bash
npm run dev
```

Now change something in `server.js` and save. You will see:

```text
[nodemon] restarting due to changes...
[nodemon] starting `node server.js`
```

Run your server in normal (production) mode:

```bash
npm start
```

> **Using Docker on Windows?** Use `nodemon -L server.js` in your `dev` script so file changes are detected inside the container. See the [Docker guide](https://github.com/kyroijijadas/docker-nodejs-setup-guide).

---

## 8. Organize Your Code (Routes and Controllers)

When your project grows, don't put everything in `server.js`. Split it into folders:

```text
my-express-app/
├── config/
│   └── db.js
├── controllers/
│   └── users.controller.js
├── routes/
│   └── users.routes.js
├── .env
├── .gitignore
├── package.json
└── server.js
```

Create the folders:

```bash
mkdir config controllers routes
```

Create the files:

```bash
type nul > routes\users.routes.js
type nul > controllers\users.controller.js
```

### controllers/users.controller.js

This example uses a simple array instead of a database, so you can test right away:

```js
let users = [
  { id: 1, name: "Ana" },
  { id: 2, name: "Ben" },
];

// GET all users
exports.getUsers = (req, res) => {
  res.json(users);
};

// GET one user
exports.getUserById = (req, res) => {
  const user = users.find((u) => u.id === Number(req.params.id));
  if (!user) {
    return res.status(404).json({ message: "User not found" });
  }
  res.json(user);
};

// POST a new user
exports.createUser = (req, res) => {
  const { name } = req.body;
  if (!name) {
    return res.status(400).json({ message: "Name is required" });
  }
  const newUser = { id: users.length + 1, name };
  users.push(newUser);
  res.status(201).json(newUser);
};

// DELETE a user
exports.deleteUser = (req, res) => {
  users = users.filter((u) => u.id !== Number(req.params.id));
  res.json({ message: "User deleted" });
};
```

### routes/users.routes.js

```js
const express = require("express");
const router = express.Router();
const controller = require("../controllers/users.controller");

router.get("/", controller.getUsers);
router.get("/:id", controller.getUserById);
router.post("/", controller.createUser);
router.delete("/:id", controller.deleteUser);

module.exports = router;
```

### Connect the routes in server.js

Add this line near the top, below the other `require` lines:

```js
const usersRoutes = require("./routes/users.routes");
```

Add this line below `app.use(express.json());`, before the 404 handler:

```js
app.use("/api/users", usersRoutes);
```

### Test your routes

Get all users:

```bash
curl http://localhost:3000/api/users
```

Get one user:

```bash
curl http://localhost:3000/api/users/1
```

Create a user (CMD needs the quotes escaped like this):

```bash
curl -X POST http://localhost:3000/api/users -H "Content-Type: application/json" -d "{\"name\":\"Carl\"}"
```

Delete a user:

```bash
curl -X DELETE http://localhost:3000/api/users/1
```

---

## 9. Use Environment Variables (.env)

Never write passwords directly in your code. Put them in a `.env` file instead.

Create the file:

```bash
type nul > .env
```

Add this inside:

```env
PORT=3000
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=change_this_password
DB_NAME=mydatabase
```

Your code reads them with `process.env`:

```js
const PORT = process.env.PORT || 3000;
```

Make sure `require("dotenv").config();` is the **first line** of `server.js`.

> **Using Docker?** Inside Docker, set `DB_HOST=db` (the service name of your database) instead of `localhost`.

---

## 10. Connect to MySQL (Optional)

Install the MySQL package:

```bash
npm install mysql2
```

Create the file:

```bash
type nul > config\db.js
```

### config/db.js

```js
const mysql = require("mysql2/promise");

const pool = mysql.createPool({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD || process.env.DB_ROOT_PASSWORD,
  database: process.env.DB_NAME,
  waitForConnections: true,
  connectionLimit: 10,
});

module.exports = pool;
```

### Create a table

Run this SQL in MySQL (Workbench, phpMyAdmin, or the MySQL command line):

```sql
CREATE TABLE IF NOT EXISTS users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL
);
```

### Example: get users from the database

Replace `getUsers` in `controllers/users.controller.js` with this:

```js
const db = require("../config/db");

exports.getUsers = async (req, res, next) => {
  try {
    const [rows] = await db.query("SELECT * FROM users");
    res.json(rows);
  } catch (err) {
    next(err);
  }
};
```

### Test the connection on startup

Add this to `server.js`, above `app.listen`:

```js
const db = require("./config/db");

db.query("SELECT 1")
  .then(() => console.log("Database connected"))
  .catch((err) => console.error("Database connection failed:", err.message));
```

> **Important:** Always use placeholders (`?`) when you put user input into a query, so nobody can attack your database with SQL injection:
>
> ```js
> await db.query("SELECT * FROM users WHERE id = ?", [req.params.id]);
> ```

---

## 11. What NOT to Put on GitHub

Create a file named exactly `.gitignore`:

```bash
type nul > .gitignore
```

Put this inside:

```gitignore
# Hide the giant folder of downloaded packages
node_modules/

# Hide secret passwords and database usernames
.env
```

Anyone who downloads your project can recreate `node_modules` by running:

```bash
npm install
```

Already pushed `.env` or `node_modules` by mistake? Remove them from Git tracking (this keeps the files on your computer):

```bash
git rm -r --cached node_modules
git rm --cached .env
git commit -m "Stop tracking node_modules and .env"
```

> **Note:** If you ever pushed real passwords to GitHub, change them. Deleting the file does not remove it from your Git history.

---

## 12. Run It With Docker

Once your Express app works, you can run it in a container. Create `Dockerfile` in your project folder:

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "run", "dev"]
```

Create `.dockerignore`:

```text
node_modules
npm-debug.log
.env
.git
```

Build and run it:

```bash
docker build -t my-express-app .
```

```bash
docker run -p 3000:3000 --env-file .env my-express-app
```

For the full setup with MySQL, `docker-compose.yml`, and live-sync, follow the [Docker & Node.js Easy Setup Guide](https://github.com/kyroijijadas/docker-nodejs-setup-guide).

---

## 13. Troubleshooting

| Problem | Fix |
| --- | --- |
| `'node' is not recognized` | Node.js isn't installed or the terminal was open during install. Close and reopen your terminal. |
| `'npm' is not recognized` | Same as above. Reinstall Node.js from [nodejs.org](https://nodejs.org/). |
| `Cannot find module 'express'` | Run `npm install` in your project folder. |
| `EADDRINUSE: address already in use :::3000` | Another program is using port 3000. Stop it, or change `PORT` in `.env`. |
| `Cannot GET /something` | That route doesn't exist. Check the URL and your route files. |
| `req.body` is `undefined` | Add `app.use(express.json());` **above** your routes. |
| Frontend gets a CORS error | Add `app.use(cors());` in `server.js` and run `npm install cors`. |
| `.env` values are `undefined` | Make sure `require("dotenv").config();` is the first line of `server.js`, and the file is named exactly `.env`. |
| `ECONNREFUSED` when connecting to MySQL | MySQL isn't running, or `DB_HOST` is wrong. Use `localhost` on your computer and `db` inside Docker. |
| `nodemon` is not recognized | Run `npm install --save-dev nodemon`, then start with `npm run dev` (not `nodemon` directly). |

Find which program is using port 3000 on Windows:

```bash
netstat -ano | findstr :3000
```

Stop that program using its PID (the last number from the command above):

```bash
taskkill /PID 1234 /F
```

---

## License

This project is licensed under the [MIT License](LICENSE).
