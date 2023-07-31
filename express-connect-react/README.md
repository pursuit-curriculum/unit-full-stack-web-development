# Express Connect React

## Introduction

You now have a nicely working back-end API that serves JSON with full CRUD using Express. Unfortunately, it isn't the best user experience for non-developers. You need to build a front-end.

> **Note**: This lesson is designed to span two lesson blocks, rather than the usual one. A good stopping point is after completing the Index route, then completing the rest in the follow up lesson. Check-in with your instructor to be sure.

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

All the front-end routes are views and are GET requests that are meant to read data.

There are no POST, PUT, or DELETE routes that have views.

</details>

## Optional Code-along

You may read the examples or code-along.

There is a [React App with starter code available for this code along](https://github.com/pursuit-curriculum-resources/pre-reading-connect-react-demo). The important details to note:

- All the routes and routing is set up in `src/App.jsx`.
- Each view will have a component in the `src/Pages` folder.
- Additional components will be in the `src/Components` folder.
- The only parts of the app that need to be built are the API calls.

While the `Pages` components will be very lean and likely have one component, you could imagine that for a professional application, each view would be far more complex.

### The Base URL

Before you get started, you have to think about a problem you will have. If you hard code `http://localhost:3003` into your React app, when you change it to be hosted online, you would have to go into your app and change every single instance of `http://localhost:3003`. Further, if you decide to host your app on another service, you would have to go in and change it again. If you are working on your app locally (usually described as a development environment) and having it available online (usually described as a production environment), you would spend a lot of time switching the URLs back and forth.

It would be better to have a variable that is set to the correct URL depending on your situation, so you only have to update it in one place if it changes.

There are a few ways to do this, depending on the goals (amount of safety, other app features). As you build different apps, you may see different approaches.

Follow the documentation for the type of app you are deploying. For Vite:

- Create a `.env` on the same level as `package.json`.
- Start all variable names with `VITE_`.
- `VITE_API_URL` or `VITE_MY_VAR` are acceptable names.
- `Vite_My_Var` is NOT going to work.

```
# Example .env
VITE_API_URL=http://localhost:3333
```

### Loading the Colors Index on Page Load

With React, you sometimes have to think about _when_ things happen. You want to make an API call to get your data AFTER the component has been fully loaded (mounted in the DOM) and then update the state.

If you made the API call first, the DOM was not fully loaded, and the state was updated first, you would risk not seeing your data, even though the API call was successful.

Inside the `useEffect` function, you will add your initial API call to get all of the resource.

- Make a `GET` request using fetch. `then` when there is a `response`, do something. In your case, you want to update state by using the `setColors` that was declared at the beginning of the `App` component. The state will be updated to be your array of color objects.

```js
// src/Components/colors
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

Try to build from the outside-in, instead of left to right. Add the callback.

```js
useEffect(() => {
  fetch(`${API}/colors`).then(() => {});
}, []);
```

The thing you are waiting on is the response. The response comes in as has a lot of extra information. You only need the colors data. Compare and contrast the info given in the two console colors:

```js
useEffect(() => {
  fetch(`${API}/colors`).then((response) => {
    console.log(response.body);
  });
}, []);
```

When you look at the response body here, you will see it is of type `ReadableStream`. Next you must convert the value to JSON and _then_ return the value.

```js
useEffect(() => {
  fetch(`${API}/colors`)
    .then((response) => {
      return response.json();
    })
    .then((responseJSON) => console.log(responseJSON));
}, []);
```

Rather than console logging, set state:

```js
useEffect(() => {
  fetch(`${API}/colors`)
    .then((response) => {
      return response.json();
    })
    .then((responseJSON) => setColors(responseJSON))
    .catch((error) => console.error(error));
}, []);
```

Add error handling:

```js
useEffect(() => {
  fetch(`${API}/colors`)
    .then((response) => {
      return response.json();
    })
    .then((responseJSON) => setColors(responseJSON))
    .catch((error) => console.error(error));
}, []);
```

**Uh Oh!** We found a ~~bug~~ feature!

![](./assets/cors-error.png)

The React app is making a request from a different origin than where the API is running. This is a great security feature! By default, requests coming from other origins are not allowed.

However, you want to allow the React app to make requests to the API.

Go to the Express API to set up this allowance.

## Cors

### Express API

- Return to your Express backend, make sure you are on the same level as `package.json`

- `npm install cors@2`

Require it:

```js
// app.js
// near the top
const cors = require("cors");
```

Configure it:

```js
// app.js
// near app.use(express.json())
app.use(cors());
```

This will allow ANY app to make requests to your API. Since you are just building a sample app and not hosting it online, you don't have to worry about restricting it. Later, when your app is deployed to the cloud, you would want to be more thoughtful about how to properly allow/restrict access.

> **Note**: Also,confirm you do not have a function that blocks the POST route, unless there is an apikey. You may remove or comment out this function for this demo.

## Show

Let's get the Show page, New, and Edit forms, and delete button working.

|  #  | Action |       URL        | HTTP Verb |   CRUD   |               Description               |
| :-: | :----: | :--------------: | :-------: | :------: | :-------------------------------------: |
|  1  | Index  |     /colors      |    GET    | **R**ead |   Get a list (or index) of all colors   |
|  2  |  Show  |   /colors/:id    |    GET    | **R**ead | Get an individual view (show one color) |
|  3  |  New   |   /colors/new    |    GET    | **R**ead |   Get a list (or index) of all colors   |
|  4  |  Edit  | /colors/:id/edit |    GET    | **R**ead | Get an individual view (show one color) |

### Show Page

You will have to request one color from the API from the show page.

You would use `useParams` from react-router-dom. This will allow us to use the URL parameters (in the app, this will be the index position of the array).

When you are on the index view at `/colors`, you will have a link to click on a specific color. Usually, this link would be the item's unique id. Since you don't have a database yet, you can use the array position.

Imagine you click on the color `cornsilk` and it has an array position of 2. The URL would be `/colors/{index}`. You will use React Router's `Link` component.

```js
<Link to={`/colors/${index}`}>{color.name}</Link>
```

The function for Show will be very similar. However, we'll add an error message if the particular color cannot be found. We'll go to `/not-found`, an invalid index position that will trigger the 404 route. It still could use even better UI/UX, but this will do for this small build. As a challenge during lab, you can work on making this an even more excellent experience.

_Reminder:_ A promise is a function that allows you to _WAIT_ for a response and _THEN_ do something. The .`then()` function takes a callback, and within that callback, you can write code that should run AFTER the first function has been fulfilled (usually by returning a value).

If you pass an argument into `.then()`, it is the return value from the previous function.

```js
// src/Components/ColorDetails.js
// On page load, load color details
useEffect(() => {
  fetch(`${API}/colors/${index}`)
    .then((response) => {
      return response.json();
    })
    .then((responseJSON) => {
      setColor(responseJSON);
    })
    .catch(() => {
      navigate("/not-found");
    });
}, [index, navigate]);
```

Notice that there are three buttons: back, edit, and delete. This has more functionality than the index view. You'll build out edit and delete shortly.

## New

When we think of our users, they want to create a color and then want to see the success that their color has been created. So the flow will be:

- A user fills out the create form.
- Presses the submit button.
- Submit sends a POST request to the Express API.
- Upon successful request, redirect the user back to the index view, where they will see their color added as the last item.
- If there is an error, there will be a message in the console (again, during lab time, you can build a component/use conditional rendering to provide a better user experience, we won't do this now in the interest of time).

There is a function `handleSubmit`. This is the function that gets called when the form is submitted.

First, we must prevent the default. When the form does not have the attributes of `action` and `method`, it will default to refreshing the page.

After that, we want to write some functionality to make a POST request and send the form data to our backend. THEN, once the request is complete, we want to navigate the user back to the Index page so they can see that their new color has been added.

It is critical to:

- Set the method to POST (by default fetch requests are type GET)
- Set the headers to have a `Content-Type` of `application/json` - Convert the color object coming from the form is converted into a string.

Put it all together:

```js
const addBookmark = () => {
  fetch(`${API}/colors`, {
    method: "POST",
    body: JSON.stringify(color),
    headers: {
      "Content-Type": "application/json",
    },
  })
    .then(() => {
      navigate(`/colors`);
    })
    .catch((error) => console.error("catch", error));
};

// Make sure to call addBookmark in handleSubmit
const handleSubmit = (event) => {
  event.preventDefault();
  addBookmark();
};
```

## Edit

You'll find the Edit view to be very similar to the New view. Two key differences is that the form should be pre-loaded with data from the item to be edited and the action taken upon form submit.

For now, you will have a separate new and edit form, but if you are looking to challenge yourself, you can work on creating one form that works for both routes.

Add the fetch request to update a color

```js
// src/Components/ColorEditForm.jsx
// Update a color. Redirect to show view
const updateColor = () => {
  fetch(`${API}/colors/${index}`, {
    method: "PUT",
    body: JSON.stringify(color),
    headers: {
      "Content-Type": "application/json",
    },
  })
    .then(() => {
      navigate(`/colors/${index}`);
    })
    .catch((error) => console.error("catch", error));
};
```

Make sure to call this function in the `handleSubmit()` function.

## Delete

Delete does not need a view. Delete is sufficient as a button.

You can put the delete button in the Show view

```js
const handleDelete = () => {
  fetch(`${API}/colors/${index}`, { method: "DELETE" })
    .then(() => {
      navigate(`/colors`);
    })
    .catch((e) => console.error(e));
};
```

[See the completed build](https://github.com/pursuit-curriculum-resources/pre-reading-connect-react-demo/tree/solution)
