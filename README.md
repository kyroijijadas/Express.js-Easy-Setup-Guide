# Express.js Easy Setup Guide (VS Code Terminal)

A step-by-step guide to build and run your first **Express.js** server using only the **VS Code terminal**. Follow the steps in order. At the end of Part 1, your server will be running successfully.

## Table of Contents

- [Before You Start](#before-you-start)
- [Part 1: Get Your Server Running](#part-1-get-your-server-running)
  - [Step 1: Open the VS Code Terminal](#step-1-open-the-vs-code-terminal)
  - [Step 2: Check Node.js](#step-2-check-nodejs)
  - [Step 3: Create Your Project Folder](#step-3-create-your-project-folder)
  - [Step 4: Create package.json](#step-4-create-packagejson)
  - [Step 5: Install the Packages](#step-5-install-the-packages)
  - [Step 6: Create Your Files](#step-6-create-your-files)
  - [Step 7: Write the Server Code](#step-7-write-the-server-code)
  - [Step 8: Add the Run Scripts](#step-8-add-the-run-scripts)
  - [Step 9: Run the Server](#step-9-run-the-server)
  - [Step 10: Test the Server](#step-10-test-the-server)
  - [Step 11: Stop the Server](#step-11-stop-the-server)
- [Part 2: Add Routes (Users API)](#part-2-add-routes-users-api)
- [Part 3: Connect to MySQL (Optional)](#part-3-connect-to-mysql-optional)
- [Part 4: Run It With Docker](#part-4-run-it-with-docker)
- [Troubleshooting](#troubleshooting)

---

## Before You Start

You need two things installed:

| Tool | Download |
| --- | --- |
| **VS Code** | [code.visualstudio.com](https://code.visualstudio.com/) |
| **Node.js (LTS version)** | [nodejs.org](https://nodejs.org/) |

When installing Node.js, keep clicking **Next** with the default options. **Close and reopen VS Code** after the install finishes.

> **Note:** The commands in this guide are written for the default VS Code terminal on Windows (**PowerShell**). Your terminal's name is shown at the top right of the terminal panel.

---

# Part 1: Get Your Server Running

## Step 1: Open the VS Code Terminal

Open VS Code, then open the terminal with this shortcut:

```text
Ctrl + `
```

Or use the menu: **Terminal** then **New Terminal**.

Useful terminal tips:

| Action | How |
| --- | --- |
| Open a second terminal | Click the **+** icon at the top right of the terminal panel |
| Clear the screen | Type `cls` and press Enter |
| Stop a running program | Press `Ctrl + C` |
| Paste a command | Right-click inside the terminal, or press `Ctrl + V` |

## Step 2: Check Node.js

Each command should print a version number. If you see a version, Node.js is ready:

```bash
node --version
```

```bash
npm --version
```

> **Seeing an error?** Close VS Code completely, reopen it, and try again. If it still fails, reinstall Node.js.

## Step 3: Create Your Project Folder

Go to your Documents folder:

```bash
cd $HOME\Documents
```

Create the project folder and go inside it:

```bash
mkdir my-express-app
cd my-express-app
```

Open this folder in VS Code:

```bash
code .
```

A new VS Code window opens with your folder. **Use that new window from now on.** Open the terminal again with `` Ctrl + ` ``.

Check that you are inside the right folder. The path should end with `my-express-app`:

```bash
pwd
```

> **If `code .` doesn't work:** In VS Code, click **File**, then **Open Folder**, and choose the `my-express-app` folder.

## Step 4: Create package.json

This file keeps track of your project name, scripts, and installed packages:

```bash
npm init -y
```

## Step 5: Install the Packages

Install Express and the helper packages:

```bash
npm install express dotenv cors
```

Install nodemon as a dev dependency (it restarts your server when you save a file):

```bash
npm install --save-dev nodemon
```

| Package | What it does |
| --- | --- |
| `express` | The web framework that handles routes and requests |
| `dotenv` | Loads secret settings from a `.env` file |
| `cors` | Lets your frontend (for example React) talk to your backend |
| `nodemon` | Restarts your server automatically when you save a file |

## Step 6: Create Your Files

Create all three files with one command:

```bash
New-Item server.js, .env, .gitignore -ItemType File
```

You should now see `server.js`, `.env`, and `.gitignore` in the VS Code file list on the left.

> **Not using PowerShell?** If your terminal says `cmd`, use `type nul > server.js` for each file. If it says `bash`, use `touch server.js .env .gitignore`.

## Step 7: Write the Server Code

Click each file in the VS Code file list on the left, and paste the content below.

### server.js

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

### .env

```env
PORT=3000
```

### .gitignore

```gitignore
# Hide the giant folder of downloaded packages
node_modules/

# Hide secret passwords and database usernames
.env
```

Press `Ctrl + S` on each file to save it.

## Step 8: Add the Run Scripts

Open `package.json` in VS Code. Find the `"scripts"` section and replace it with this:

```json
"scripts": {
  "dev": "nodemon server.js",
  "start": "node server.js"
},
```

Your `package.json` should look similar to this (the version numbers may be different):

```json
{
  "name": "my-express-app",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "dev": "nodemon server.js",
    "start": "node server.js"
  },
  "dependencies": {
    "cors": "^2.8.5",
    "dotenv": "^16.4.5",
    "express": "^5.0.0"
  },
  "devDependencies": {
    "nodemon": "^3.1.0"
  }
}
```

> **Important:** Don't change the `dependencies` and `devDependencies` that npm created for you. Only replace the `"scripts"` part and make sure `"main"` is `"server.js"`. Save with `Ctrl + S`.

## Step 9: Run the Server

Start the server in development mode:

```bash
npm run dev
```

You should see this:

```text
[nodemon] starting `node server.js`
Server running on http://localhost:3000
```

**Your server is running.** Leave this terminal open. If you close it, the server stops.

## Step 10: Test the Server

### Test in your browser

Open these links:

- `http://localhost:3000` should show `Express server is running!`
- `http://localhost:3000/api/health` should show `{"status":"ok"}`

### Test in a second terminal

Click the **+** icon in the terminal panel to open a second terminal, then run:

```bash
Invoke-RestMethod http://localhost:3000/api/health
```

You should see:

```text
status
------
ok
```

### Test auto-restart

With the server still running, open `server.js`, change the text `Express server is running!` to `Hello from Express!`, and press `Ctrl + S`. In the first terminal you will see:

```text
[nodemon] restarting due to changes...
[nodemon] starting `node server.js`
Server running on http://localhost:3000
```

Refresh `http://localhost:3000` in your browser to see the new text.

## Step 11: Stop the Server

Click inside the terminal that is running the server and press:

```text
Ctrl + C
```

If it asks `Terminate batch job (Y/N)?`, type `Y` and press Enter.

Run the server again anytime with:

```bash
npm run dev
```

Run it in normal (production) mode, without auto-restart:

```bash
npm start
```

## Part 1 Checklist

- [ ] `node --version` shows a version number
- [ ] `npm run dev` shows `Server running on http://localhost:3000`
- [ ] The browser shows `Express server is running!`
- [ ] Saving `server.js` makes nodemon restart by itself

If all four are checked, your Express server is set up successfully.

---

# Part 2: Add Routes (Users API)

When your project grows, split your code into folders instead of putting everything in `server.js`.

Create the folders:

```bash
mkdir routes, controllers
```

Create the files:

```bash
New-Item routes\users.routes.js, controllers\users.controller.js -ItemType File
```

Your project now looks like this:

```text
my-express-app/
├── controllers/
│   └── users.controller.js
├── routes/
│   └── users.routes.js
├── .env
├── .gitignore
├── package.json
└── server.js
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

Add this line below the other `require` lines at the top:

```js
const usersRoutes = require("./routes/users.routes");
```

Add this line below `app.use(express.json());`:

```js
app.use("/api/users", usersRoutes);
```

Save the file. nodemon restarts the server for you. Open a second terminal and test:

Get all users:

```bash
Invoke-RestMethod http://localhost:3000/api/users
```

Get one user:

```bash
Invoke-RestMethod http://localhost:3000/api/users/1
```

Create a user:

```bash
Invoke-RestMethod -Method Post -Uri http://localhost:3000/api/users -ContentType "application/json" -Body '{"name":"Carl"}'
```

Delete a user:

```bash
Invoke-RestMethod -Method Delete -Uri http://localhost:3000/api/users/1
```

---

# Part 3: Connect to MySQL (Optional)

Install the MySQL package:

```bash
npm install mysql2
```

Create the config folder and file:

```bash
mkdir config
New-Item config\db.js -ItemType File
```

### config/db.js

```js
const mysql = require("mysql2/promise");

const pool = mysql.createPool({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,
  waitForConnections: true,
  connectionLimit: 10,
});

module.exports = pool;
```

### Add the database settings to .env

```env
PORT=3000
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=change_this_password
DB_NAME=mydatabase
```

> **Using Docker?** Inside Docker, set `DB_HOST=db` (the service name of your database) instead of `localhost`.

### Create a table

Run this SQL in MySQL (Workbench, phpMyAdmin, or the MySQL command line):

```sql
CREATE TABLE IF NOT EXISTS users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL
);
```

### Get users from the database

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

### Check the connection on startup

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

# Part 4: Run It With Docker

Once your Express app works, you can run it in a container. Create the files:

```bash
New-Item Dockerfile, .dockerignore -ItemType File
```

### Dockerfile

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "run", "dev"]
```

### .dockerignore

```text
node_modules
npm-debug.log
.env
.git
```

Build and run it (Docker Desktop must be running):

```bash
docker build -t my-express-app .
```

```bash
docker run -p 3000:3000 --env-file .env my-express-app
```

> **Using Docker on Windows?** Change your `dev` script to `nodemon -L server.js` so file changes are detected inside the container.

For the full setup with MySQL, `docker-compose.yml`, and live-sync, follow the [Docker & Node.js Easy Setup Guide](https://github.com/kyroijijadas/docker-nodejs-setup-guide).

---

# Troubleshooting

| Problem | Fix |
| --- | --- |
| `'node' is not recognized` | Close VS Code completely and reopen it. If it still fails, reinstall Node.js. |
| `running scripts is disabled on this system` | PowerShell is blocking npm. Run the command below this table, then try again. |
| `code .` doesn't work | In VS Code, click **File**, then **Open Folder**, and choose your project folder. |
| `Cannot find module 'express'` | Make sure you are in the project folder, then run `npm install`. |
| `Missing script: "dev"` | Your `package.json` doesn't have the scripts from Step 8, or you are in the wrong folder. |
| `EADDRINUSE: address already in use :::3000` | Another program is using port 3000. Stop it (see below), or change `PORT` in `.env`. |
| `Cannot GET /something` | That route doesn't exist. Check the URL and your route files. |
| `req.body` is `undefined` | Add `app.use(express.json());` **above** your routes. |
| Frontend gets a CORS error | Make sure `app.use(cors());` is in `server.js`. |
| `.env` values are `undefined` | Make sure `require("dotenv").config();` is the first line of `server.js`, and the file is named exactly `.env`. |
| `ECONNREFUSED` when connecting to MySQL | MySQL isn't running, or `DB_HOST` is wrong. Use `localhost` on your computer and `db` inside Docker. |

Fix for `running scripts is disabled on this system` (run once, then close and reopen the terminal):

```bash
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```

Find which program is using port 3000:

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
