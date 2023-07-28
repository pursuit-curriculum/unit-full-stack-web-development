# Express Connect React

## Learning Outcomes

By the end of this lesson, you should be able to:

- Be able to connect a React application to an Express back-end API.
- Be able to describe all 7 RESTful views.
- Be able to set a base URL that can be easily updated, depending on the local environment, and describe why it is important to do so.
- Be able to describe why an easily updated URL is useful
- Be able to view an index route with data from the express API.
- Be able to describe what CORS is and why the default is to block cross-origin requests.

---

## Getting Started

- Go to your bookmarks app and get it started.
- Open a new tab in terminal <kbd>command</kbd> <kbd>t</kbd>, do not shut down your bookmarks app
- Note: if you end up having a lot of extra data from testing, restart your express API (restart nodemon)
- Fork the starter code in your class GitHub Organization called [starter-express-connect-react](https://github.com/pursuit-curriculum-resources/starter-express-connect-react).
- `git clone` the forked repository.
- `cd` to the directory where you cloned it.
- `npm install` to install dependencies already included in the `package.json`.
- `touch .env`.

**.env** (Details below)

```
VITE_API_URL=http://localhost:3003
```

(or whatever port your bookmarks API is on)

- Start your React app

### Landing Page/Index

<details><summary>Landing Page</summary>

![](../assets/landing-page.png)

</details>

Let's click on `Bookmarks` in the navigation to go to `/bookmarks`

<details><summary>Index Page Empty</summary>

![](../assets/index-empty.png)

</details>

We can see that we have a view for our index page. But we need to connect this app to our back end.

We can also go to the following:

- http://localhost:3000/bookmarks/1 to see an empty show page
- http://localhost:3000/bookmarks/1/edit to see an empty edit page

We can do so using `axios` much like we did use third-party APIs.

## Setting Up the App to Make Requests

### The Base URL

Before we get started, we have to think about a problem we will have. If we hard code `http://localhost:3003` into our react app, when we change it to be hosted online, we would have to go into our app and change every single instance of `http://localhost:3003`. Further, if we decide to host our app on another service, we would have to go in and change it again. If we are working on our app locally (usually described as a development environment) and having it available online (usually described as a production environment), we would spend a lot of time switching the URLs back and forth.

We would rather have the correct URL depending on our situation set, and we would like to include it in one place, so we only have to update it in one place if it changes.

There are a few ways to do this, depending on the goals (amount of safety, other app features). As you build different apps, you may see different approaches.

We are going to use Vite's way of setting up these variables.

- Create a `.env` on the same level as `package.json`.
- Start all variable names with `VITE_`.
- `VITE_API_URL` or `VITE_MY_VAR` are acceptable names.
- `Vite_My_Var` is NOT going to work.

We should have done this as part of our setup earlier.

**src/Components/Bookmarks.js**

At the top:

```js
const API = import.meta.env.VITE_API_URL;
```

Confirm it is working

```js
console.log(API);
```

Remove this console log once you have confirmed the value.

## Getting Data from the API

Inside the `Bookmarks` function, add `setBookmarks` so we can update the state when `bookmarks` changes.

```js
const [bookmarks, setBookmarks] = useState([]);
```

### Loading the Bookmarks Index on Page Load

With React, we sometimes have to think about _when_ things happen. For us, we want to make an API call to get our data AFTER the component has been fully loaded (mounted in the DOM) and then update the state.

If we made the API call first, the DOM was not fully loaded, and the state was updated first, we would risk not seeing our data, even though the API call was successful.

Inside the `useEffect` function, we will add our initial API call to get all the bookmarks.

- Make a `GET` request using fetch. `then` when there is a `response`, do something. In our case, we want to update state by using the `setBookmarks` that was declared at the beginning of the `App` component. The state will be updated to be our array of bookmark objects.

**src/Components/Bookmarks.js**

Note: Make sure this code is before the `return` statement

Let's add `useEffect` to the component. Update the line that imports `useState`

```js
import { useState, useEffect } from "react";
```

```js
useEffect(() => {}, []);
```

Let's make a get request.

```js
useEffect(() => {
  fetch(`${API}/bookmarks`);
}, []);
```

Once the request is complete, `then`, what should happen?

`.then()` takes two arguments. Both callback functions

```js
.then(()=> {}, ()=>{})
```

The first is executed when the promise is fulfilled (in our case, a successful response from the server).

The second is executed if the promise is rejected (in our case, there is some error with our fetch request).

We can, instead, add a `.catch()` - this will be the final error handling of our fetch request.

**BONUS** [Take the time to compare and contrast](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)

While we don't need error handling for our app to work, it is conducive to debugging and writing code that would notify users that something has gone wrong.

```js
.then((response)=> {})
.catch((error)=> {})
```

Let's put it together with `useEffect` and `axios`

Try to build from the outside-in, instead of left to right. Add the callback.

```js
useEffect(() => {
  fetch(`${API}/bookmarks`).then(() => {});
}, []);
```

The thing we are waiting on is the response. The response comes in as has a lot of extra information. We only need the bookmarks data. Compare and contrast the info given in the two console logs:

```js
useEffect(() => {
  fetch(`${API}/bookmarks`).then((response) => {
    console.log(response.body);
  });
}, []);
```

When you look at the response body here, you will see it is of type `ReadableStream`. It must be converted to JSON.

```js
useEffect(() => {
  fetch(`${API}/bookmarks`).then((response) => {
    console.log(response.json());
  });
}, []);
```

Next you must return the value and _then_ set state:

```js
useEffect(() => {
  fetch(`${API}/bookmarks`)
    .then((response) => {
      return response.json();
    })
    .then((responseJSON) => setBookmarks(responseJSON));
}, []);
```

Add error handling:

```js
useEffect(() => {
  fetch(`${API}/bookmarks`)
    .then((response) => {
      return response.json();
    })
    .then((responseJSON) => setBookmarks(responseJSON))
    .catch((error) => console.error(error));
}, []);
```

**Uh Oh!** We found a ~~bug~~ feature!

![](../assets/cors-error.png)

Our React app is making a request from a different origin than where our API is running. This is a great security feature! By default, requests coming from other origins are not allowed.

However, we want to allow our React app to make requests to our API.

We must go to our express API to set up this allowance.

#### Express API

**terminal**

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

This will allow ANY app to make requests to your API. Since we are just building a sample app and not hosting it online, we don't have to worry about restricting it. Later, when your app is deployed in the cloud, you would want to be more thoughtful about how to properly allow/restrict access.

**Success:**

<details><summary>Index Page Loaded</summary>

![](../assets/index-loaded.png)

</details>

<br />

### One More Thing

Our app will show more data than what we have in our mock database.

In our express app, let's update these values:

**models/bookmarksArray.js**

```js
module.exports = [
  {
    name: "MDN",
    url: "https://developer.mozilla.org/en-US/",
    isFavorite: true,
    category: "educational",
    description: "",
  },
  {
    name: "Apartment Therapy",
    url: "https://www.apartmenttherapy.com",
    isFavorite: true,
    category: "inspirational",
    description: "",
  },
  {
    name: "DMV",
    url: "https://dmv.ny.gov",
    isFavorite: false,
    category: "adulting",
    description: "",
  },
];
```

## NEXT:

[The Rest of the Routes](./rest-of-the-routes.md)

[Reference Back-end](https://github.com/pursuit-curriculum-resources/bookmarks-express-demo/tree/connect-react)
