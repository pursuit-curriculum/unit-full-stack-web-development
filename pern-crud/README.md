# PERN Stack: CRUD

# Express Connect React - REVIEW

## Intro

We now have a nicely working back-end with full CRUD using express. Unfortunately, it isn't the best user experience for non-developers. We need to build a front-end.

When you stack **P**ostgres, **E**press, **R**eact, and **N**ode, it is often referred to as the `PERN` Stack.

There are many different technology stacks. You may see some when you start looking for jobs, like Ruby on Rails, LAMP, MEAN, Django, and Laravel - generally, these stacks refer to a back-end language/framework, a database, and some way of rendering views. As you encounter these stacks, google them as needed. It will be improbable that you will have worked with every technology listed in a stack. In tech, it's more important to be ready to learn what you need as you build than to try to learn everything first.

## REVIEW: The Seven RESTful Routes

The seven RESTful routes are often the classic example of a RESTful pattern. Despite the different tech stacks listed above, many will still follow this pattern, usually some version of MVC.

While this pattern is classic, and we will follow it exactly for learning fundamentals, you will see many variations in the wild. These variations are to meet specific needs of functionality or to improve user experience.

We have built the first 5 routes in Express:

|  #  | Action  |      URL       | HTTP Verb |    CRUD    |              Description               |
| :-: | :-----: | :------------: | :-------: | :--------: | :------------------------------------: |
|  1  |  Index  |   /bookmarks   |    GET    |  **R**ead  | Get a list (or index) of all bookmarks |
|  2  |  Show   | /bookmarks/:id |    GET    |  **R**ead  | Get an individual view (show one log)  |
|  3  | Create  |   /bookmarks   |   POST    | **C**reate |           Create a new book            |
|  4  | Destroy | /bookmarks/:id |  DELETE   | **D**elete |             Delete a book              |
|  5  | Update  | /bookmarks/:id |    PUT    | **U**pdate |             Update a book              |

<br />

The next four are ones that we will build in our front-end:

|  #  | Action |         URL         | HTTP Verb |   CRUD   |                Description                 |
| :-: | :----: | :-----------------: | :-------: | :------: | :----------------------------------------: |
|  1  | Index  |     /bookmarks      |    GET    | **R**ead |   Get a list (or index) of all bookmarks   |
|  2  |  Show  |   /bookmarks/:id    |    GET    | **R**ead | Get an individual view (show one bookmark) |
|  3  |  New   |   /bookmarks/new    |    GET    | **R**ead |    Get a form to create a new bookmark     |
|  4  |  Edit  | /bookmarks/:id/edit |    GET    | **R**ead |      Get a form to update a bookmark       |

Which two are new, and which ones have we built with express?
