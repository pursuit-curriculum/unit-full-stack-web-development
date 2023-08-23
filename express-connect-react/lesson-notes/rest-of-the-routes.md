# Express Connect React

## Learning Outcomes

- Complete the remaining RESTful Routes
- Show
- New
- Edit
- Delete

## Continue

So far, we have added functionality to our React app to complete requests and get responses for the Index resource.

Let's get the Show page, New, Edit forms working and a delete button.

|  #  | Action |         URL         | HTTP Verb |    CRUD    |                Description                 |
| :-: | :----: | :-----------------: | :-------: | :--------: | :----------------------------------------: |
|  1  | Index  |     /bookmarks      |    GET    |  **R**ead  |   Get a list (or index) of all bookmarks   |
|  2  |  Show  |   /bookmarks/:id    |    GET    |  **R**ead  | Get an individual view (show one bookmark) |
|  3  |  New   |   /bookmarks/new    |    GET    |  **R**ead  |   Get a list (or index) of all bookmarks   |
|  4  |  Edit  | /bookmarks/:id/edit |    GET    |  **R**ead  | Get an individual view (show one bookmark) |
|  5  | Delete |   /bookmarks/:id    |  DELETE   | **D**elete | Get an individual view (show one bookmark) |

### Loading a Bookmark on Page Load (Show Page)

<details><summary>Show Page Empty</summary>

![](../assets/show-empty.png)

</details>

Let's request one bookmark from our API from the show page.

**src/Components/BookmarkDetails.js**

At the top:

```js
const API = import.meta.env.VITE_API_URL;
```

- What does the react-router-dom function `userParams` do?
- How will we incorporate URL params into the Show route?
- How can you create a 404 view in React using React Router?

Inside the `BookmarkDetails` function, update the line of code to be:

```js
// src/Components/BookmarkDetails.js
// Inside the function
const [bookmark, setBookmark] = useState([]);
```

Add useNavigate so we can use the browser's forward and back buttons. [useNavigate api](https://reactrouter.com/docs/en/v6/api#navigation).

Let's fetch one bookmark based on the index position. If the index position is invalid or not found, it will trigger the error callback, in which case, we will send the users to the 404 page.

Set up a request on page load:

```js
// src/Components/BookmarkDetails.js
// Inside the function
useEffect(() => {
  fetch(`${API}/bookmarks/${index}`).then((response) => {
    setBookmark(response.data);
  });
}, [index]);
```

Import `useNavigate`.

```js
// src/Components/BookmarkDetails.js
// At the top
import { Link, useParams, useNavigate } from "react-router-dom";
```

Configure `useNavigate`.

```js
// src/Components/BookmarkDetails.js
// Inside the function
let navigate = useNavigate();
```

Navigate to the 404 view, if a bookmark is not found.

```js
useEffect(() => {
  fetch(`${API}/bookmarks/${index}`)
    .then((response) => {
      return response.json();
    })
    .then((responseJSON) => {
      setBookmark(responseJSON);
    })
    .catch(() => {
      navigate("/not-found");
    });
}, [index, navigate]);
```

<details><summary>Show Page Loaded</summary>

![](../assets/show-loaded.png)

</details>

<br />

## Using the Create Form to Create a new Bookmark

<br />

When we think of our users, they want to create a bookmark and then want to see the success that their bookmark has been created. So the flow will be:

