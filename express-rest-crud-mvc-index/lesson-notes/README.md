# Express CRUD/MVC

## Getting Started

We are going to be building this app over several sessions, so let's make sure we put it on GitHub and commit often!

- navigate to your Desktop or other convenient folder
- `git status` to make sure you are not already in a `git` repository
- `mkdir bookmarks`
- `cd bookmarks`
- `touch server.js`
- `npm init -y` (this will automatically say yes to all the npm default settings - this is fine for tutorials, small test builds, etc.)
- `touch .gitignore` (tell git which files to ignore)
- `touch app.js`
- `touch .env`
- `npm install express dotenv nodemon`
- go into `package.json` and change the start script to say `"start": "nodemon server.js"`

**.gitignore**

```
node_modules
.env
.DS_Store
```

**.env**

```
PORT=3003
```

**app.js**

```js
// DEPENDENCIES
const express = require("express");

// CONFIGURATION
const app = express();

// ROUTES
app.get("/", (req, res) => {
  res.send("Welcome to Bookmarks App");
});

// EXPORT
module.exports = app;
```

**server.js**

```js
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

## Demonstrating the Working Application

[Demo on YouTube](https://youtu.be/-FrpZpGk82c) - We will be working with an app called Postman in order to be able to make all of our requests. By the end of this multi-day build, your app will allow you to:

- See a list (array) of all the bookmarks (**R**ead: `index`)
- See a single bookmark (**R**ead: `show`)
- **C**reate a new bookmark
- **U**pdate a bookmark
- **D**elete a bookmark

## Set Up Models

We're just going to have one model, with four values:

- name: string
- url: string
- isFavorite: boolean
- category: string

We can open a new tab in terminal, so we can keep nodemon running while we add these extra files: <kbd>command</kbd> <kbd>t</kbd>.

- `ls` - confirm you are in the same directory as `package.json`
- `mkdir models`
- `touch models/bookmark.js`

and then copy paste this into

**bookmark.js**

```js
module.exports = [
  {
    name: "MDN",
    url: "https://developer.mozilla.org/en-US/",
    isFavorite: true,
    category: "educational",
  },
  {
    name: "Apartment Therapy",
    url: "https://www.apartmenttherapy.com",
    isFavorite: true,
    category: "inspirational",
  },
  {
    name: "DMV",
    url: "https://dmv.ny.gov",
    isFavorite: false,
    category: "adulting",
  },
];
```

**Thought question:** What does `module.exports` do? What happens if we forget to add it? What kind of error will we get?

## Set up Controllers

With our simple app, making folders with single files seems a bit over-engineered. However, it will be worth it, because our goal is to build good organizational habits.

Our controllers are going to handle the routes. It's quite common to make a separate file and group similar controllers together. Express makes it easy to set it up.

All of our routes related to our bookmarks are going to be starting with `/bookmarks`. We are going to name our `express.Router()` variable `bookmarks` to help us remember that this is base of the route. Let's code to understand it better:

- `ls` - confirm you are in the same directory as `package.json`
- `mkdir controllers`
- `touch controllers/bookmarksController.js`

**controllers/bookmarksController.js**

```js
const express = require("express");
const bookmarks = express.Router();
const bookmarksArray = require("../models/bookmark.js");

bookmarks.get("/", (req, res) => {
  res.json(bookmarksArray);
});

module.exports = bookmarks;
```

We are using `res.json` instead of `res.send`, since we are sending `json` instead of a simple string.

In `app.js` we have to connect our controllers for `bookmarks`.

First, we'll require the file.

Then we'll use `/bookmarks` as the base of the routes, like so:

**app.js**

```js
const bookmarksController = require("./controllers/bookmarksController.js");
app.use("/bookmarks", bookmarksController);
```

Now, we should be able to go to http://localhost:3003/bookmarks and see our JSON

## Error Handling

Let's add a 404 route. It should be below all the other routes.

**app.js**

```js
// 404 PAGE
app.get("*", (req, res) => {
  res.json({ error: "Page not found" });
});
```

Test it http://localhost:3003/nothing_here

When we open up our Chrome Dev Tools and go to the `Network` tab, we can see that this file loads with an http status of [304](https://http.cat/304) or [200](https://http.cat/200)

But because this is a 404 [page not found](https://http.cat/404), we should make sure we are sending through the correct status code.

```js
// 404 PAGE
app.get("*", (req, res) => {
  res.status(404).json({ error: "Page not found" });
});
```
