# Express RESTful Routes: Update & Delete

# Express RESTful Routes: Update & Delete

## Learning Objectives

By the end of this lesson, you should be able to:

- Make requests to a locally running server via Postman.
- Build a complete RESTful API that responds to a valid request with appropriate messages and status codes.

---

## Postman

- What is Postman?

## Test Current Routes

- Why is it important to test your routes before building a front-end application?

|  #  |   Action   |      URL       | HTTP Verb |    CRUD    |                Description                 |
| :-: | :--------: | :------------: | :-------: | :--------: | :----------------------------------------: |
|  1  | **Create** |   /bookmarks   |   POST    | **C**reate |           Create a new bookmark            |
|  2  | **Index**  |   /bookmarks   |    GET    |  **R**ead  |   Get a list (or index) of all bookmarks   |
|  3  |  **Show**  | /bookmarks/:id |    GET    |  **R**ead  | Get an individual view (show one bookmark) |

## Create

![Create bookmark](../assets/postman-create.png)

- Why do you need to update the option to POST?

- What is the base URL to make the correct POST request for creating a new bookmark?

- What does choosing `body` do in Postman?

- What does choosing `raw` do in Postman?

- Why do you need to select `JSON`?

- Compare and contrast what you've done to set up this Postman request to the cURL request you made previously.

Here is an example JSON object to test with Postman:

```js
{
 "name": "Pursuit",
 "url": "https://www.pursuit.org/",
 "isFavorite": true,
 "category": "education"
}
```

- How does the API work? What is the response of a successful POST request?

- Could you redirect back to the `index` route?

- Can you think of any apps you use where you create something new and get back the new item?

- Can you think of any apps you use where you create something new and see the index route?

- Is one response type better than the other?

- How could we update our API to show the newest item as the first item in the array?

## Delete

- Return to your `bookmarks` app. (If your app is not working, you may clone down [the previous complete build and work from there](https://github.com/pursuit-curriculum-resources/bookmarks-express-demo/tree/middleware)).

| Action  |      URL       | HTTP Verb |    CRUD    |    Description    |
| :-----: | :------------: | :-------: | :--------: | :---------------: |
| Destroy | /bookmarks/:id |  DELETE   | **D**elete | Delete a bookmark |

- If you wanted to test this route in the browser, how could you do it?

- What does splice do in the following code block?

```js
// DELETE
bookmarks.delete("/:arrayIndex", (req, res) => {
  const { arrayIndex } = req.params;
  const deletedBookmark = bookmarksArray.splice(arrayIndex, 1);
  res.status(200).json(deletedBookmark[0]);
});
```

- Why return `deletedBookMark[0]` instead of `deletedBookmark`?

### TEST WITH POSTMAN

- Update the option to `DELETE`.
- Change the URL back to http://localhost:3003/bookmarks/0
- Send

Add some error handling.

- What does the following error handling do?

```js
// DELETE
bookmarks.delete("/:arrayIndex", (req, res) => {
  const { arrayIndex } = req.params;
  if (bookmarksArray[arrayIndex]) {
    const deletedBookMark = bookmarksArray.splice(arrayIndex, 1);
    res.status(200).json(deletedBookMark[0]);
  } else {
    res.status(404).json({ error: "Not Found" });
  }
});
```

## Update

| Action |      URL       | HTTP Verb |    CRUD    |    Description    |
| :----: | :------------: | :-------: | :--------: | :---------------: |
| Update | /bookmarks/:id |    PUT    | **U**pdate | Update a bookmark |

We will take the array position of the item we want to update. We will set the value as the incoming `req.body`.

```js
// UPDATE
bookmarks.put("/:arrayIndex", (req, res) => {
  const { arrayIndex } = req.params;
  bookmarksArray[arrayIndex] = req.body;
  res.status(200).json(bookmarksArray[arrayIndex]);
});
```

Let's also make sure that the user enters a valid URL again. Let's reuse the function we built earlier and add some error handling.

```js
// UPDATE
bookmarks.put("/:arrayIndex", validateURL, async (req, res) => {
  const { arrayIndex } = req.params;
  if (bookmarksArray[arrayIndex]) {
    bookmarksArray[arrayIndex] = req.body;
    res.status(200).json(bookmarksArray[arrayIndex]);
  } else {
    res.status(404).json({ error: "Not Found" });
  }
});
```

### TEST WITH POSTMAN

- Update the option to `PUT`.
- http://localhost:3003/bookmarks/0
- Choose `body`.
- Choose `raw`.
- Select `JSON`.
- In the text area, create a proper JSON object (double quotes around all the key and value pairs, no trailing commas )

An easy thing to test is to add many exclamation points at the end of the `name`.

![Postman Update](../assets/postman-update.png)

## Resources

[Finished build](https://github.com/pursuit-curriculum-resources/bookmarks-express-demo/tree/update-delete)
