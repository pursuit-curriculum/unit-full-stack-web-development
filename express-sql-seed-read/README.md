# Express & SQL w. PG Seed & Read

## Learning Objectives

By the end of this lesson, you should be able to:

- Run SQL files from the terminal using the `psql` command.
- Create and run DDL files using the appropriate process.
- Create and run seed files using the appropriate process.
- Describe the purpose of DDL and seed files.
- Connect a database to a JavaScript application using `pg-promise`.
- Identify a database’s connection URL.

---

## Adding a Database to your Express App

## Rebuild a back-end API

Earlier in this module, you built two apps: a back-end API that serves JSON and front-end that consumes the API.

You will rebuild the backend API in order to review Express and learn how to add a database in.

Sometimes, rebuilding something can seem less exciting than trying something new. However, being able to compare and contrast the differences will help solidify what you have already learned and what parts are new.

## Getting Started

- navigate to your Desktop or another convenient folder
- `git status` to make sure you are not already in a `git` repository
- `mkdir pg-colors-api`
- `cd pg-colors-api`
- `touch .gitignore`

```
# .gitignore
node_modules
.env
.DS_Store
```

- `touch server.js`
- `npm init -y` (this will automatically say yes to all the npm default settings - this is fine for tutorials, small test builds, etc.)
- `touch app.js .env`
- `npm install express dotenv cors`

- `git init`
- `git add -A`
- `git commit -m 'first commit'`

**Thought question** - Why is it essential to add and commit after setting up the .gitignore?

**Follow-up question** - How would you fix adding and committing folders and files (like `node_modules`) you did not mean to add?

**Review Questions:**

- What did the above steps do? Try to put it in your own words. It's essential to learn to talk about code.

```env
# .env
PORT=3003
```

- What are acceptable port numbers?

**Review Questions:**

- What do this file and setup do?

```js
// app.js
// DEPENDENCIES
const cors = require("cors");
const express = require("express");

// CONFIGURATION
const app = express();

// MIDDLEWARE
app.use(cors());
app.use(express.json());

// ROUTES
app.get("/", (req, res) => {
  res.send("Youlcome to Colors App");
});

// EXPORT
module.exports = app;
```

**Review Questions:**

- What do this file and setup do?
- What is middleware?
- What does `app.use(cors())` do?
- What does `app.use(express.json())` set up?
- What does `app.get()` do?
- What is `req` short for?
- What is `res` short for?
- What is `module.exports`? What does it do?

```js
// server.js
// DEPENDENCIES
const app = require("./app.js");

// CONFIGURATION
require("dotenv").config();
const PORT = process.env.PORT;

// LISTEN
app.listen(PORT, () => {
  console.log(`Listening on port ${PORT}`);
});
```

Test that your app works: http://localhost:3003

**Review Questions:**

- What do this file and setup do?
- What is `app`? What does it do?

## Colors Controller

Use <kbd>control</kbd> <kbd>shift</kbd> <kbd>`</kbd> to open a new terminal tab so you can continue your work without having to shut your server down (please note that changes to configuration files do require a hard reset of your server).

**Terminal**

- `mkdir controllers`
- `touch controllers/colorsController.js`

```js
// controllers/colorsController.js
const express = require("express");
const colors = express.Router();

// INDEX
colors.get("/", (req, res) => {
  res.json({ status: "ok" });
});

module.exports = colors;
```

**Review Questions:**

- What is the URL one needs to go to to see this message?
- Why doesn't it work yet?
- Why don't you see a 404 message, either?

```js
// app.js
// Colors ROUTES
const colorsController = require("./controllers/colorsController.js");
app.use("/colors", colorsController);

