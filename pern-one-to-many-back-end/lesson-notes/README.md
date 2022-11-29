

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
('1', 'Evan', 'My Favorite', 'This website crushes it when it comes to awesome explanations, 3),
('2', 'Evan', 'My Favorite', 'This website crushes it when it comes to inspiring me, 3),
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

**app.js**

```js
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

[README2.md](./README2.md)
