# Express RESTful Routes: Update & Delete

## Learning Objectives

By the end of this lesson, you should be able to:

- Make requests to a locally running server via Postman.
- Build a complete RESTful API that responds to a valid request with appropriate messages and status codes.

---

## POSTMAN

Even though the command line is powerful and you can do amazing things like this: `curl -s -L http://bit.ly/10hA8iC | bash` ( <kbd>control</kbd> <kbd>c</kbd> to cancel it), it can end up being a LOT of typing, especially when one wants to test a back-end thoroughly.

So, some developers built a GUI (Graphical User Interface) to allow developers to test their back-ends called Postman.

If you haven't already, [download Postman](https://www.postman.com/downloads/).

Once you open the app, it will ask you to log in. However, looking carefully, you can find a `skip` option in tiny letters.

Postman is a very robust tool with a lot of functionality. You'll just be using it in a very simple way for now, and you'll learn a bit more useful things as you keep building out the app.

First, start by testing the three routes you've built:

### Test Current Routes

|  #  |   Action   |     URL     | HTTP Verb |    CRUD    |               Description               |
| :-: | :--------: | :---------: | :-------: | :--------: | :-------------------------------------: |
|  1  | **Create** |   /colors   |   POST    | **C**reate |           Create a new color            |
|  2  | **Index**  |   /colors   |    GET    |  **R**ead  |   Get a list (or index) of all colors   |
|  3  |  **Show**  | /colors/:id |    GET    |  **R**ead  | Get an individual view (show one color) |

### Index

![Get all colors](./assets/postman-index.png)

- Set the option to `GET`.
- Enter the URL for your colors URL for the index (e.g., http://localhost:3003/colors).
- Press `SEND`.

Below, you should see the response.

### Show One

- Set the option to `GET`.
- Enter the URL for your colors URL for one color (e.g., http://localhost:3003/colors/1).
- Press `SEND`.

### Create

![Create color](./assets/postman-create.png)

- Update the option to `POST`.
- Change the URL back to http://localhost:3003/colors.
- Choose `body`.
- Choose `raw`.
- Select `JSON`.
- In the text area, create a proper JSON object (double quotes around all the key and value pairs, no trailing commas ).

```js
{
 "name": "cornsilk",
}
```

Press send. The API works by returning to the GET route, and you should see all the colors, including the newest one, at the bottom. **Thought question:** How could you update the API to show the newest item as the first item in the array?

### Delete

| Action  |     URL     | HTTP Verb |    CRUD    |  Description   |
| :-----: | :---------: | :-------: | :--------: | :------------: |
| Destroy | /colors/:id |  DELETE   | **D**elete | Delete a color |

Let's add delete functionality. Again, this functionality will only be available via an HTML form if you use a browser. So we'll use Postman to test it.

We will use the index position of the array item and splice out the deleted item, which will remove the item at that array position. Then, you will send the deleted color back.

```js
// DELETE
colors.delete("/:arrayIndex", (req, res) => {
  const { arrayIndex } = req.params;
  const deletedBookmark = colorsArray.splice(req.params.indexArray, 1);
  res.status(200).json(deletedBookmark[0]);
});
```

#### TEST WITH POSTMAN

- Update the option to `DELETE`.
- Change the URL back to http://localhost:3003/colors/0
- Press `send`.

You should see the updated colors array without the item that was in array position 0.

Add some error handling.

```js
// DELETE
colors.delete("/:indexArray", (req, res) => {
  const { indexArray } = req.params;
  if (colorsArray[indexArray]) {
    const deletedColor = colorsArray.splice(indexArray, 1);
    res.status(200).json(deletedColor[0]);
  } else {
    res.status(404).json({ error: "Not Found" });
  }
});
```

You can instead, choose to redirect after successful delete:

```js
// DELETE
colors.delete("/:indexArray", (req, res) => {
  const { indexArray } = req.params;
  if (colorsArray[indexArray]) {
    colorsArray.splice(indexArray, 1);
    res.redirect("/colors");
  } else {
    res.status(404).json({ error: "Not Found" });
  }
});
```

### Update

| Action |     URL     | HTTP Verb |    CRUD    |  Description   |
| :----: | :---------: | :-------: | :--------: | :------------: |
| Update | /colors/:id |    PUT    | **U**pdate | Update a color |

You will take the array position of the item you want to update. You will set the value as the incoming `req.body`.

```js
// UPDATE
colors.put("/:arrayIndex", (req, res) => {
  const { arrayIndex } = req.params;
  colorsArray[arrayIndex] = req.body;
  res.status(200).json(colorsArray[arrayIndex]);
});
```

You can reuse the middleware functionality to check that the updated value has a `name` key.

```js
// UPDATE
colors.put("/:arrayIndex", checkForColorKey, (req, res) => {
  const { arrayIndex } = req.params;
  colorsArray[arrayIndex] = req.body;
  res.status(200).json(colorsArray[arrayIndex]);
});
```

#### TEST WITH POSTMAN

- Update the option to `PUT`.
- http://localhost:3003/colors/0
- Choose `body`.
- Choose `raw`.
- Select `JSON`.
- In the text area, create a proper JSON object (double quotes around all the key and value pairs, no trailing commas )

An easy thing to test is to add many exclamation points at the end of the property's `name`.

![Postman Update](./assets/postman-update.png)

## Summary

You did it!

You built your first single-model full CRUD RESTful API with Express!

You have a simple app that stores HTML color names. Users can create, read, update, and delete colors.

Unfortunately, asking users to make updates using Postman isn't give your app any wow factor.

For some wow factor, you'll need to build a front-end.
