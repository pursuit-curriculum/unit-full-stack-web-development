# Express Connect React

## Introduction

You now have a nicely working back-end API that uses Express to serve JSON with full CRUD. Unfortunately, asking users to use Postman or cURL to use your app isn't the best user experience for non-developers. You need to build a front-end.

> **Note**: This lesson is designed to span two lesson blocks rather than the usual one. A good stopping point is after completing the Index route, then completing the rest in the follow-up lesson. Check in with your instructor(s) to be sure this will be the split they are planning.

## Learning Outcomes

By the end of this lesson, you should be able to:

- Be able to connect a React application to an Express back-end API.
- Be able to describe all 7 RESTful views.
- Be able to set a base URL that can be easily updated, depending on the local environment, and describe why it is important to do so.
- Be able to describe why an easily updated URL is useful
- View an index route with data from the express API.
- Be able to describe what CORS is and why the default is to block cross-origin requests.

---

## The Seven RESTful Routes

The seven RESTful routes are the classic example of a RESTful pattern. Before front-end applications like React became more standard, servers handled creating HTML views for a website. That means that there are two additional views: `New`, which is a view with a form for users to fill out and submit `POST` requests, and `Edit`, which is a view with a form for users to fill out and submit `PUT` requests. And specific routes like `Create`, `Delete` and `Update` don't have views, you will only trigger these routes through form submission or button click.

While this pattern is classic, and you will follow it exactly for learning fundamentals, you will see many variations in the wild. These variations are built to meet an application's specific needs and improve user experience.

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

All the front-end routes are views, and all require GET requests to allow a user to read data or access a form.

There are no POST, PUT, or DELETE routes that have views.

</details>

## Optional Code-along

You may read the examples or code along.

