# PERN Many to Many

Here we will be adding users to our app. Our users can have many bookmarks, and bookmarks can belong to many users.

We will only implement this functionality on the back-end in this lesson.

If we add a model `users`, we can say that `users` have many `reviews`. `reviews` can only belong to one `user`, a one-to-many relationship we've seen with `bookmarks` and reviews.

However, `bookmarks` can have many `users` and `users` can have many `bookmarks` - thus creating a `many to many relationship`.

We can imagine seeing a list of bookmarks by category, allowing users to 'favorite' or add them to their collection of bookmarks. If a user no longer likes a bookmark, we would like to remove that bookmark from the `users` list, but we would not want to delete that bookmark.

As we can see, the relationship between `bookmarks` and `users` has different functions than `bookmarks` and `reviews`. Which means the way we set up the routes will be different.

Once there are more than two models, designing an API begins to become complex. There are a lot of considerations needed. Building every possible permutation of routes/resources is not a good use of time, nor would it be easy to maintain.

We should spend some time drawing ERD/UML diagrams and user stories.

For example:

- ✅ a user can see all of their reviews
- ❓ see all the users associated with a bookmark (depending on the goal(s) of our app)
- ❌ see all the reviews that belong to a user, but without knowing which bookmark the reviews belong to (how would this be useful?)

Since our focus is a short, simple, demonstrative lesson that you can use as a reference. These are the users stories we would like to build

- A user can create/update/delete/see details of their user info
- A user can see a list of bookmarks they have
- A user can add/remove bookmarks from their collection
- A user can see a list of all their reviews (with information about which bookmark the reviews belong to)

|     | Action  |         URL          | HTTP Verb |    CRUD    |                  Description                   |
| :-: | :-----: | :------------------: | :-------: | :--------: | :--------------------------------------------: |
|  1  | Create  |        /users        |   POST    | **C**reate |               Create a new user                |
|  2  |  Index  |        /users        |    GET    |  **R**ead  |       Get a list (or index) of all users       |
|  3  |  Show   |      /users/:id      |    GET    |  **R**ead  |     Get an individual view (show one user)     |
|  4  | Update  |      /users/:id      |    PUT    | **U**pdate |                 Update a user                  |
|  5  | Destroy |      /users/:id      |  DELETE   | **D**elete |                 Delete a user                  |
|  6  |  Index  | /users/:id/bookmarks |    GET    |  **R**ead  | Get a list (or index) of all bookmarks by user |
|  7  | Create  | /users/:id/bookmarks |   POST    | **C**reate |      Add a bookmark to a user collection       |
|  8  | Destroy |      /users/:id      |  DELETE   | **D**elete |                 Delete a user                  |
|  9  |  Index  |  /users/:id/reviews  |    GET    |  **R**ead  |  Get a list (or index) of all reviews by user  |
