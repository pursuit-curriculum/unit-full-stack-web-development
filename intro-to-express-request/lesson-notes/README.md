# Express Request

## Getting Started

### Quick Review

- Express is a code framework hosted on npm and written in JavaScript. Express is a framework for building a web server
- We initialize our folder as an npm project by typing `npm init`
- Add npm packages by typing `npm install <package-name> `, e.g., `npm install express`
- Use the `require()` function to bring in the express code so that we can use it
- Add `app.listen(PORT)` to turn on our server. By default, it will listen to http://localhost. When we work on our computers, we have to pick a port. Usually, ports for servers we'll be building are in the numeric range of 3000 - 9000. When we host our projects on the web, the port will be automatically configured for us

We are going to add more complexity to this setup so that we can start to bring some more best practices into our app.

### Start coding

- navigate to your Desktop or another convenient folder
- `git status` to make sure you are not already in a `git` repository
- `mkdir express-minerals`
- `cd express-minerals`
- `touch server.js`
- `npm init -y` (this will automatically say yes to all the npm default settings - this is fine for tutorials, small test builds, etc.)
- `npm install express`
- `touch .gitignore` (tell git which files to ignore) and add the following to the file:

```
node_modules
.env
```

This file is not JavaScript, so don't add semi-colons.

**Thought question**: Why and when do we start file/folder names with a `.`?

### JavaScript Environments

There are many places (environments) to run JavaScript, for example, in the browser or terminal. Each place is a different environment: Your computer is a different environment than your friend's computer. Therefore, being able to add a file that stores specific information about running the code in the environment it is in is a helpful feature.

Let's make our environmental variable file. We'll call it `.env`. We've already chosen not to track the file in our `.gitignore`. This will allow each environment to keep its own file, and if there is sensitive information like passwords or API keys, they will also not be shared on GitHub.

- `touch .env`
- `npm install dotenv`

- Open `.env` add

```
PORT=3333
```

This file is also not JavaScript, so don't add semi-colons, quotes, or extra white space.

### Set Up App

- `touch app.js`

**app.js**

```js
// DEPENDENCIES
const express = require("express");

// CONFIGURATION
const app = express();

// ROUTES
app.get("/", (req, res) => {
  res.send("Welcome to Express Minerals App");
});

// EXPORT
module.exports = app;
```

### Separating Concerns

We will be adding code testing for our subsequent assignments and assessments. To set it up correctly, we have to set up the server in one file and then our routes and other logic in other files.

When we import an npm package, like express, we require it by name.
We can also include code from other files we've created. We have to give the relative path when it is a file we've made.

When we set up our `server.js` file, we must first import our code from our app.js.

Then, we need to configure our app to use our environment variables.

We're using a package called [dotenv](https://www.npmjs.com/package/dotenv)

We will follow the basic instructions/example code provided in the docs.

**server.js**

```js
// DEPENDENCIES
const app = require("./app.js");

// CONFIGURATION
require("dotenv").config();
const PORT = process.env.PORT;

// LISTEN
app.listen(PORT, () => {
  console.log(`🪨 Listening on port ${PORT} 💎 `);
});
```

Get the app running with `nodemon server.js` and go to http://localhost:3333

**Note:** Having the variable `PORT` in all caps is the default for deploying your back-end on the cloud(make it live). In other languages, all caps denote that the variable is a constant and cannot be changed. So even though it is not necessary for JavaScript, we will keep this variable in all caps so we are ready for deployment and won't have to go back and edit it.

### Building An App

Right now, we'll be focused on learning express. Later we'll learn about databases and how to integrate a database into our express app.

In the meantime, we're mocking a database by just using arrays of objects that will live in memory while the server is running.

Data modeling is a technique for defining business requirements for a database. In our case, we are making a simple version of a rock shop, and our data will be an array of objects. We're going to store this data in a file called `rock.js` that will be inside a folder called `models`.

- `mkdir models`
- `touch models/rock.js`

We need to assign it to `module.exports` so we can import it elsewhere in our app.

**models/rock.js**

```js
module.exports = [
  "Crocoite",
  "Wulfenite",
  "Amber",
  "Malachite",
  "Azurite",
  "Amethyst",
];
```

Copy and paste this array into `rock.js`

**IMPORTANT** if you forget to use `module.exports` or misspell it as `module.export`, you will get an empty object. Be sure to check this carefully, as it is a common bug.

Let's make an index of all the rocks.

**app.js**

```js
// DEPENDENCIES
const express = require("express");
const rocks = require("./models/rock.js");

// CONFIGURATION
const app = express();

// ROUTES
app.get("/", (req, res) => {
  res.send("Welcome to Express Minerals App");
});

app.get("/rocks", (req, res) => {
  res.send(rocks);
});

// EXPORT
module.exports = app;
```

http://localhost:3333/rocks

### Showing each rock

We have 6 rocks for sale. We could create a route for each rock. Six is very doable. But what if this store is a huge success, and we end up having hundreds or thousands of rocks?

Writing a route for each one would be

- tedious
- hard to maintain

