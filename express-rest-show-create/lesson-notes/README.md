# Express RESTful Routes, Middleware: Show and Create

## Class Activity

### Organization of routes

Imagine you are in charge of maintaining a website for a school. Your task is to create pages for all the cancellations and delays due to snow storms and inclement weather.

How would you create the routes? Here are some examples. Think about the following details:

- Are the routes stable? Is there a maintainable pattern to follow if the number of routes grows?
- Are the routes organized in a way that makes sense?
- Are the routes as simple as possible?
- Can you reorganize them in a (relatively) easy way?
- Can both creators and users understand what is going on?

<br>

- Imagine inheriting a server that has the following routes. Do these routes fit the above principles?

  - `/January-Events/20/2019/Snow`
  - `/Snowstorms/Cancellations/Tomorrow`
  - `/Info/Events/Important/inclementWeather`

- How would you continue to build out the routes if the following events happened?

  - December 18, 2018 - early dismissal
  - February 13, 2020 - half day
  - May 4, 2016 - windstorm/county power outage

- What are the challenges of creating routes for the above scenarios?

- Did you make sure your routes are:
  - Stable
  - Organized
  - Following a simple pattern
  - Easily reorganized code
  - Understandable by creators and users

### RESTful Routes

- What are RESTful routes?

- Why were they developed?

- What are the five RESTful routes for a back-end server that sends JSON?

|  #  |   Action   |      URL       | HTTP Verb |    CRUD    |                Description                 |
| :-: | :--------: | :------------: | :-------: | :--------: | :----------------------------------------: |
|  1  | **Create** |   /bookmarks   |   POST    | **C**reate |           Create a new bookmark            |
|  2  |  _Index_   |   /bookmarks   |    GET    |  **R**ead  |   Get a list (or index) of all bookmarks   |
|  3  |  **Show**  | /bookmarks/:id |    GET    |  **R**ead  | Get an individual view (show one bookmark) |
|  4  |   Update   | /bookmarks/:id |    PUT    | **U**pdate |             Update a bookmark              |
|  5  |  Destroy   | /bookmarks/:id |  DELETE   | **D**elete |             Delete a bookmark              |

## Getting Started

Today we'll build the `show` and `create` routes. Note we've already made the `index` route.

