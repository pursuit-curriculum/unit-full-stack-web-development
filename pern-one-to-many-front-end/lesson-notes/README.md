# PERN Stack: One to Many - Front-end

## Important

Make sure all your routes work as expected. It does not matter how amazing your front-end code is; it cannot repair a broken back-end.

> **Note**: If needed, you can also clone down the completed back-end [here](https://github.com/pursuit-curriculum-resources/express-sql-seed-read-demo), use branch `one-to-many`.

## Getting Started

- `git clone https://github.com/pursuit-curriculum-resources/starter-pern-crud`
- `cd starter-pern-crud`
- Checkout a new branch: `git checkout -b one-to-many`
- Pull in the `one-to-many` branch - `git pull origin starter-one-to-many`
- `npm install`
- `touch .env`
- Add `.env` variables'

## Adding the front-end

We will be adding views of reviews only to the `BookmarkDetails` view.

- Keep your back-end running
- Make sure you are at the root of your react-app (same level as its `package.json`)
- `touch src/Components/Review.jsx`
- `touch src/Components/Reviews.jsxx`

## Reviews All

Create an index view of all reviews belonging to one bookmark.

```js
// src/Reviews.jsx
import { useState, useEffect } from "react";
import { useParams } from "react-router-dom";
import Review from "./Review";

const API = import.meta.env.VITE_BASE_URL;

function Reviews() {
  const [reviews, setReviews] = useState([]);
  let { id } = useParams();

  useEffect(() => {
    fetch(`${API}/bookmarks/${id}/reviews`)
      .then((response) => response.json())
      .then((response) => {
        setReviews(response.reviews);
      });
  }, [id, API]);
  return (
    <section className="Reviews">
      {reviews.map((review) => (
        <Review key={review.id} review={review} />
      ))}
    </section>
  );
}

export default Reviews;
```

Create a review component:

```js
// src/Review.js
function Review({ review }) {
  return (
    <div className="Review">
      <h4>
        {review.title} <span>{review.rating}</span>
      </h4>
      <h5>{review.reviewer}</h5>
      <p>{review.content}</p>
    </div>
  );
}

export default Review;
```

Import `Reviews` into BookmarkDetails.

```js
// src/BookmarkDetails.jsx
import Reviews from "./Reviews";
```

After the buttons and closing `div`s (right above the closing `article` tag):

```js
<Reviews />
```

<details><summary>Reviews Added to the bookmarks show route</summary>

![](../assets/reviews-read.png)

</details>

## Review Form

We will handle the form differently than we did with the bookmarks form. We will keep the new form on the same page as the bookmarks/reviews.

Additionally, we will reuse this form for new and editing bookmarks.

In the interest of time during the lecture, let's copy-paste this code and discuss what is going on.

Depending on whether we want to add a new review or update a review, `handleSubmit` will run a different function. We'll see how the pieces come together as we build the rest.

We want to be able to pre-fill the data if it is the edit form. We can `useEffect` to check if data is being passed down and update the form inputs.

If it is the new form, we want to display an extra `h3` to inform the users this is the form to create a new review. We will do this by using `children` to display this additional `h3`.

## Add new review functionality

Here are the new pieces of code. Place them in their appropriate locations(along the top, inside the function, inside the return).

```js
// src/Reviews.jsx
import ReviewForm from "./ReviewForm";

// add this inside the Reviews() function
const handleAdd = (newReview) => {
  fetch(`${API}/bookmarks/${id}/reviews`, {
    method: "POST",
    body: JSON.stringify(newReview),
    headers: {
      "Content-Type": "application/json",
    },
  })
    .then((response) => response.json())
    .then((responseJSON) => {
      setReviews([responseJSON, ...reviews]);
    })
    .catch((error) => console.error("catch", error));
};

return (
  <section className="Reviews">
    <h2>Reviews</h2>
    <ReviewForm handleSubmit={handleAdd}>
      <h3>Add a New Review</h3>
    </ReviewForm>
    {reviews.map((review) => (
      <Review key={review.id} review={review} />
    ))}
  </section>
);
```

## Add Delete Functionality

```js
// src/Reviews.jsx
const handleDelete = (id) => {
  fetch(`${API}/bookmarks/${id}/reviews/${id}`, {
    method: "DELETE",
  })
    .then(
      (response) => {
        const copyReviewArray = [...reviews];
        const indexDeletedReview = copyReviewArray.findIndex((review) => {
          return review.id === id;
        });
        copyReviewArray.splice(indexDeletedReview, 1);
        setReviews(copyReviewArray);
      },
      (error) => console.error(error)
    )
    .catch((error) => console.warn("catch", error));
};
```

// in the return

```
<Review key={review.id} review={review} handleDelete={handleDelete} />
```

Add `handleDelete` as a parameter and then add an `onClick` event on delete button in the `Review` component.

```js
// src/Review.js
function Review({ review, handleDelete }) {
  return (
    <div className="Review">
      <h4>
        {review.title} <span>{review.rating}</span>
      </h4>
      <h5>{review.reviewer}</h5>
      <p>{review.content}</p>
      <button onClick={() => handleDelete(review.id)}>delete</button>
    </div>
  );
}
```

## Add Edit Functionality

Add the API call to Reviews.jsx

```js
// src/Reviews.jsx
const handleEdit = (updatedReview) => {
  fetch(`${API}/bookmarks/${id}/reviews/${updatedReview.id}`, {
    method: "PUT",
    body: JSON.stringify(updatedReview),
    headers: {
      "Content-Type": "application/json",
    },
  })
    .then((response) => response.json())
    .then((responseJSON) => {
      const copyReviewArray = [...reviews];
      const indexUpdatedReview = copyReviewArray.findIndex((review) => {
        return review.id === updatedReview.id;
      });
      copyReviewArray[indexUpdatedReview] = responseJSON;
      setReviews(copyReviewArray);
    })
    .catch((error) => console.error(error));
};
```

Pass it to the `Review` component

```js
<Review
  key={review.id}
  review={review}
  handleSubmit={handleEdit}
  handleDelete={handleDelete}
/>
```

The way we will approach this is to add a button to edit. This will toggle the view from a `review` to a `reviewForm` by using conditional rendering with a ternary operator. It will end up looking like this example:

```js
// DEMO DO NOT CODE
{
  viewEditForm ? <ShowIfValueIsTrue /> : <ShowIfValueIsFalse />;
}
```

<br />

```js
// Components/Review.jsx
import { useState } from "react";
import ReviewForm from "./ReviewForm";
function Review({ review, handleDelete, handleSubmit }) {
  const [viewEditForm, setEditForm] = useState(false);
  const toggleView = () => {
    setEditForm(!viewEditForm);
  };
  return (
    <div className="Review">
      {viewEditForm ? (
        <ReviewForm
          reviewDetails={review}
          toggleView={toggleView}
          handleSubmit={handleSubmit}
        />
      ) : (
        <div>
          <h4>
            {review.title} <span>{review.rating}</span>
          </h4>
          <h5>{review.reviewer}</h5>
          <p>{review.content}</p>
        </div>
      )}
      <div className="review-actions">
        <button onClick={toggleView}>
          {viewEditForm ? "Cancel" : "Edit this review"}
        </button>
        <button onClick={() => handleDelete(review.id)}>delete</button>
      </div>
    </div>
  );
}

export default Review;
```

Now you should be able to click the `edit this review` button and toggle between the two components.

We'll have to continue to pass the `handleSubmit` function down. Passing props down two or more times can be called `props drilling`. A little bit of props drilling is ok. However, if you find you need to use it a lot, you can look into a React API (like Context or Redux) to help manage state.

And that should be it! A one-to-many relationship is one way to do full CRUD on a second model.

### Bonus

Can you figure out a way that _when_ the viewEditForm is visible, the button would say `see review`, but if the value is toggled back to false, the button would say `edit this review` instead?

## New Bookmarks Bonus Challenges/Lab time

Are you looking for a challenge?

BONUS FORM change the category to options of already created categories, then allow for a new category option where a user will enter a new category

Add functionality that the categories are always lowercase, including new entries.

Alternatively, if you have finished the Tuner lab requirements, you can implement a `playlist` model where a `playlist` will have many songs.

**HINT**: If you delete a playlist, do you want the songs to be deleted too? This will likely be a different use case than what we did with bookmarks.
