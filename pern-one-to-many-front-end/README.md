# PERN Stack: One to Many - Front-end

## Important

Make sure all your routes work as expected. It does not matter how amazing your front-end code is, it cannot repair a broken back end.

It's important to test all your routes with something like Postman to be sure you know what the expected responses are. It is critical that when you encounter a bug you can clearly and easily isolate whether the issue is coming from the back-end or front-end.

Here is a refresher of the routes you built

|     | Action  |            URL            | HTTP Verb |    CRUD    |                        Description                        |
| :-: | :-----: | :-----------------------: | :-------: | :--------: | :-------------------------------------------------------: |
|  1  | Create  |          /users           |   POST    | **C**reate |                     Create a new user                     |
|  2  |  Index  |          /users           |    GET    |  **R**ead  |            Get a list (or index) of all users             |
|  3  |  Show   |        /users/:id         |    GET    |  **R**ead  |          Get an individual view (show one user)           |
|  4  | Update  |        /users/:id         |    PUT    | **U**pdate |                       Update a user                       |
|  5  | Destroy |        /users/:id         |  DELETE   | **D**elete |                       Delete a user                       |
|  6  | Create  |     /users/:id/posts      |   POST    | **C**reate |         Create a new post associated with a user          |
|  7  |  Index  |     /users/:id/posts      |    GET    |  **R**ead  | Get a list (or index) of all posts associated with a user |
|  8  |  Show   | /users/:id/posts/:post_id |    GET    |  **R**ead  |       Get an individual post associated with a user       |
|  9  | Update  | /users/:id/posts/:post_id |    PUT    | **U**pdate |                       Update a post                       |
| 10  | Destroy | /users/:id/posts/:post_id |  DELETE   | **D**elete |                       Delete a post                       |

Thought question: Is the route `http://localhost:3003/reviews` available?

Once again, there are no new concepts to learn with React to build out the functionality. The challenge will be in adding the complexity needed.
