# Express RESTful Routes: Update & Delete

## POSTMAN

## Test Current Routes

|  #  |   Action   |      URL       | HTTP Verb |    CRUD    |                Description                 |
| :-: | :--------: | :------------: | :-------: | :--------: | :----------------------------------------: |
|  1  | **Create** |   /bookmarks   |   POST    | **C**reate |           Create a new bookmark            |
|  2  | **Index**  |   /bookmarks   |    GET    |  **R**ead  |   Get a list (or index) of all bookmarks   |
|  3  |  **Show**  | /bookmarks/:id |    GET    |  **R**ead  | Get an individual view (show one bookmark) |

## Index

![Get all bookmarks](../assets/postman-index.png)

- Set the option to `GET`.
- Enter the URL for your bookmarks URL for the index (e.g., http://localhost:3003/bookmarks).
- Press `SEND`.

Below, you should see the response.

## Show One

- Set the option to `GET`.
- Enter the URL for your bookmarks URL for one bookmark (e.g., http://localhost:3003/bookmarks/1).
- Press `SEND`.

## Create

![Create bookmark](../assets/postman-create.png)

- Update the option to `POST`.
- Change the URL back to http://localhost:3003/bookmarks.
- Choose `body`.
- Choose `raw`.
- Select `JSON`.
- In the text area, create a proper JSON object (double quotes around all the key and value pairs, no trailing commas ).

```js
{
 "name": "Pursuit",
 "url": "https://www.pursuit.org/",
 "isFavorite": true,
 "category": "education"
}
```

Press send. Our API works by returning us to the GET route, and we should see all our bookmarks, including our newest one, at the bottom. **Thought question:** how could we update our API to show the newest item as the first item in the array?

## Delete

| Action  |      URL       | HTTP Verb |    CRUD    |    Description    |
| :-----: | :------------: | :-------: | :--------: | :---------------: |
| Destroy | /bookmarks/:id |  DELETE   | **D**elete | Delete a bookmark |

Let's add delete functionality. Again, this functionality will only be available to us via form. So we'll use Postman to test it.

We will use the index position of the array item and splice out the deleted item, which will remove the item at that array position. Then, we will send the deleted bookmark back.

```js
// DELETE
bookmarks.delete("/:indexArray", (req, res) => {
  const deletedBookmark = bookmarkArray.splice(req.params.indexArray, 1);
  res.status(200).json(deletedBookmark);
});
```

### TEST WITH POSTMAN

- Update the option to `DELETE`.
- Change the URL back to http://localhost:3003/bookmarks/0
- Send

You should see the updated bookmarks array without the item that was in array position 0

Let's add some error handling.

```js
// DELETE
bookmarks.delete("/:indexArray", (req, res) => {
  if (bookmarkArray[req.params.arrayIndex]) {
    const deletedBookMark = bookmarkArray.splice(req.params.indexArray, 1);
    res.status(200).json(deletedBookMark);
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
  bookmarkArray[req.params.arrayIndex] = req.body;
  res.status(200).json(bookmarkArray[req.params.arrayIndex]);
});
```

Let's also make sure that the user enters a valid URL again. Let's reuse the function we built earlier and add some error handling.

```js
// UPDATE
bookmarks.put("/:arrayIndex", validateURL, async (req, res) => {
  if (bookmarkArray[req.params.arrayIndex]) {
    bookmarkArray[req.params.arrayIndex] = req.body;
    res.status(200).json(bookmarkArray[req.params.arrayIndex]);
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

## Summary

You did it!

You built your first API with ExpressJS!
You have a simple app that lets users keep track of their favorite websites. Users can create, read, update and delete bookmarks.

Unfortunately, asking users to make updates using Postman isn't going to give your app any wow factor.

For some wow factor, we'll need to build a front end.