Instead, we want user input for the rock the user wants to see. We'll use the array position and have the user type in the URL for now. Eventually, we would create a complete web page with links and id numbers for our rocks.

Let's try it out.

We create a request parameter by adding a `:` to distinguish it from a regular path.

Whatever value the user types will then be sent with the request and is accessible inside the `request.params` object.

Let's try it:

```js
// ROUTES
app.get("/rocks/:index", (req, res) => {
  res.send(req.params);
});
```

http://localhost:3333/rocks/1

We can now use this value to access the item in the array's position 1.

```js
// ROUTES
app.get("/rocks/:index", (req, res) => {
  res.send(rocks[req.params.index]);
});
```

We can make this code easier to read by using object deconstruction. Let's try it.

```js
// ROUTES
app.get("/rocks/:index", (req, res) => {
  const { index } = req.params;
  res.send(rocks[index]);
});
```

## A Common Error

You can only have one response for every request: This is the rule of the http protocol. If you try to send multiple responses, you'll get an error in the terminal. Let's try it!

```js
app.get("/rocks/oops/:index", (req, res) => {
  const { index } = req.params;
  res.send(rocks[index]);
  // error cannot send more than one response!
  res.send("this is the index: " + index);
});
```

We can, however, have multiple statements if we use our `if` statements or other program logic correctly:

```js
app.get("/rocks/:index", (req, res) => {
  const { index } = req.params;
  if (rocks[index]) {
    res.send(rocks[index]);
  } else {
    res.send("cannot find any rocks at this index: " + index);
  }
});
```

http://localhost:3333/rocks/98

or

http://localhost:3333/rocks/not_a_valid_index_position

## Place routes in the correct order

- Express starts at the top of your `app.js` file and attempts to match the URL used by the browser with routes in the order in which they're defined.
- URL params (e.g. `:foo`, `:example`, `like `) can be anything, a number or a string
- Therefore, if you have these routes in this order in your `server.js`:
- `/:color`
- `/rocks`
- And you want to get to `/rocks` - you'll always hit the `/:color` route because the URL parameter will accept _any_ string. It doesn't know that `rocks` is something specific/special
- To fix this, we put the more specific routes first
- `/rocks`
- `/:color`
  Now, from top to bottom, the more specific route `/rocks` will be triggered when the URL has `/rocks`, and if it doesn't match `/rocks`, it will go to the following route.

Let's code an example of this together:

```javascript
app.get("/rocks/:index", (req, res) => {
  //:index can be anything, even awesome
  res.send(rocks[req.params.index]);
});

app.get("/rocks/awesome", (req, res) => {
  //this will never be reached
  res.send(`
 <h1>Rocks are awesome!</h1>
 <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/cc/Bismuth_crystal_macro.jpg/800px-Bismuth_crystal_macro.jpg" >
 `);
});
```

If this happens, reorder them so that more specific routes come before less specific routes (those with params in them)

```javascript
app.get("/rocks/awesome", (req, res) => {
  res.send(`
 <h1>rocks are awesome!</h1>
 <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/91/Pyrite_-_Huanzala_mine%2C_Huallanca%2C_Bolognesi%2C_Ancash%2C_Peru.jpg/260px-Pyrite_-_Huanzala_mine%2C_Huallanca%2C_Bolognesi%2C_Ancash%2C_Peru.jpg" >
 `);
});

app.get("/rocks/:index", (req, res) => {
  const { index } = req.params;
  if (rocks[index]) {
    res.send(rocks[index]);
  } else {
    res.send("cannot find anything at this index: " + index);
  }
});
```

## Multiple Parameters

We can add more parameters to the `req.params` object:

```js
app.get("/hello/:firstName/:lastName", (req, res) => {
  console.log(req.params);
  const { firstName, lastName } = req.params;
  res.send(`hello ${firstName} ${req.params.lastName}`);
});
```

http://localhost:3333/hello/karolin/rafalski

Try other first names and last names!

## Query Strings

Another way to get values from the URL is with query strings.

Query strings go at the end of a path and start with a `?`. They are key-value pairs with the syntax of `value=100`.

You can add as many as you like by separating them with an ampersand `&`.

```js
app.get("/calculator/:operator", (req, res) => {
  console.log("This is req.params", req.params);
  console.log("This is req.query", req.query);
  const sum = req.query.num1 + req.query.num2;
  res.send("sum is " + sum);
});
```

http://localhost:3333/calculator/add?num1=5&num2=4

Uh oh! We got 54 instead of 9. Remember, incoming requests always come in as strings. Let's clean up our code:

```js
app.get("/calculator/:operator", (req, res) => {
  const { num1, num2 } = req.query;
  const sum = Number(num1) + Number(num2);
  res.send("sum is " + sum);
});
```

Let's add a bit more logic.

```js
app.get("/calculator/:operator", (req, res) => {
  const { num1, num2 } = req.query;
  let sum = 0;
  if ((req.params.operator === "add")) {
    sum = Number(num1) + Number(num2);
  }
  res.send("sum is " + sum);
});
```

**Bonus**

Can you figure out how to add the functionality of `subtract`, `multiple`, and `divide` ?
