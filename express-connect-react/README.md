# Express Connect React

## Introduction

You now have a nicely working back-end API that serves JSON with full CRUD using Express. Unfortunately, it isn't the best user experience for non-developers. You need to build a front-end.

## Learning Outcomes

By the end of this lesson, you should be able to:

- Be able to connect a React application to an Express back-end API.
- Be able to describe all 7 RESTful views.
- Be able to set a base URL that can be easily updated, depending on the local environment, and describe why it is important to do so.
- Be able to describe why an easily updated URL is useful
- Be able to view an index route with data from the express API.
- Be able to describe what CORS is and why the default is to block cross-origin requests.

---

## The Seven RESTful Routes

The seven RESTful routes are the classic example of a RESTful pattern. Before front-end applications like React became more standard, servers handled creating HTML views for a web site. That means that there are two additional views: `New`, which is a view with a form for users to fill out and submit `Create` requests and `Edit` which is a view with a form for users to fill out and submit an `Update` requests. And certain routes like `Create`, `Delete` and `Update` don't have views, so there is nothing to build on the front-end for them.

While this pattern is classic, and you will follow it exactly for learning fundamentals, you will see many variations in the wild. These variations are to meet specific needs of an application and to improve user experience.

We have built the first five routes in Express:

|  #  | Action  |     URL     | HTTP Verb |    CRUD    |               Description               |
| :-: | :-----: | :---------: | :-------: | :--------: | :-------------------------------------: |
|  1  |  Index  |   /colors   |    GET    |  **R**ead  |   Get a list (or index) of all colors   |
|  2  |  Show   | /colors/:id |    GET    |  **R**ead  | Get an individual view (show one color) |
|  3  | Create  |   /colors   |   POST    | **C**reate |           Create a new color            |
|  4  | Destroy | /colors/:id |  DELETE   | **D**elete |             Delete a color              |
|  5  | Update  | /colors/:id |    PUT    | **U**pdate |             Update a color              |

<br />

The next four are ones that you will build with a front-end:

|  #  | Action |       URL        | HTTP Verb |   CRUD   |               Description               |
| :-: | :----: | :--------------: | :-------: | :------: | :-------------------------------------: |
|  1  | Index  |     /colors      |    GET    | **R**ead |   Get a list (or index) of all colors   |
|  2  |  Show  |   /colors/:id    |    GET    | **R**ead | Get an individual view (show one color) |
|  3  |  New   |   /colors/new    |    GET    | **R**ead |    Get a form to create a new color     |
|  4  |  Edit  | /colors/:id/edit |    GET    | **R**ead |      Get a form to update a color       |

Which two are new, and which ones have you built with Express?

Do you notice any patterns with the front-end routes?

<details><summary>Answer</summary>

All the front-end routes are GET requests that are meant to read data.

There are no POST, PUT, or DELETE routes.

</details>

> **Note**: There are only code-examples here, there is no code-along. If you'd like, you may build your own React app and try to create an app that interacts with the colors back-end.

### The Base URL

Before you get started, you have to think about a problem you will have. If you hard code `http://localhost:3003` into your React app, when you change it to be hosted online, you would have to go into your app and change every single instance of `http://localhost:3003`. Further, if you decide to host your app on another service, you would have to go in and change it again. If you are working on your app locally (usually described as a development environment) and having it available online (usually described as a production environment), you would spend a lot of time switching the URLs back and forth.

It would be better to have a variable that is set to the correct URL depending on your situation, so you only have to update it in one place if it changes.

There are a few ways to do this, depending on the goals (amount of safety, other app features). As you build different apps, you may see different approaches.

We are going to use Vite's way of setting up these variables.

- Create a `.env` on the same level as `package.json`.
- Start all variable names with `VITE_`.
- `VITE_API_URL` or `VITE_MY_VAR` are acceptable names.
- `Vite_My_Var` is NOT going to work.

### Loading the Colors Index on Page Load

With React, you sometimes have to think about _when_ things happen. You want to make an API call to get your data AFTER the component has been fully loaded (mounted in the DOM) and then update the state.

If you made the API call first, the DOM was not fully loaded, and the state was updated first, you would risk not seeing your data, even though the API call was successful.

Inside the `useEffect` function, you will add your initial API call to get all of the resource.

- Make a `GET` request using fetch. `then` when there is a `response`, do something. In your case, you want to update state by using the `setColors` that was declared at the beginning of the `App` component. The state will be updated to be your array of color objects.

```js
import { useState, useEffect } from "react";
```

```js
useEffect(() => {}, []);
```

Make a get request.

```js
useEffect(() => {
  fetch(`${API}/colors`);
}, []);
```

