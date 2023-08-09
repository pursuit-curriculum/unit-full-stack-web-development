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
- Open a new tab in terminal <kbd>command</kbd> <kbd>t</kbd>, do not shut down your bookmarks app.
- Note: if you end up having a lot of extra data from testing, restart your express API (restart nodemon).
- Fork the starter code in your class GitHub Organization called [starter-express-connect-react](https://github.com/pursuit-curriculum-resources/starter-express-connect-react).
- `git clone` the forked repository.
- `cd` to the directory where you cloned it.
- `npm install` to install dependencies already included in the `package.json`.
- `touch .env`.

Add the back-end URL to the React app's .env file:

```
# .env
VITE_API_URL=http://localhost:3003

# Or whichever port your bookmarks API is on
```

- Start the React app

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

- http://localhost:5173/bookmarks/1 to see an empty show page
- http://localhost:5173/bookmarks/1/edit to see an empty edit page

To get the data from the back-end API, we can use The [fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) much like we did when using third-party APIs with our React apps.

## Setting Up the App to Make Requests

**src/Components/Bookmarks.js**

At the top:

```js
const API = import.meta.env.VITE_API_URL;
```

Confirm it is able to get the environmental variable value:

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

- How can you set up your code so that the bookmarks load on page-load?

**src/Components/Bookmarks.js**

Add `useEffect` to the component. Update the line that imports `useState`.

```js
useEffect(() => {
  fetch(`${API}/bookmarks`);
}, []);
```

- Once the request is complete, `then`, what should happen?

- If there is an error, how can you handle it?

```js
.then((response)=> {})
.catch((error)=> {})
```

Put it together with `useEffect` and `fetch`.

Try to build from the outside-in, instead of left to right. Add the callback.

```js
useEffect(() => {
  fetch(`${API}/bookmarks`).then(() => {});
}, []);
```

First console log the incoming response:

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

- What does this error mean?

- What needs to be done to fix it?

### Express API

**Go to your Terminal**, make sure you are in the directory with the Express backend and on the same level as the `package.json`.

- `npm install cors@2`

Require it:

```js
// app.js
// Near the top
const cors = require("cors");
```

Configure it:

```js
// app.js
// Near the app.use(express.json())
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
