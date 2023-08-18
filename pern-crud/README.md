# PERN Stack: CRUD

There will only be minor changes to the front-end to get it to work with your new back-end. Therefore this lesson will be review.

## Intro

You now have a nicely working back-end with full CRUD using Express. Unfortunately, it isn't the best user experience for non-developers. You need to build a front-end.

When you stack **P**ostgres, **E**press, **R**eact, and **N**ode, it is often called the `PERN` Stack.

There are many different technology stacks. You may see some when you start looking for jobs, like Ruby on Rails, LAMP, MEAN, Django, and Laravel - generally, these stacks refer to a back-end language/framework, a database, and some way of rendering views. As you encounter these stacks, google them as needed. It will be improbable that you will have worked with every technology listed in a stack. In tech, it's more important to have strong fundamentals so that you will be ready to learn what you need as you build.

## REVIEW: The Seven RESTful Routes

The seven RESTful routes are often the classic example of a RESTful pattern. Despite the different tech stacks listed above, many will still follow this pattern, usually some version of MVC.

While this pattern is classic, and we will follow it exactly for learning fundamentals, you will see many variations in the wild. These variations are to meet specific functionality needs or improve user experience. Patterns are recommendations, not rules.

We have built the first five routes in Express:

|  #  | Action  |     URL     | HTTP Verb |    CRUD    |              Description              |
| :-: | :-----: | :---------: | :-------: | :--------: | :-----------------------------------: |
|  1  |  Index  |   /colors   |    GET    |  **R**ead  |  Get a list (or index) of all colors  |
|  2  |  Show   | /colors/:id |    GET    |  **R**ead  | Get an individual view (show one log) |
|  3  | Create  |   /colors   |   POST    | **C**reate |           Create a new book           |
|  4  | Destroy | /colors/:id |  DELETE   | **D**elete |             Delete a book             |
|  5  | Update  | /colors/:id |    PUT    | **U**pdate |             Update a book             |

<br />

The next four are ones that we will build in our front-end:

|  #  | Action |       URL        | HTTP Verb |   CRUD   |               Description               |
| :-: | :----: | :--------------: | :-------: | :------: | :-------------------------------------: |
|  1  | Index  |     /colors      |    GET    | **R**ead |   Get a list (or index) of all colors   |
|  2  |  Show  |   /colors/:id    |    GET    | **R**ead | Get an individual view (show one color) |
|  3  |  New   |   /colors/new    |    GET    | **R**ead |    Get a form to create a new color     |
|  4  |  Edit  | /colors/:id/edit |    GET    | **R**ead |      Get a form to update a color       |

Which two are new, and which ones have we built with Express?

## Pre-reading code along

There is no step-by-step code along. However, you can clone down a fresh copy of the [colors react app](https://github.com/pursuit-curriculum-resources/pre-reading-connect-react-demo) and refactor it to work with a back-end that uses Postgres. One of the most significant changes is that you'll need to change from using the array position to ids. Why?