The first is executed when the promise is fulfilled (in our case, a successful response from the server).

The second is executed if the promise is rejected (in our case, there is some error with our fetch request).

We can, instead, add a `.catch()` - this will be the final error handling of our fetch request.

**BONUS** [Take the time to compare and contrast](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)

While you don't need error handling for our app to work, it is conducive to debugging and writing code that would notify users that something has gone wrong.

Try to build from the outside-in, instead of left to right. Add the callback.

```js
useEffect(() => {
  fetch(`${API}/colors`).then(() => {});
}, []);
```

The thing you are waiting on is the response. The response comes in as has a lot of extra information. We only need the colors data. Compare and contrast the info given in the two console colors:

```js
useEffect(() => {
  fetch(`${API}/colors`).then((response) => {
    console.color(response.body);
  });
}, []);
```

When you look at the response body here, you will see it is of type `ReadableStream`. It must be converted to JSON.

```js
useEffect(() => {
  fetch(`${API}/colors`).then((response) => {
    console.color(response.json());
  });
}, []);
```

Next you must return the value and _then_ set state:

```js
useEffect(() => {
  fetch(`${API}/colors`)
    .then((response) => {
      return response.json();
    })
    .then((responseJSON) => setBookmarks(responseJSON));
}, []);
```

Add error handling:

```js
useEffect(() => {
  fetch(`${API}/colors`)
    .then((response) => {
      return response.json();
    })
    .then((responseJSON) => setBookmarks(responseJSON))
    .catch((error) => console.error(error));
}, []);
```

**Uh Oh!** We found a ~~bug~~ feature!

![](./assets/cors-error.png)

Our React app is making a request from a different origin than where our API is running. This is a great security feature! By default, requests coming from other origins are not allowed.

However, you want to allow our React app to make requests to our API.

We must go to our express API to set up this allowance.

## Cors

### Express API

- Return to your Express backend, make sure you are on the same level as `package.json`

- `npm install cors@2`

**app.js**
Near the top

```js
const cors = require("cors");
```

**near `app.use(express.json())`**

```js
app.use(cors());
```

This will allow ANY app to make requests to your API. Since you are just building a sample app and not hosting it online, you don't have to worry about restricting it. Later, when your app is deployed in the cloud, you would want to be more thoughtful about how to properly allow/restrict access.

## NEXT: The Rest of the Routes

- Complete the remaining RESTful Routes
- Show
- New
- Edit
- Delete

## Continue

Let's get the Show page, New, and Edit forms, and delete button working.

|  #  | Action |       URL        | HTTP Verb |   CRUD   |               Description               |
| :-: | :----: | :--------------: | :-------: | :------: | :-------------------------------------: |
|  1  | Index  |     /colors      |    GET    | **R**ead |   Get a list (or index) of all colors   |
|  2  |  Show  |   /colors/:id    |    GET    | **R**ead | Get an individual view (show one color) |
|  3  |  New   |   /colors/new    |    GET    | **R**ead |   Get a list (or index) of all colors   |
|  4  |  Edit  | /colors/:id/edit |    GET    | **R**ead | Get an individual view (show one color) |

### Show Page

You will have to request one color from our API from the show page.

You would use are `useParams` from react-router-dom. This will allow us to use the URL parameters (in our app, this will be the index position of the array).

When you are on the index view at `/colors`, you will have a link to click on a specific color. Usually, this link would be the item's unique id. Since you don't have a database yet, you can use the array position.

Imagine you click on the color `cornsilk` and it has an array position of 2. The URL would be `/colors/{index}`. You will use React Router's `Link` component.

```js
<Link to={`/bookmarks/${index}`}>✏️</Link>
```

Our function for Show will be very similar. However, we'll add an error message if the particular color cannot be found. We'll go to `/not-found`, an invalid index position that will trigger the 404 route. It still could use even better UI/UX, but this will do for our small build. As a challenge during lab, you can work on making this an even more excellent experience.

_Reminder:_ A promise is a function that allows you to _WAIT_ for a response and _THEN_ do something. The .`then()` function takes a callback, and within that callback, you can write code that should run AFTER the first function has been fulfilled (usually by returning a value).

If you pass an argument into `.then()`, it is the return value from the previous function.

Sometimes, things go wrong (for example, your server needs to be running). In that case, we add a `.catch()` function to deal with possible errors.

```js
.catch(()=>{})
```

Additionally, if the function only has one line of code, the curly braces can be skipped, and the code can be shortened to:

```js
.then(response => response.data)
```

However, this style is very limiting. We can't add any extra lines of code easily. So we'll write out the request in a longer format that is easier to maintain.