// 404 PAGE
app.get("*", (req, res) => {
  res.status(404).send("Page not found");
});
```

Now try: http://localhost:3003/colors

Why did you name your route `/colors`? Is there a reason you name your route(s) this way?

What would happen if you put this code ABOVE the middleware setup?

## Setting up The Database

You need to create a database and table for your colors in Postgres.

You could open up a shell and do it. However, it can be helpful to store your commands for reuse.

When might you want to reuse them?

- When collaborating on a group project and you need your partner(s) to have the same setup
- When you deploy your app in the cloud and want to be sure your db/tables are set up the same way
- When you want to test your database with CircleCi or another automated testing
- When you get a new computer and want to set up the project on your new computer

**GOTCHA**: Do not name a database and a table the same name. E.g., database `colors` & table `colors` - this will cause errors.

You will call your database `colors_dev` and your table `colors`.

### SQL Files

You will create two files.

- **schema**: which is the representation of your data model and will also contain db/table(s) set up
- **seed**: This is some starter data you can insert into the database

- `mkdir db`
- `touch db/schema.sql`
- `touch db/seed.sql`

**db/schema.sql**

```sql
DROP DATABASE IF EXISTS colors_dev;
CREATE DATABASE colors_dev;

\c colors_dev;

CREATE TABLE colors (
 id SERIAL PRIMARY KEY,
 name TEXT NOT NULL,
 is_favorite BOOLEAN
);

```

Run this command:

```bash
psql -U postgres -f db/schema.sql
```

This line of code says, run the app `psql`, use the `U`ser `postgres` and run the `f`ile `db/schema.sql`.

**Success** should look something like this

![](./assets/success-db-init.png)

<hr />

**db/seed.sql**

```sql
\c colors_dev;

INSERT INTO colors (name, is_favorite) VALUES
('Orchid',  true),
('Lavender', true),
('Salmon', true);
```

Run this command

```bash
psql -U postgres -f db/seed.sql

```

What does the above command do?

**Success** should look something like this

![](./assets/success-seed.png)

### Reusing the db commands

As you develop your applications, you'll find you need to rerun the Postgres commands over and over again. In one case, because you've made changes to the database (added a column, changed a name, updated a constraint). In the case of the seed data, you may end up wanting to try new test-cases or resetting the data because something went wrong.

Rather than looking up these commands over and over again, you can add them to your `package.json`

```json
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node server.js",
    "db:init": "psql -U postgres -f db/schema.sql",
    "db:seed": "psql -U postgres -f db/seed.sql"
  },
```

To run, make sure your server is not running, then you can type:

- `npm run db:init`
- `npm run db:seed`

> **Note**: When making changes to configurations, be sure to fully shut down your server and start again. Configuration changes are often not tracked/reset with nodemon.

## Adding Postgres/pg-promise

You're going to use an npm package called `pg-promise`; pg-promise will make it simple for you to connect to your Postgres database and allow you to write SQL commands that return JSON to you that you can then send out.

The server will now make requests to the database, and the database will send back a response, very much like the request/response cycle we've already seen between clients and servers.

Requests require a path or URL to know where to go. The first part of adding pg-promise is to configure where the requests to the database should go.

An example URL to a database is:

```
postgres://john:password1234@localhost:5432/colors_dev
\________/\___/\____________/\_______/\___/\_________/
protocol   user   password      host   port sub-database
```

You will configure this URL below.

![](./assets/server-db.png)

- `npm install pg-promise`
- `touch db/dbConfig.js`

Currently, we'll be running your app on your computer, but later, we'll want to deploy it. So you will want to set up your environmental variables. Reminder - this (`.env`) is not a JavaScript file, do not use semi-colons or quotes.

When you installed Postgres, it set up to, by default, run on localhost with a port of 5432. You are going to keep these defaults. You can always check them with the Postgres App.

![](./assets/postgres-config.png)

```
# .env
PORT=3003
PG_HOST=localhost
PG_PORT=5432
PG_DATABASE=colors_dev
PG_USER=postgres
```

<br />

You can go to [the docs](http://vitaly-t.github.io/pg-promise/index.html) and see how complete or modify the set up (we will keep the default configuration and not pass any arguments).

Remember, a database like Postgres is very similar to a server in that it takes requests and makes responses.

```js
// db/dbConfig.js
const pgp = require("pg-promise")();

