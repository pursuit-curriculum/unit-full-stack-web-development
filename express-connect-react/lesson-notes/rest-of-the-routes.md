# Express Connect React

## Learning Outcomes

- Complete the remaining RESTful Routes
- Show
- New
- Edit
- Delete

## Continue

So far, we have added functionality to our React app to complete requests and get responses for Index.

Let's get the Show page, New, and Edit forms working.

|  #  | Action |         URL         | HTTP Verb |   CRUD   |              Description               |
| :-: | :----: | :-----------------: | :-------: | :------: | :------------------------------------: |
|  1  | Index  |     /bookmarks      |    GET    | **R**ead | Get a list (or index) of all bookmarks |
|  2  |  Show  |   /bookmarks/:id    |    GET    | **R**ead | Get an individual view (show one log)  |
|  3  |  New   |   /bookmarks/new    |    GET    | **R**ead | Get a list (or index) of all bookmarks |
|  4  |  Edit  | /bookmarks/:id/edit |    GET    | **R**ead | Get an individual view (show one log)  |

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

We are also using `useParams` from react-router-dom. This will allow us to use the URL parameters (in our app, this will be the index position of the array)

Our function for Show will be very similar. However, we'll add an error message if the particular bookmark cannot be found. We'll go to `/not-found`, an invalid index position that will trigger the 404 route. It still could use even better UI/UX, but this will do for our small build. As a challenge during lab, you can work on making this an even more excellent experience.

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

### Add a Way to Update State

**src/Components/BookmarkDetails.js**

Inside the `BookmarkDetails` function, update the line of code to be:

```js
const [bookmark, setBookmark] = useState([]);
```

Add useNavigate so we can use the browser's [useNavigate api](https://reactrouter.com/docs/en/v6/api#navigation)

Let's fetch one bookmark based on the index position. If the index position is invalid or not found, it will trigger the error callback, in which case, we will send the users to the 404 page.

```js
useEffect(() => {
  fetch(`${API}/bookmarks/${index}`).then((response) => {
    setBookmark(response.data);
  });
}, [index]);
```

```js
import { Link, useParams, useNavigate } from "react-router-dom";
```

Inside the `BookmarkDetails` function

```js
let navigate = useNavigate();
```

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

- A user fills out the create form
- Presses the submit button
- Submit sends a post request to the express API
- Upon successful request, we'll redirect the user back to the index view, where they will see their bookmark added as the last item
- If there is an error, there will be a message in the console (again, during lab time, you can build a component/use conditional rendering to provide a better user experience, we won't do this in the interest of time)

**src/BookmarkNewForm.js**

Add the URL for the API

```js
const API = import.meta.env.VITE_API_URL;
```

Add `useNavigate` so that it navigates back to the index page when a new bookmark is created.

```js
import { useNavigate } from "react-router-dom";

const navigate = useNavigate();
```

There is a function `handleSubmit`. This is the function that gets called when the form is submitted.

First, we must prevent the default. When the form does not have the attributes of `action` and `method`, it will default to refreshing the page.

After that, we want to write some functionality to make a POST request and send the form data to our backend. THEN, once the request is complete, we want to navigate the user back to the Index page so they can see that their new bookmark has been added.

It is critical

- Set the method to POST (by default fetch requests are type GET)
- To set the headers to have a `Content-Type` of `application/json` - Convert the bookmark object coming from the form is converted into a string.

Put it all together:

```js
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

**src/Components/BookmarkEditForm.js**

At the top:

```js
import { useParams, Link, useNavigate } from "react-router-dom";
const API = import.meta.env.VITE_API_URL;

// inside BookmarkEditForm function
const navigate = useNavigate();
```

```js
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

Let's add the functionality to set a PUT request and update our API. Then we'll have the user return to the show page of the item they updated.

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

Call updateBookmark inside `handleSubmit`

```js
const handleSubmit = (event) => {
  event.preventDefault();
  updateBookmark();
};
```

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