- A user fills out the create form.
- Presses the submit button.
- Submit sends a POST request to the express API.
- Upon successful request, redirect the user back to the index view, where they will see their bookmark added as the last item.
- If there is an error, there will be a message in the console (again, during lab time, you can build a component/use conditional rendering to provide a better user experience, we won't do this now in the interest of time).

```js
// src/BookmarkNewForm.jsx
const API = import.meta.env.VITE_API_URL;
```

Add `useNavigate` so that it navigates back to the index page when a new bookmark is created.

```js
// src/BookmarkNewForm.jsx
import { useNavigate } from "react-router-dom";

const navigate = useNavigate();
```

There is a function `handleSubmit`. This is the function that gets called when the form is submitted.

- Why does `event.preventDefault()` need to be called?
- Why not use a regular button with an `onClick()` event?
- What is the default behavior of a form that does not have an `action` and `method` attributes set?
- Why is there a generic `handleSubmit` function?
- Is there a case where you might want to reuse the form in the future?

- How do you set the method to POST with `fetch()`?
- What is the default action of `fetch()`
- When making a `POST` request do you have to set headers?
- What kind of data (URL encoded, JSON, XML ...) will you send to the back-end?
- How can you "inform" the back-end of the type of data you will send?
- Can you send a JavaScript object?
- If you can't send an objet, what kind of datatype can you send?
- How can you convert a JavaScript object into the correct datatype?

- Imagine being a user, what would you expect would happen after a successful submission?
- How can you build the app to give the user some feedback?
- In the interest of time, you will only log an error. However, what is the experience for the user like if their POST request does not go through? What would make a better experience for a user that doesn't know to open dev tools and look through errors?

Put it all together:

```js
// src/Components/BookmarkNewForm.jsx
const addBookmark = () => {
  fetch(`${API}/bookmarks`, {
    method: "POST",
    body: JSON.stringify(bookmark),
    headers: {
      "Content-Type": "application/json",
    },
  })
    .then(() => {
      navigate(`/bookmarks`);
    })
    .catch((error) => console.error("catch", error));
};

// Make sure to call addBookmark in handleSubmit
const handleSubmit = (event) => {
  event.preventDefault();
  addBookmark();
};
```

<br>

### Using the Edit Form to Update a Bookmark

The edit form is very similar to the create form. However, for a better user experience, it should be pre-filled with the values rather than requiring the user to type everything from scratch.

At the top:

```js
// src/Components/BookmarkEditForm.js
import { useParams, Link, useNavigate } from "react-router-dom";
const API = import.meta.env.VITE_API_URL;
```

```js
// inside BookmarkEditForm function
const navigate = useNavigate();
```

```js
// inside BookmarkEditForm function
useEffect(() => {
  fetch(`${API}/bookmarks/${index}`)
    .then((response) => {
      return response.json();
    })
    .then((responseJSON) => {
      setBookmark(responseJSON);
    })
    .catch((error) => console.error(error));
}, [index]);
```

Now, your form should be pre-loaded with the bookmark data.

<details><summary>Edit Form Loaded</summary>

![](../assets/edit-form-loaded.png)

</details>

Add the functionality to set a PUT request and update our API. Then we'll have the user return to the show page of the item they updated.

```js
const updateBookmark = () => {
  fetch(`${API}/bookmarks/${index}`, {
    method: "PUT",
    body: JSON.stringify(bookmark),
    headers: {
      "Content-Type": "application/json",
    },
  })
    .then(() => {
      navigate(`/bookmarks/${index}`);
    })
    .catch((error) => console.error("catch", error));
};
```

Call updateBookmark inside `handleSubmit()`

```js
const handleSubmit = (event) => {
  event.preventDefault();
  updateBookmark();
};
```

- The new and edit forms are very similar. If you are ready for a challenge you can try to use one form component for both actions.

## Adding Delete Functionality

For delete, we can just add the functionality on the show page. No separate page is needed.

**src/Components/BookmarkDetails.js**

```js
const handleDelete = () => {
  fetch(`${API}/bookmarks/${index}`, { method: "DELETE" })
    .then(() => {
      navigate(`/bookmarks`);
    })
    .catch((e) => console.error(e));
};
```

## Summary

We have created a full CRUD full-stack application using an express backend and a create-react-app front end. We utilized all 7 RESTful routes.

[See the complete build here](https://github.com/pursuit-curriculum-resources/starter-express-connect-react/tree/solution)
