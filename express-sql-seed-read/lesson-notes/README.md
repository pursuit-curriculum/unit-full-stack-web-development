## Recommendation

All the code blocks are here to help guide you and help you if you get stuck.

You will gain the best understanding if you try to type everything out. Even though it takes longer and can feel frustrating, there is much more benefit in trying to do so.

## Getting Started

- Navigate to your Desktop or another convenient folder
- `git status` to make sure you are not already in a `git` repository
- `mkdir pg-bookmarks-api`
- `cd pg-bookmarks-api`
- `touch .gitignore`

```
# .gitignore
node_modules
.env
.DS_Store
```

- `git init`
- `git add -A`
- `git commit -m 'first commit'`
- `touch server.js`
- `npm init -y`
- `touch app.js .env`
- `npm install express dotenv cors`

**Thought question** - Why is it essential to add and commit after setting up the .gitignore?

**Follow-up question** - How would you fix adding and committing folders and files you did not mean to add?

**Review Questions:**

- What did the above steps do? Try to put it in your own words. It's essential to learn to talk about code.

```
# .env
PORT=3003
```

**Review Questions:**

- What does the `.env` file and setup do?

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
  res.send("Welcome to Bookmarks App");
});

// EXPORT
module.exports = app;
```

**Review Questions:**

- What do the `app.js` file and setup do?
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
  console.log(`💻 Listening on port ${PORT} 🔖`);
});
```

Test that your app works: http://localhost:3003

**Review Questions:**

- What do the `server` file and setup do?
- What is `app`? What does it do in this file?

## Bookmarks Controller

Use <kbd>control</kbd> <kbd>shift</kbd> <kbd>`</kbd> to open a new terminal tab so you can continue your work without having to shut your server down (please note that changes to configuration files do require a hard reset of your server).

**Terminal**

- `mkdir controllers`
- `touch controllers/bookmarksController.js`

```js
// controllers/bookmarksController.js
const express = require("express");
const bookmarks = express.Router();

// INDEX
bookmarks.get("/", (req, res) => {
  res.json({ status: "ok" });
});

module.exports = bookmarks;
```

**Review Questions:**

- What URL must one go to to see this message?
- Why doesn't it work yet?
- Why don't we see a 404 message, either?

```js
// app.js
// Bookmarks ROUTES
const bookmarksController = require("./controllers/bookmarksController.js");
app.use("/bookmarks", bookmarksController);

// 404 PAGE
app.get("*", (req, res) => {
  res.status(404).send("Page not found");
});
```

Now try: http://localhost:3003/bookmarks

- Why did we name our route `/bookmarks`? Is there a reason we name our route(s) this way?
- What would happen if we put this code ABOVE the middleware setup?

## Setting up The Database

We need to create a database and table for our bookmarks in Postgres.

We could open up a shell and do it. However, it can be helpful to store our commands for reuse.

- When might we want to reuse them?

> **Note**: Do not name a database and a table the same name, E.g., database `bookmarks` & table `bookmarks` - this will cause errors.

We will call our database `bookmarks_dev` and our table `bookmarks`.

### SQL Files

We will create two files.

- **schema**: which is the representation of your data model and will also contain db/table(s) set up
- **seed**: This is some starter data we can insert into the database
- `mkdir db`
- `touch db/schema.sql`
- `touch db/seed.sql`

```SQL
-- db/schema.sql
DROP DATABASE IF EXISTS bookmarks_dev;
CREATE DATABASE bookmarks_dev;

\c bookmarks_dev;

CREATE TABLE bookmarks (
 id SERIAL PRIMARY KEY,
 name TEXT NOT NULL,
 url TEXT,
 category TEXT,
 is_favorite BOOLEAN
);

```

Run this command:

```
psql -U postgres -f db/schema.sql
```

This line of code says, run the app `psql`, use the `U`ser `postgres` and run the `f`ile `db/schema.sql`.

**Success** should look something like this

![](../assets/success-db-init.png)

<hr />

```SQL
-- db/seed.sql
\c bookmarks_dev;

INSERT INTO bookmarks (name, url, category, is_favorite) VALUES
('MDN', 'https://developer.mozilla.org/en-US/', 'educational', true),
('Apartment Therapy', 'https://www.apartmenttherapy.com', 'inspirational', true),
('DMV', 'https://dmv.ny.gov', 'adulting', true);
```

Run this command

```
psql -U postgres -f db/seed.sql
```

**Success** should look something like this

![](../assets/success-seed.png)

Create npm scripts in the `package.json` file to run the above commands:

```json
 "scripts": {
 "db:init": "psql -U postgres -f db/schema.sql",
 "db:seed": "psql -U postgres -f db/seed.sql"
 },