- Return to your `bookmarks` app. (If your app is not working, you may clone down [the previous complete build and work from there](https://github.com/pursuit-curriculum-resources/bookmarks-express-demo)).

- Make sure you are on the same directory level as your `package.json` (`ls` to check) to start your app.

- Check `/` and `/bookmarks` routes to confirm your app is still working as expected.

## Show Route

Add a show route to your app.

- What is the purpose of this route?

- Where would you write this code?

| Action |      URL       | HTTP Verb |   CRUD   |                Description                 |
| :----: | :------------: | :-------: | :------: | :----------------------------------------: |
|  Show  | /bookmarks/:id |    GET    | **R**ead | Get an individual view (show one bookmark) |

Create a show route based on the array position:

```js
// controllers/bookmarksController.js
// SHOW
bookmarks.get("/:arrayIndex", (req, res) => {
  const { arrayIndex } = req.params;
  res.json(bookmarksArray[arrayIndex]);
});
```

**Error Handling:** A large part of making a good application is handling errors and giving users feedback so they can use the app confidently and quickly. We'll demonstrate some simple error handling. In the interest of time, we won't cover all the ways to handle errors, but as you work on your projects, you should continue improving error handling.

```js
// controllers/bookmarksController.js
// SHOW
bookmarks.get("/:arrayIndex", (req, res) => {
  if (bookmarksArray[req.params.arrayIndex]) {
    res.json(bookmarksArray[req.params.arrayIndex]);
  } else {
    res.status(404).json({ error: "Not Found" });
  }
});
```

## Create Route

Add a show route to your app.

- What is the purpose of this route?

- Where would you write this code?

| Action |    URL     | HTTP Verb |    CRUD    |      Description      |
| :----: | :--------: | :-------: | :--------: | :-------------------: |
| Create | /bookmarks |   POST    | **C**reate | Create a new bookmark |

- Why does `create` have the same path as the index route?

- How is it distinguished from the index route?

Let's add a route to take the data from the request body and push it onto the `bookmarksArray`.

```js
// controllers/bookmarksController.js
// CREATE
bookmarks.post("/", (req, res) => {
  bookmarksArray.push(req.body);
  res.json(bookmarksArray[bookmarksArray.length - 1]);
});
```

- How do we make a POST request that will access this route?

- Why can't we use the browser URL to make this request, as we've done for the GET routes?

Let's use cURL and start with a simple get request.

- What is cURL?

- What is the advantage of using cURL over creating an HTML form or a GUI like Postman?

- When is it a good time to use cURL?

- When does it make more sense to use an app like Postman?

- `curl http://localhost:3003/bookmarks`

We can also `POST` data using cURL.

- `curl -H "Content-Type: application/json" -X POST -d '{"name":"AV Club", "url": "https://www.avclub.com"}' http://localhost:3003/bookmarks`

<br>

We should get a message that the route was found and returns the new bookmark. But it does not fully work yet. For now, it should add a value of `null` to the array.

Before moving on, break down the cURL command.

- What is `-H`?

- What is `"Content-Type: application/json"`

- Can you write `contentType: json`? Or any other variation to `"Content-Type: application/json"`?

- What is `-X`? Why do you need it?

- What is `POST`? Why do you need it?

- Can you type `localhost:3003/bookmarks` or must you type `http://localhost:3003/bookmarks`?

- Why do the keys and values of the data need to be wrapped in double quotes?

- Why does the data object need to be wrapped in single quotes?

After making a POST request, make another GET request:

- `curl http://localhost:3003/bookmarks`

- Why is the value null and not the object you inputted?

- How can you get your data from the request object?

- What is middleware?

- Why does the incoming data need to be parsed?

```js
// app.js
// MIDDLEWARE
app.use(express.json()); // Parse incoming JSON
```

- What is `express.json()`? What does it do?

- What is `app.use()`? What does it do?

- Why is the placement of the middleware important?

- Where should this middleware be placed?

- What happens if you place this middleware below all the routes?

Now we should be able to run our cURL commands and see our new data (don't forget you can scroll through previous commands in the Terminal using the up arrow rather than typing them all out again).

- `curl -H "Content-Type: application/json" -X POST -d '{"name":"AV Club", "url": "https://www.avclub.com"}' localhost:3003/bookmarks`

- `curl http://localhost:3003/bookmarks`

We should also be able to see this change in our browser. Let's visit http://localhost:3003/bookmarks, and we should see our new bookmark at the bottom of the list.

## Custom middleware

In the above example, you used some built-in middleware. You can also build your own.

```js
// app.js
app.use((req, res, next) => {
  console.log("This code runs for every request");
  return next();
});
```

Check the Terminal to see this `console.log` - it should run every time you make a request.

Or you can add middleware to specific routes only.

```js
// controllers/bookmarksController.js
const validateURL = (req, res, next) => {
  console.log(
    "This function checks the validity of the URL entered by the user."
  );
};
```

Add this function to create a new bookmark:

```js
// controllers/bookmarksController.js
// CREATE
bookmarks.post("/", validateURL, (req, res) => {
  bookmarksArray.push(req.body);
  res.json(bookmarksArray[bookmarksArray.length - 1]);
});
```

Now you can test this again by using the up arrow in the Terminal and rerunning the cURL request and see that it almost works. We must add the `next()` function to get it fully working.

```js
// controllers/bookmarksController.js
const validateURL = (req, res, next) => {
  console.log(
    "This function checks the validity of the URL entered by the user."
  );
  return next();
};
```

Now it should let us complete the POST request.

- What does the `next()` function do?

- What happens if you only use `return` instead of `next()`?

- Can middleware return a response?

### Code Organization

There is a function to validate input in our file full of routes.

- Is it ok to leave this function in the controllers file if this is the only function like this?

- Is it ok to leave this function in the controllers file if it is only used once?

- When should you work on organizing your code differently?

- What would be a sensible file/folder name for this functionality?

Let's put it in its own file. We can put the file anywhere (inside the `controllers `folder, on the same level as the `package.json` file, we can make a new file, etc.), but we'll just put it in the `models` folder, `models` have to do with data, and we are validating incoming data. Again, there are many different ways to organize an Express app. We'll focus on simple patterns to practice.

Ensure you are on the same level as `package.json` in the Terminal.

- `touch models/validations.js`

Cut and paste the `validateURL` function into this file and export it:

```js
const validateURL = (req, res, next) => {
  console.log(
    "This function checks the validity of the URL entered by the user."
  );
  return next();
};

module.exports = { validateURL };
```

**controllers/bookmarkController.js**

```js
const { validateURL } = require("../models/validations.js");
```

And now check that the functionality (console.log runs on a POST request).

### Validate URL Logic

Right now, our function doesn't check for anything.

Let's take a few minutes to write some pseudocode to check if the URL entered starts with `http` or `https` and share it as a class.

Working on your problem-solving skills and devising a way to solve the problem is essential. Try not to peek until you have come up with your plan first.

<details><summary>One possible way to do it</summary>

```js
const validateURL = (req, res, next) => {
  if (
    req.body.url.substring(0, 7) === "http://" ||
    req.body.url.substring(0, 8) === "https://"
  ) {
    return next();
  } else {
    res
      .status(400)
      .send(`Oops, you forgot to start your url with http:// or https://`);
  }
};
```

- Why is a status code added in the else statement?

- What does a status code of 400 mean?

</details>

### Bonus 1 - Using A Regular Expression

We have yet to learn about regular expressions. But here is a different approach to using them.

<details><summary>Another way to test for http or https</summary>

You could also use the JavaScript function `.match()`, which will test for a matching string. You can use a simple string or a [regular expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp)

So it would look like this.

```js
req.body.url.match(/https?:\/\//);
```

- The regular expressions starts and ens with a forward slash `/`.
- Inside the regular expression the exact letters `http` are expected to be matched.
- The `?` denotes that the previous character, `s` is optional.
- Since `/` is a special character, it must be escaped. The backslash `\` allows the forward slash `/` to be used as a regular character.

[learn more about regular expressions](https://regexone.com/)

</details>

## Resource

[Completed class build](https://github.com/pursuit-curriculum-resources/bookmarks-express-demo/tree/middleware)
