# Express Connect React

## Introduction

You now have a nicely working back-end API that serves JSON with full CRUD using Express. Unfortunately, it isn't the best user experience for non-developers. You need to build a front-end.

## Learning Outcomes

By the end of this lesson, you should be able to:

- Be able to connect a React application to an Express back-end API.
- Be able to describe all 7 RESTful views.
- Be able to set a base URL that can be easily updated, depending on the local environment, and describe why it is important to do so.
- Be able to describe why an easily updated URL is useful
- Be able to view an index route with data from the express API.
- Be able to describe what CORS is and why the default is to block cross-origin requests.

---

## The Seven RESTful Routes

The seven RESTful routes are the classic example of a RESTful pattern. Before front-end applications like React became more standard, servers handled creating HTML views for a web site. That means that there are two additional views: `New`, which is a view with a form for users to fill out and submit `Create` requests and `Edit` which is a view with a form for users to fill out and submit an `Update` requests. And certain routes like `Create`, `Delete` and `Update` don't have views, so there is nothing to build on the front-end for them.

While this pattern is classic, and we will follow it exactly for learning fundamentals, you will see many variations in the wild. These variations are to meet specific needs of an application and to improve user experience.

We have built the first five routes in Express:

|  #  | Action  |      URL       | HTTP Verb |    CRUD    |              Description               |
| :-: | :-----: | :------------: | :-------: | :--------: | :------------------------------------: |
|  1  |  Index  |   /bookmarks   |    GET    |  **R**ead  | Get a list (or index) of all bookmarks |
|  2  |  Show   | /bookmarks/:id |    GET    |  **R**ead  | Get an individual view (show one log)  |
|  3  | Create  |   /bookmarks   |   POST    | **C**reate |            Create a new log            |
|  4  | Destroy | /bookmarks/:id |  DELETE   | **D**elete |              Delete a log              |
|  5  | Update  | /bookmarks/:id |    PUT    | **U**pdate |              Update a log              |

<br />

The next four are ones that you will build with a front-end:

|  #  | Action |         URL         | HTTP Verb |   CRUD   |              Description               |
| :-: | :----: | :-----------------: | :-------: | :------: | :------------------------------------: |
|  1  | Index  |     /bookmarks      |    GET    | **R**ead | Get a list (or index) of all bookmarks |
|  2  |  Show  |   /bookmarks/:id    |    GET    | **R**ead | Get an individual view (show one log)  |
|  3  |  New   |   /bookmarks/new    |    GET    | **R**ead |  Get a form to create a new bookmark   |
|  4  |  Edit  | /bookmarks/:id/edit |    GET    | **R**ead |    Get a form to update a bookmark     |

Which two are new, and which ones have you built with Express?

Do you notice any patterns with the front-end routes?

<details><summary>Answer</summary>

All the front-end routes are GET requests that are meant to read data.

There are no POST, PUT, or DELETE routes.

</details>