```

## Adding Postgres/pg-promise

- What does a request and response cycle look like when you include a client, server, and database?

We're going to use an npm package called `pg-promise`.

- What does `pg-promise` do?

Install and configure it.

- `npm install pg-promise`
- `touch db/dbConfig.js`

Why do we want to set up environmental variables instead of hard-coding values?

- By default, what is the port number for Postgres?
- How can you find the port?
- Can you run your server on the same port as Postgres?

```
# .env
PORT=3003
PG_HOST=localhost
PG_PORT=5432
PG_DATABASE=bookmarks_dev
PG_USER=postgres
```

<br />

Begin configuring pg-promise to make a connection within your Express server:

```js
// db/dbConfig.js
const pgp = require("pg-promise")();

module.exports = db;
```

We will pass an object with the necessary information to connect our server with our database. We'll bring in the variables from our `.env` file.

Finally, we must open the connection with `const db = pgp(cn);`

- What is `cn` short for?

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

To add additional db connection status info (the following code is unnecessary to connect the db and run the app.) After a few more steps, you will not see this status message until you open a connection.

```js
db.connect()
  .then((cn) => {
    const { user, host, port, database } = cn.client;
    console.log(
      `Postgres connection established with user:\x1b[33m${user}\x1b[0m, host:\x1b[33m${host}\x1b[0m, port:\x1b[33m${port}\x1b[0m, database:\x1b[33m${database}\x1b[0m`
    );
    cn.done();
  })
  .catch((error) => console.log("database connection error", error));
```

## Querying the Database

We are going to separate our SQL queries from our routes. For organizational purposes, let's make a folder called `queries`.

- `mkdir queries`
- `touch queries/bookmarks.js`

First, let's bring in our connection to the database and immediately export it (so we don't forget to do this later).

```js
// queries/bookmarks.js
const db = require("../db/dbConfig.js");

module.exports = {};
```

Next, let's write our first function, which will have a SQL query.

**IMPORTANT** - this will be an async function. We need to wait for the database's response before we try to return a value.

```js
// queries/bookmarks.js
const db = require("../db/dbConfig.js");

const getAllBookmarks = async () => {};

module.exports = { getAllBookmarks };
```

> **Note**: We are returning an object with `module.exports` because we will return more than one function. Therefore, we will store it in an object.

Next, we want to set up a `try/catch` block so that if we have a problem, we can (likely) get a more informative error.

```js
const getAllBookmarks = async () => {
  try {
  } catch (error) {
    return error;
  }
};
```

Finally, let's add our query.

- What is `db.any()`?
- What are the parameter(s) for `db.any()`?
- Why are you using `db.any()` for the index route?

Be sure to export this function.

```js
// queries/bookmarks.js
const getAllBookmarks = async () => {
  try {
    const allBookmarks = await db.any("SELECT * FROM bookmarks");
    return allBookmarks;
  } catch (error) {
    return error;
  }
};
```

Require `getAllBookmarks` function and update `bookmarks.get()` index route to be `async`.

```js
// controllers/bookmarksController.js
const express = require("express");
const bookmarks = express.Router();
const { getAllBookmarks } = require("../queries/bookmarks");

// INDEX
bookmarks.get("/", async (req, res) => {});

module.exports = bookmarks;
```

Let's create a new variable, `allBookmarks`, an array of bookmark objects. Remember, we must `await` for the value to come back from the database.

Then, we'll send it as JSON to the browser.

```js
// INDEX
bookmarks.get("/", async (req, res) => {
  const allBookmarks = await getAllBookmarks();
  res.json(allBookmarks);
});
```

Let's do a little error handling.

- Why is error handling important?

```js
// INDEX
bookmarks.get("/", async (req, res) => {
  const allBookmarks = await getAllBookmarks();
  if (allBookmarks[0]) {
    res.status(200).json(allBookmarks);
  } else {
    res.status(500).json({ error: "server error" });
  }
});
```

## Test it

How can you test this route?

## Save it

- `git add -A`
- `git commit -m 'index route complete'`.

## Reference build

[Index route](https://github.com/pursuit-curriculum-resources/express-sql-seed-read-demo)