A [React App with starter code is available for this code along](https://github.com/pursuit-curriculum-resources/pre-reading-connect-react-demo). The important details to note:

- All the routes and routing are set up in `src/App.jsx`.
- Each view will have a component in the `src/Pages` folder.
- Additional components will be in the `src/Components` folder.
- The API calls are the only parts of the app that need to be built.

While the `Pages` components will be very lean and likely have one component, you could imagine each view would be far more complex for a professional application.

### The Base URL

Before you start, you have to think about a problem you will have. If you hard code `http://localhost:3003` into every fetch request of your React app, when you deploy your app online, you must change every single instance of `http://localhost:3003`. Further, if you decide to host your app on another service later, you must change it again. And, if you are working on your app locally (usually described as a development environment) and having it available online (usually described as a production environment), you would spend a lot of time switching the URLs back and forth if they are all hard-coded.

It would be better to set a variable to the correct URL depending on the environment so you only have to update it in one place when it changes.

Follow the [documentation for the type of app you are deploying. For Vite:](https://vitejs.dev/guide/env-and-mode.html)

- Create a `.env` on the same level as `package.json`.
- Start all variable names with `VITE_`.
- `VITE_API_URL` or `VITE_MY_VAR` are acceptable names.
- `Vite_My_Var` is NOT going to work.

```
# Example .env
VITE_API_URL=http://localhost:3333
```

### Loading the Colors Index on Page Load

You sometimes have to think about _when_ things happen with React. You want to make an API call to get your data AFTER the component has been fully loaded (mounted in the DOM) and then update the state.

If you made the API call first, without waiting on the DOM, React would not be able to update state correctly. Because of this, React has a built-in function called `useEffect()` that helps you control when state should be updated.

Inside the `useEffect` function, you will add your initial API call to get all the resources.

- Make a `GET` request using fetch. `then` when there is a `response`, do something. In your case, you want to update the state using the `setColors` declared in the `App` component. The state will be updated to be your array of color objects.

```js
// src/Components/Colors.jsx
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

Try to build from the outside-in instead of left to right. Add the callback.

```js
useEffect(() => {
  fetch(`${API}/colors`).then(() => {});
}, []);
```

The thing you are waiting on is the response. The response comes in as it has a lot of extra information. You only need the colors data. Compare and contrast the info given in `response` versus `response.body`:

```js
useEffect(() => {
  fetch(`${API}/colors`).then((response) => {
    console.log(response.body);
  });
}, []);
```

When you look at the response body here, you will see it is of type `ReadableStream`. Next, you must convert the value to JSON and _then_ return the value.

```js
useEffect(() => {
  fetch(`${API}/colors`)
    .then((response) => {
      return response.json();
    })
    .then((responseJSON) => console.log(responseJSON));
}, []);
```

Rather than console logging, set the state:

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

However, you want to allow your React app to make requests to your API.

Go to the Express API to set up this allowance.

## CORS

So far, you've been building simple apps to learn the basics of building full-stack applications. One topic that has not been addressed is protecting your applications from hackers and other malicious or unauthorized usage of your applications.

An example of this is imagine you have been working on a banking application - let's call it Bank-A. In it, you have security measures like passwords. Bank-A contains a lot of sensitive information: name, address, social security number, bank account numbers, and more. A malicious actor could make a website called Bank-B that looks like Bank-A. If a user mistakenly goes to Bank-B, they can enter their login information into Bank-B. Bank-B can then use the user input, create a login request to Bank-A, capture all the information, and make additional requests to transfer money or delete accounts.

One way to prevent this is to build in an extra request before each request. This request is typically referred to as a [preflight request](https://developer.mozilla.org/en-US/docs/Glossary/Preflight_request). This preflight request checks several things, including the server origin against the client origin. If the origins are different, the actual request is rejected before it reaches its intended route.

In this case, when preflight checks are set up, Bank-A will automatically reject any requests from Bank-B because the origin is different than expected. This automatic cross-origin blocking is called [`Cross-Origin Resource Sharing` or CORS for short](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).

Suppose you have a situation where you have two different applications (like React and Express), and you need to allow Cross-Origin requests just between these two applications. In that case, you can set up your server to accept these requests from specific origins using the npm package `cors`.

### Add CORS to your Express API

- Return to your Express back-end, and make sure you are on the same level as `package.json`

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

This configuration (`cors()` function without arguments) will allow ANY app at any origin to request your API. Since you are just building a sample app and not hosting it online, you don't have to worry about restricting it. Later, when your app is deployed to the cloud, you would want to be more thoughtful about how to allow/restrict access properly.

> **Note**: If you are coding along and are using a previous build of the API, confirm you do not have a function that blocks the POST route unless there is an `apikey`. You may remove or comment on this function for this demo.

## Show

Review the four views you are building. This section will demonstrate the show view.

|  #  | Action |       URL        | HTTP Verb |   CRUD   |               Description               |
| :-: | :----: | :--------------: | :-------: | :------: | :-------------------------------------: |
|  1  | Index  |     /colors      |    GET    | **R**ead |   Get a list (or index) of all colors   |
|  2  |  Show  |   /colors/:id    |    GET    | **R**ead | Get an individual view (show one color) |
|  3  |  New   |   /colors/new    |    GET    | **R**ead |   Get a list (or index) of all colors   |
|  4  |  Edit  | /colors/:id/edit |    GET    | **R**ead | Get an individual view (show one color) |

You must request one color from the API from the show page. To get one color, you must request it by a specific parameter. When using a database, you would typically use the object's id. However, in this simple example, you can use the index position of the array of objects stored in the server.

You would build a link to each item's show route on the index page.

Then would use `useParams` from react-router-dom. This will allow you to access and use the URL parameters of the view to get the data for the specific color.

Imagine you click on the color `cornsilk`, which has an array position of 2. The URL would be coded to be dynamic `/colors/{index}`. When the link is generated, it will be `/colors/2`. You will use React Router's `Link` component to navigate to the show view.

```js
<Link to={`/colors/${index}`}>{color.name}</Link>
```

The fetch request for the show view will be similar to the index request. However, you'll add an error message if the particular color cannot be found. It will go to `/not-found`, an invalid index position that will trigger the 404 view. It still could use even better UI/UX. As a challenge during lab, you can work on making this an even more excellent experience.

> **Note**: A promise is a function that allows you to _WAIT_ for a response and _THEN_ do something. The .`then()` function takes a callback, and within that callback, you can write code that should run AFTER the first function has been fulfilled (usually by returning a value).

> **Note**: If you pass an argument into `.then()`, it is the return value from the previous function.

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

Notice that this view also has three buttons: back, edit, and delete. You'll build-out edit and delete shortly.

A user can use the back button in the browser. Why would you want to include a" back " button that is in the browser view?

## New

When you think of your users, when they want to create a color, they also want to see the success that their color has been created. So the flow will be:

- A user fills out the create form.
- Presses the submit button.
- On submit, fetch sends a POST request to the Express API.
- Upon successful request, redirect the user back to the index view, where they will see their color added as the last item.
- If there is an error, there will be a message in the console (again, during lab time, you can build a component/use conditional rendering to provide a better user experience, you won't do this now in the interest of time).

There is a function `handleSubmit()`. This is the function that gets called when the form is submitted.

You can design inputs and other form elements without a form element with React. However, it is vital to use a form element for web accessibility and best practices. Forms allow for things like tabbing across fields and using the enter key as an alternative to a click event on a button. For people who use screen readers, even more functionality is built to allow them to use the app.

Back to building the form: first, you must prevent the default. When the form does not have the attributes of `action` and `method`, it will default to refreshing the page.

After setting `preventDefault()`, you want to write some functionality to make a POST request and send the form data to the back-end. Then, once the request is complete, you want to navigate the user back to the Index page so they can see that their new color has been added.

It is critical to:

- Set the method to POST (by default, fetch requests are type GET).
- Set the headers to a key value of `Content-Type` of `application/json`.
- Convert the color object from the form to a string.
- Redirect the user back to the index view where they can see their newly created color.

Put it all together:

```js
// src/Components/ColorNewForm.jsx
// Add a color, return to the index view
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

You'll find the Edit view very similar to the New view. Two key differences are that the form should be pre-loaded with data from the item to be edited, and the action taken upon form submission will be a PUT request rather than a POST request.

For now, you will have a separate new and edit form, but if you are looking to challenge yourself, you can work on creating one form that works for both routes.

Add the fetch request to update a color:

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

You can put the delete button in the Show view.

```js
// src/Components/ColorDetails.jsx
const handleDelete = () => {
  fetch(`${API}/colors/${index}`, { method: "DELETE" })
    .then(() => {
      navigate(`/colors`);
    })
    .catch((error) => console.error(error));
};
```

[See the completed build](https://github.com/pursuit-curriculum-resources/pre-reading-connect-react-demo/tree/solution)

## Extra Reading

Domino's Pizza was sued for not having an accessible website and lost the case. They must now make their [website accessible](https://www.cnbc.com/2019/10/07/dominos-supreme-court.html). Building accessible websites is integral to building professional applications.
