## Bookmarks and Reviews

**One** bookmark can have **many** reviews.

How did we set up this relationship in SQL?

<details><summary>Answer</summary>

We added foreign keys (the primary key `id` of the `one` to the `many`)

</details>

## Create Full CRUD with a New Model Reviews

- `cd` into your bookmarks app that you've been building

Let's add a new table for reviews. We want to add a couple of constraints.

- We only want reviews to be between 0 and 5
- We want to add the `REFERENCES` constraint, that means that reviews cannot be created without a bookmark `PRIMARY KEY` that exists in the `bookmarks` table
- We also want to delete all the reviews for a bookmark if the bookmark is deleted.

### SQL Part

Let's set up our tables and insert some seed data.

**db/schema.sql**

```sql
DROP TABLE IF EXISTS reviews;

CREATE TABLE reviews (
 id SERIAL PRIMARY KEY,
 reviewer TEXT,
 title TEXT,
 content TEXT,
 rating NUMERIC,
 CHECK (rating >= 0 AND rating <= 5),
 bookmark_id INTEGER REFERENCES bookmarks (id)
 ON DELETE CASCADE
);
```

```bash
psql -U postgres -f db/schema.sql
```

And insert some reviews. Note, since we are dropping the bookmarks table as well, when we insert the bookmarks again into a new table, we should get bookmarks with `id` of 1, 2 and 3

**db/seed.sql**

Copy and paste these:

```sql
INSERT INTO reviews (bookmark_id, reviewer, title, content, rating )
VALUES
('1', 'Evan', 'My Favorite', 'This website crushes it when it comes to awesome explanations', 3),
('2', 'Evan', 'My Favorite', 'This website crushes it when it comes to inspiring me', 3),
('3', 'Evan', 'My Least Favorite', 'This website crushes it when it comes to destroying my patience', 5),
('2', 'Juliana', 'I Love Going Here', 'I finally learned how to properly fold a fitted sheet', 5),
('2', 'David', 'Cool Site', 'But I got way into adding decorative pillows everywhere', 1),
('2', 'Mr. Mingo', 'So Helpful', 'I got some awesome recommendations for a ceiling fan and some spoons', 3),
('2', 'Alison', 'A lifesaver!','Helped me get my stove cleaner than I ever imagiend possible!', 4),
('3', 'Hannah', 'Insert Confetti Emoji Here', 'I survived 6 hours at the DMV!', 4),
('3', 'Gabi', 'My Friend Hannah', 'Gets a discount if I leave a positive review, so here it is', 5);
```

```bash
psql -U postgres -f db/seed.sql
```

**Remember** bookmark_id is a foreign key. It is the id of the bookmark that this review belongs to. An `id` for each review will be automatically generated, so we don't have to create it.

### Express/PG-promise

Let's create the RESTful routes for reviews and test them.

|     | Action  |     URL      | HTTP Verb |    CRUD    |               Description                |
| :-: | :-----: | :----------: | :-------: | :--------: | :--------------------------------------: |
|  1  | Create  |   /reviews   |   POST    | **C**reate |           Create a new review            |
|  2  |  Index  |   /reviews   |    GET    |  **R**ead  |   Get a list (or index) of all reviews   |
|  3  |  Show   | /reviews/:id |    GET    |  **R**ead  | Get an individual view (show one review) |
|  4  | Update  | /reviews/:id |    PUT    | **U**pdate |             Update a review              |
|  5  | Destroy | /reviews/:id |  DELETE   | **D**elete |             Delete a review              |

- `touch controllers/reviewsController.js`
- `touch queries/reviews.js`

Everything in these two files should be pretty familiar - the complete code is in the folder along with this README [reviews.js](./reviews.js) [reviewsController.js](./reviewsController.js) - if you need a reference, as your instructor codes out these routes with you; in case you get stuck. Your instructor will demonstrate this in a specific order that allows for testing the code often.

You should test all of the routes with Postman before moving on.

Don't forget to add this to your `app.js`.

```js
// app.js
// Reviews ROUTES
const reviewsController = require("./controllers/reviewsController.js");
app.use("/reviews", reviewsController);
```

### Don't forget to Test All Your routes

Here is a sample object

