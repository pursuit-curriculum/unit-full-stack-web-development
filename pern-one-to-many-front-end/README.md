# PERN Stack: One to Many - Front-end

## Important

Make sure all your back-end routes work as expected. It does not matter how amazing your front-end code is, it cannot repair a broken back end.

It's important to test all your routes with something like Postman to be sure you know what the expected responses are. It is critical that when you encounter a bug you can clearly and easily isolate whether the issue is coming from the back-end or front-end.

Here is a refresher of the routes you built for the bookmarks app:

|     | Action  |                URL                | HTTP Verb |    CRUD    |                           Description                           |
| :-: | :-----: | :-------------------------------: | :-------: | :--------: | :-------------------------------------------------------------: |
|  1  | Create  |            /bookmarks             |   POST    | **C**reate |                      Create a new bookmark                      |
|  2  |  Index  |            /bookmarks             |    GET    |  **R**ead  |             Get a list (or index) of all bookmarks              |
|  3  |  Show   |          /bookmarks/:id           |    GET    |  **R**ead  |           Get an individual view (show one bookmark)            |
|  4  | Update  |          /bookmarks/:id           |    PUT    | **U**pdate |                        Update a bookmark                        |
|  5  | Destroy |          /bookmarks/:id           |  DELETE   | **D**elete |                        Delete a bookmark                        |
|  6  | Create  |      /bookmarks/:id/reviews       |   POST    | **C**reate |         Create a new review associated with a bookmark          |
|  7  |  Index  |      /bookmarks/:id/reviews       |    GET    |  **R**ead  | Get a list (or index) of all reviews associated with a bookmark |
|  8  |  Show   | /bookmarks/:id/reviews/:review_id |    GET    |  **R**ead  |       Get an individual review associated with a bookmark       |
|  9  | Update  | /bookmarks/:id/reviews/:review_id |    PUT    | **U**pdate |                         Update a review                         |
| 10  | Destroy | /bookmarks/:id/reviews/:review_id |  DELETE   | **D**elete |                         Delete a review                         |

Thought question: Is the route `http://localhost:3003/reviews` available?

Once again, there are no new concepts to learn with React to build out the functionality. The challenge will be in adding the complexity needed.