module.exports = db;
```

Now, you have to set up the connection. You will pass an object with the necessary information to connect your server with your database. You'll bring in the variables from your `.env` file.

You will create a [Connection Object](https://github.com/vitaly-t/pg-promise/wiki/Connection-Syntax#configuration-object) that pg-promise will convert into a URL.

In this case, you'll be using the user `postgres`, which by default, does not have a password. It's important to note, A setup without a specific username and no password is not secure but ok for participating in a lesson or working on a lab since there is no sensitive information being passed around.

When you work for a company, you will have a specific username and password. As an aside, when you start your job as a dev, most companies will set up your computer for you with usernames, password and configurations. This will be handled either by an IT department or senior developer. Therefore, learning how to configure things like Postgres at this stage is less critical than learning to use it to build apps.

```
postgres://postgres@localhost:5432/colors_dev
\________/\________/\_______/\___/\_________/
protocol     user      host   port sub-database
```

- `cn` - is short for connection

```js
const pgp = require("pg-promise")();
require("dotenv").config();

const cn = {
  host: process.env.PG_HOST,
  port: process.env.PG_PORT,
  database: process.env.PG_DATABASE,
  user: process.env.PG_USER,
};

module.exports = db;
```

Finally, you must open the connection with `const db = pgp(cn);`

```js
const pgp = require("pg-promise")();
require("dotenv").config();

const cn = {
  host: process.env.PG_HOST,
  port: process.env.PG_PORT,
  database: process.env.PG_DATABASE,
  user: process.env.PG_USER,
};

const db = pgp(cn);

module.exports = db;
```

If you would like more information about the connection, you can add the following function. This is not required to create the connection, but like the console.log in the `app.listen` function, gives you a status of the

```js
db.connect()
  .then((cn) => {
    const { user, host, port, database } = cn.client;
    console.log(
      "\x1b[90m" +
        `Postgres connection established with user:${user}, host:${host},  port:${port}, database:${database}` +
        "\x1b[0m"
    );
    cn.done();
  })
  .catch((error) => console.log("database connection error", error));
```

> **Note**: You will not see this message until you've made a request to the database. This request will not happen until you've taken a few more steps and called the query function in the controller. Follow the next steps to get there.

## Querying the Database

You are going to separate your SQL queries from your routes. For organizational purposes, let's make a folder called `queries`

- `mkdir queries`
- `touch queries/color.js`

**queries/color.js**

First, let's bring in your connection to the database and immediately export it (so you don't forget to do this later)

```js
const db = require("../db/dbConfig.js");

module.exports = {};
```

Next, let's write your first function, which will have a SQL query.

**IMPORTANT** - this will be an async function. You need to wait for the database's response before you try to return a value.

```js
const db = require("../db/dbConfig.js");

const getAllColors = async () => {};

module.exports = { getAllColors };
```

> **Note**: with `module.exports`, you are returning an object because you will return more than one function. Therefore, you will store it in an object.

Next, you want to set up a `try/catch` block so that if you have a problem, you can (likely) get a more informative error.

```js
const getAllColors = async () => {
  try {
  } catch (error) {
    return error;
  }
};
```

Finally, let's add your query.

`db.any()` is a function that takes a string as a first argument.

[.any()](https://github.com/vitaly-t/pg-promise#methods) means it will accept any return from the database, no rows, one row, or many rows of data.

Be sure to export this function.

```js
const getAllColors = async () => {
  try {
    const allColors = await db.any("SELECT * FROM colors");
    return allColors;
  } catch (error) {
    return error;
  }
};
```

**controllers/colorController.js**

Require `getAllColors()` function and update `colors.get()` index route to be `async`.

```js
const express = require("express");
const colors = express.Router();
const { getAllColors } = require("../queries/color");

// INDEX
colors.get("/", async (req, res) => {});

module.exports = colors;
```

Let's create a new variable, `allColors` which will be an array of color objects. Remember, you must `await` for the value to come back from the database.

Then, we'll send it as JSON to the browser.

```js
// INDEX
colors.get("/", async (req, res) => {
  const allColors = await getAllColors();
  res.json(allColors);
});
```

Let's do a little error handling.

```js
colors.get("/", async (req, res) => {
  const allColors = await getAllColors();
  if (allColors[0]) {
    res.status(200).json(allColors);
  } else {
    res.status(500).json({ error: "server error" });
  }
});
```

## Test it

How can you test this route?

Go to http://localhost:3003/colors

## Save it

- `git add -A`
- `git commit -m 'index route complete'`.

## Reference application

[pre-reading-express-sql-seed-read](https://github.com/pursuit-curriculum-resources/pre-reading-express-sql-seed-read)