```js
{
 "reviewer":"Lou",
 "title": "Fryin Better",
 "content": "With the great tips and tricks I found here",
 "bookmark_id": "2",
 "rating": "4"
}
```

Be sure to git add, and commit.

Super! We now have two models with full CRUD. But how do we connect them?

## Nesting Reviews Resource

We need to do two things to use `/bookmarks/:id/reviews` as a base route.

- pass an option to `express.Router()` to merge parameters from the bookmarks and reviews route
- import the reviews controller in the bookmarks controller

```js
// controllers/reviewController.js
const reviews = express.Router({ mergeParams: true });
```

```js
// controllers/bookmarkController.js
const reviewsController = require("./reviewsController.js");
bookmarks.use("/:bookmark_id/reviews", reviewsController);
```

Now we can go to http://localhost:3333/bookmarks/1/reviews

We've made progress! However, instead of seeing the reviews for the bookmark with the `id` of 2 we see all the reviews.

```js
// controllers/reviewsController.js
reviews.get("/", async (req, res) => {
  const { bookmark_id } = req.params;
  try {
    const allReviews = await getAllReviews(bookmark_id);
    res.json(allReviews);
  } catch (err) {
    res.json(err);
  }
});
```

```js
//  queries/reviews
const getAllReviews = async (bookmark_id) => {
 try {
 const allReviews = await db.any(
 "SELECT * FROM reviews WHERE bookmark_id=$1",
 bookmark_id
 );
 return allReviews;
 } catch (err) {
 return err;
 }
```

It would be useful to see the bookmark with the review

Import the query `getBookmark` to reviews controller

```js
const { getBookmark } = require("../queries/bookmarks.js");
```

Call it and make a new object to send back

```js
reviews.get("/", async (req, res) => {
  const { bookmark_id } = req.params;
  const allReviews = await getAllReviews(bookmark_id);
  const bookmark = await getBookmark(bookmark_id);
  if (allReviews[0]) {
    res.status(200).json({ bookmark, allReviews });
  } else {
    res.status(500).json({ error: "server error" });
  }
});
```

## Cleaning up the Code

In this app, there is no need to ever see reviews without the bookmark. So you can remove those routes.

```js
// app.js
// REMOVE THIS CODE
const reviewsController = require("./controllers/reviewsController.js");
app.use("/reviews", reviewsController);
```

## Test the routes

### Show

Test a show route like http://localhost:3003/bookmarks/1/reviews/1

It should show you the review. But since this is a nested route, rather than just the review, you would expect that the bookmark details are included as well. You can update the show route to include the bookmark data

First import the query for one bookmark

```js
// controllers/reviewsController.js
const { getBookmark } = require("../queries/bookmarks.js");
```

Use the `getBookmark()` function and create a new object with both the bookmark and review:

```js
// controllers/reviewsController.js
reviews.get("/:id", async (req, res) => {
  const { bookmark_id, id } = req.params;
  const review = await getReview(id);
  const bookmark = await getBookmark(bookmark_id);
  if (review) {
    res.json({ bookmark, review });
  } else {
    res.status(404).json({ error: "not found" });
  }
});
```

### Create

Create a new review with Postman

```json
{
  "id": 10,
  "reviewer": "Hadly",
  "title": "Life changing!",
  "content": "Wow! MDN is such a great resource. I can feel my coding skills get better every time I visit!",
  "rating": "5"
}
```

The `bookmark_id` is null. Update the `/reviews` POST route:

```js
reviews.post("/", async (req, res) => {
  const { bookmark_id } = req.params;
  const review = await newReview({ bookmark_id, ...req.body });
  res.status(200).json(review);
});
```

Update the new bookmark (be sure to include a review's id in the URL as well as a bookmark id).

```json
{
  "reviewer": "Hadly",
  "title": "Life changing!",
  "content": "It's ok",
  "rating": 4
}
```

### Update

Make the updates to accept the correct arguments:

```js
reviews.put("/:id", async (req, res) => {
  const { id, bookmark_id } = req.params;
  const updatedReview = await updateReview(id, { bookmark_id, ...req.body });
  if (updatedReview.id) {
    res.status(200).json(updatedReview);
  } else {
    res.status(404).json("Review not found");
  }
});
```

### Delete

Finally test the delete route. It should work without needing to make more updates. Why? Do you need the bookmark id in order to delete the bookmark?
