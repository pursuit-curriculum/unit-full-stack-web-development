# Express Connect React

## Learning Outcomes

- Be able to connect a create-react-app to an express backend
- Be able to describe all 7 RESTful Routes
- Be able to set a base URL that can be easily updated, depending on the environment
- Be able to describe why an easily updated URL is useful
- Be able to view an index route with data from the express API
- Be able to describe what CORS is and why the default is to block cross-origin requests

## Intro

We now have a nicely working back-end with full CRUD using express. Unfortunately, it isn't the best user experience for non-developers. We need to build a front end.

## The Seven RESTful Routes

The seven RESTful routes are often the classic example of a RESTful pattern. Every `GET` request must have a view. Therefore, we have some new views we must make for our users. For example, Users must be able to see a form to make a new bookmark/access the `update` route.

While this pattern is classic, and we will follow it exactly for learning fundamentals, you will see many variations in the wild. These variations are to meet specific needs of functionality and to improve user experience.

We have built the first five routes in Express:

|  #  | Action  |      URL       | HTTP Verb |    CRUD    |              Description               |
| :-: | :-----: | :------------: | :-------: | :--------: | :------------------------------------: |
|  1  |  Index  |   /bookmarks   |    GET    |  **R**ead  | Get a list (or index) of all bookmarks |
|  2  |  Show   | /bookmarks/:id |    GET    |  **R**ead  | Get an individual view (show one log)  |
|  3  | Create  |   /bookmarks   |   POST    | **C**reate |            Create a new log            |
|  4  | Destroy | /bookmarks/:id |  DELETE   | **D**elete |              Delete a log              |
|  5  | Update  | /bookmarks/:id |    PUT    | **U**pdate |              Update a log              |

<br />

The next four are ones that we will build in our front end:

|  #  | Action |         URL         | HTTP Verb |   CRUD   |              Description               |
| :-: | :----: | :-----------------: | :-------: | :------: | :------------------------------------: |
|  1  | Index  |     /bookmarks      |    GET    | **R**ead | Get a list (or index) of all bookmarks |
|  2  |  Show  |   /bookmarks/:id    |    GET    | **R**ead | Get an individual view (show one log)  |
|  3  |  New   |   /bookmarks/new    |    GET    | **R**ead |  Get a form to create a new bookmark   |
|  4  |  Edit  | /bookmarks/:id/edit |    GET    | **R**ead |    Get a form to update a bookmark     |

Which two are new, and which ones have we built with ExpressJS?
