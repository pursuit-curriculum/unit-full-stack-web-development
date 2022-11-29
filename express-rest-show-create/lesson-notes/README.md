# Express RESTful Routes, Middleware: Show and Create

## Organization of routes

Imagine you are in charge of maintaining a website for a school. Your task is to create pages for all the cancellations/delays to snow storms/inclement weather.

**~5 Minute In-class activity**

How would you create the routes? Here are some examples. Think about the following details:

- Are the routes stable? Is there a maintainable pattern to follow if the number of routes grows?
- Are the routes organized in a way that makes sense?
- Are the routes as simple as possible?
- Can you reorganize them in a (relatively) easy way?
- Can both creators and users understand what is going on?

- `/January-Events/20/2019/Snow`
- `/Snowstorms/Cancellations/Tomorrow`
- `/Info/Events/Important/inclementWeather`

How would you continue to build out the routes if the following events happened?

- December 18, 2018 - early dismissal
- February 13, 2020 - half day
- May 4, 2016 - windstorm/county power outage

Taking a moment to think about it, we'll realize it's hard to organize. So challenging a computer scientist named Roy Fielding ended up doing his dissertation on [Architectural Styles and the Design of Network-based Software Architectures](https://en.wikipedia.org/wiki/Representational_state_transfer). He took feedback from over 500 developers to hone a model to a core set of principles called REST.

REST stands for **Re**presentational **S**tate **T**ransfer - the technical meaning can take a while to study and learn and gets much deeper than we need to worry about today. However, we can effectively utilize the pattern of routes to build basic apps that use these best practices.

### Restful Routes

|  #  |   Action   |      URL       | HTTP Verb |    CRUD    |                Description                 |
| :-: | :--------: | :------------: | :-------: | :--------: | :----------------------------------------: |
|  1  | **Create** |   /bookmarks   |   POST    | **C**reate |           Create a new bookmark            |
|  2  |  _Index_   |   /bookmarks   |    GET    |  **R**ead  |   Get a list (or index) of all bookmarks   |
|  3  |  **Show**  | /bookmarks/:id |    GET    |  **R**ead  | Get an individual view (show one bookmark) |
|  4  |   Update   | /bookmarks/:id |    PUT    | **U**pdate |             Update a bookmark              |
|  5  |  Destroy   | /bookmarks/:id |  DELETE   | **D**elete |             Delete a bookmark              |

**Note** This pattern of routes is similar across many technical stacks. If you ever need a refresher, you can easily google it and find a resource [like this one](https://guides.rubyonrails.org/routing.html#crud-verbs-and-actions).

In our app, we've already built the `index` route.

Today we'll build the `show` and `create` routes.

## Getting Started

- Return to your `bookmarks` app
- Make sure you are on the same directory level as your `package.json` (`ls` to check)
- Check `/` and `/bookmarks` routes to confirm your app is still working as expected

## Show Route

| Action |      URL       | HTTP Verb |   CRUD   |                Description                 |
| :----: | :------------: | :-------: | :------: | :----------------------------------------: |
|  Show  | /bookmarks/:id |    GET    | **R**ead | Get an individual view (show one bookmark) |

**controllers.js**

When we connect to a database, we'll use the `id` (a unique number./identifier for each item in the database). But for now, we will use the index position of the array to mock the behavior.

Create a show route based on the array position:

```js
// SHOW
bookmarks.get("/:arrayIndex", (req, res) => {
  const { arrayIndex } = req.params;
  res.json(bookmarksArray[arrayIndex]);
});
```

**Error Handling:** A large part of making a good application is handling errors and giving users feedback so they can use the app confidently and easily. We'll demonstrate some simple error handling. In the interest of time, we won't cover all the ways to handle errors, but as you work on your projects, you should continue improving error handling.

```js
// SHOW
bookmarks.get("/:arrayIndex", (req, res) => {
  if (bookmarkArray[req.params.arrayIndex]) {
    res.json(bookmarkArray[req.params.arrayIndex]);
  } else {
    res.status(404).json({ error: "Not Found" });
  }
});
```

## Create Route

| Action |    URL     | HTTP Verb |    CRUD    |      Description      |
| :----: | :--------: | :-------: | :--------: | :-------------------: |
| Create | /bookmarks |   POST    | **C**reate | Create a new bookmark |

So far, all our requests have used the `GET` verb. As we've seen with request parameters and query strings, GET requests can only pass data through the URL (request header).

We want to allow users to add data via an HTML form. HTML form data comes through in the `body` of a request.

We will use a new verb, `POST`, that will allow us to pass data through the response body.

Since we are working with an array stored in memory, we will push our new data onto the array. The problem is every time we restart the server; our changes will disappear. Later, we'll learn about persisting our data with a database.

**controllers.js**

Let's add a route that will take the data from the request body and push it onto the `bookmarksArray`.

```js
// CREATE
bookmarks.post("/", (req, res) => {
  bookmarksArray.push(req.body);
  res.json(bookmarkArray[bookmarkArray.length - 1]);
});
```

How do we make a POST request? We can't use the browser URL as we've done.

We can use a command line tool called [cURL](https://curl.se) - cURL stands for client URL.

Let's try a simple get request.

- `curl http://localhost:3003/bookmarks`

Nice! We can see our data in the terminal.

We can also `POST` data using cURL.

- `curl -H "Content-Type: application/json" -X POST -d '{"name":"AV Club", "url": "https://www.avclub.com"}' localhost:3003/bookmarks`

Or, if you have the latest version of cURL:

- `curl --json '{"name":"AV Club", "url": "https://www.avclub.com"}' -X POST localhost:3003/bookmarks`

We should get a message that the route was found and returns the new bookmark.

<details><summary>Breaking down the cURL request</summary>

Commands in the terminal are a bit like sentences.

- `cURL` the application we want to run
- `-` This notes that a `flag` is being added. It allows us to add options to our command. We can add multiple flags in a command
- `-H` means headers. Remember, a request is made up of a header and a body. [A list of request fields for the header](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields). In our case, we want to inform the server that we will send `JSON` instead of plain text, or possibly URL encoded text, etc.
- `-X` what kind of action are we going to send? `GET`, `POST`, `PUT`, `DELETE`, or other.
- `-d` is for data, the next thing will be some valid JSON wrapped in single quotes, that is the data we are sending, inside of a web application in a front end, the user would enter this information into a form and then submit it
- And finally, the location where we are sending the request, in our case, it is `localhost:3003/bookmarks`

For more info on the [--json flag](https://daniel.haxx.se/blog/2022/02/02/curl-dash-dash-json/)

</details>

Let's make a get request back to our index:

- `curl http://localhost:3003/bookmarks`

Uh oh! Rather than putting in our data, we got a null property.

The request body can come in many formats, such as JSON, URL-encoded, binary, etc. We need to write code that will `parse` the incoming request body and give us the data we are trying to get. Typically, our data will be collected from a form.

The request will likely come in as URL-encoded if we use a traditional HTML form. But if we use a front-end app like React, we will send our data as JSON.

We could write the logic to take the incoming data and parse it. But since it is such a common problem, express already has a way to parse the code for us. We just need to configure it properly.

We will use some `middleware` to parse all incoming JSON.

**app.js**

Make sure you put this ABOVE your routes.

```js
// MIDDLEWARE
app.use(express.json()); // Parse incoming JSON
```

Now we should be able to run our cURL commands and see our new data (don't forget you can scroll through previous commands in the terminal by using the up arrow)

- `curl -H "Content-Type: application/json" -X POST -d '{"name":"AV Club", "url": "https://www.avclub.com"}' localhost:3003/bookmarks`

- `curl http://localhost:3003/bookmarks`

We should also be able to see this change in our browser. Let's visit http://localhost:3003/bookmarks, and we should see our new bookmark at the bottom of the list.

## Middleware in more depth

Middleware is code that "runs in the middle" of a request and response. We have a third parameter called `next`. Next is a function that will let the app know when it is time to move to the next callback.

It can be set up to run for every route:

**app.js** Above other routes

```js
app.use((req, res, next) => {
  console.log("This code runs for every request");
  next();
});
```

Check the terminal to see this `console.log` - it should run every time you make a browser request.

Or you can add middleware to specific routes only.

**controllers/bookmarksController.js**

```js
const validateURL = (req, res, next) => {
  console.log(
    "This function checks the validity of the URL entered by the user"
  );
};
```

Add this function to `CREATE`.

```js
// CREATE
bookmarks.post("/", validateURL, (req, res) => {
  bookmarksArray.push(req.body);
  res.json(bookmarkArray[bookmarkArray.length - 1]);
});
```

Now you can test this again by using the up arrow in the terminal and rerunning the cURL request and see that it almost works. We must add the `next` function to get it fully working.

```js
const validateURL = (req, res, next) => {
  console.log(
    "This function checks the validity of the URL entered by the user"
  );
  next();
};
```

Now it should let us complete the Post request.

### Code Organization

In our file full of routes, there is a function to validate input.

If this is our only function, it isn't a big deal to leave it here. But if our app kept growing, this would become hard to manage.

Let's put it in its own file. We can put the file anywhere (inside the `controllers `folder, on the same level as the `package.json` file, we can make a new file, etc.), but we'll just put it in the `models` folder, `models` have to do with data, and we are validating incoming data. Again, there are many different ways to organize an express app. We'll focus on simple patterns to practice.

Ensure you are on the same level as `package.json` in the terminal.

- `touch models/validations.js`

Cut and paste the `validateURL` function into this file and export it:

```js
const validateURL = (req, res, next) => {
  console.log(
    "This function checks the validity of the URL entered by the user"
  );
  next();
};

module.exports = { validateURL };
```

**controllers/bookmarkController.js**

```js
const { validateURL } = require("../models/validations.js");
```

And now check that the functionality (console log runs on a POST request works)

### Validate URL Logic

Right now, our function doesn't check for anything.

Let's take a few minutes to write some pseudocode to check if the URL entered starts with `http` or `https` and share it as a class.

It is essential to work on your problem-solving skills and devise a way to solve the problem. Try not to peek until you have come up with your plan first.

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

</details>

### Bonus 1 - Using A Regular Expression

We have yet to learn about regular expressions. But here is a different approach to using them.

<details><summary>Another way to test for http or https</summary>

You could also use the JavaScript function `.match()`, which will test for a matching string. You can use a simple string or a [regular expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp)

So it would look like this.

```js
req.body.url.match(/https?:\/\//);
```

[learn more about regular expressions](https://regexone.com/)

</details>
