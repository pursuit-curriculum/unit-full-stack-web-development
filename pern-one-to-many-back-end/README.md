# PERN Stack: One-to-Many - Back-End

So far, we've just worked with one model. When you get to a professional project, there will be many models. There are many design considerations when adding models, including relationships and whether full CRUD is needed.

Here are some different apps that require different designs:

- A weather station reporting to a central server/database - we likely don't want to be able to edit or delete the data coming in, so we probably would not build out full CRUD in a user interface.
- An online store - it's essential to retrieve the index and show pages as quickly as possible since that is most of the db calls users will make. Updates and deletes of the products don't have to be as fast, so you may design how you build the **r**ead resources (mostly choices around database functionality) differently than **u**pdate and **d**elete.
- If you have students in a class (**one** class to **many** students), but the class is canceled, you don't want to delete the students from the database.
- In contrast, if you have **one** product with **many** reviews, if the product is deleted from the database, you would likely want to delete all the reviews that went with it because the reviews don't make sense without the product.

For now, we are just building simple apps intending to learn rather than make a robust and compelling user experience or worrying about optimization. We also do not yet have to consider what happens if we have thousands or millions of users. We're going to focus on syntax and code examples that are simple, so you can have a solid foundation to build on so that when you encounter new challenges, you can build on your foundation.

Remember, each application has unique needs and requires thoughtful solutions. It will be your job to take the foundations you know and adapt them to solve the problems in meeting the business needs. Demonstrating critical thinking and analysis in your coding is essential to becoming a great developer.

A typical learning pattern is first to imitate (like following the code-along here or another tutorial), to assimilate (build something very similar to a tutorial, like the labs you've been working on), and finally innovate (the project and future work you will do on the job).

## Learning Objectives

By the end of this lesson, you should be able to:

- Connect a table with foreign keys to an Express API.
- Respond with parent and child data from an API.
- Maintain RESTful nested routes when working with one-to-many relationships.
- Represent one-to-many relationships in a React application.

## Adding a second model to the back-end

> **Note**: This reading only has some small code examples. There is no code-along.

### Setting up the tables in SQL

Thinking about a blog app where a user can have many posts (but posts can only belong to one user), the data models/SQL tables are the first to set up. Users can exist without posts. But posts without a user don't hold much value. Even if you wanted to allow for anonymous posts, you likely would like a record of who wrote it (in case it is a troll account or bot), even though you would not display the user information publicly in this case.

Let's start with a user table:

```SQL
-- USER

DROP TABLE IF EXISTS users;

CREATE TABLE users (
 id SERIAL PRIMARY KEY,
 name TEXT,
 password TEXT,
 email TEXT,
);
```

Then let's make a posts table. There are two new things to note.

- When setting up the foreign key, there is a constraint keyword [`REFERENCES`](https://www.postgresql.org/docs/current/tutorial-fk.html). This means posts cannot be created without a valid user `PRIMARY KEY` in the user table.
- If the user gets deleted, their posts should also get deleted. Therefore you can add [`ON DELETE CASCADE`](https://www.postgresql.org/docs/current/ddl-constraints.html), which will remove the posts if the user gets deleted.

```SQL
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
 id SERIAL PRIMARY KEY,
 title TEXT,
 content TEXT,
 user_id INTEGER REFERENCES users (id)
 ON DELETE CASCADE
);
```

When inserting data into the posts table, you must pass a valid value for `user_id`. While this is simple enough to do on the command line, when you create your application, you would need to have a way to collect that information from the app. So while there are very few new concepts in creating a one-to-many relationship, the challenge now comes from the added complexity that needs to be built.

## RESTful Routes

You would have the following routes if you built full CRUD for users and posts.

|     | Action  |    URL     | HTTP Verb |    CRUD    |              Description               |
| :-: | :-----: | :--------: | :-------: | :--------: | :------------------------------------: |
|  1  | Create  |   /posts   |   POST    | **C**reate |           Create a new post            |
|  2  |  Index  |   /posts   |    GET    |  **R**ead  |   Get a list (or index) of all posts   |
|  3  |  Show   | /posts/:id |    GET    |  **R**ead  | Get an individual view (show one post) |
|  4  | Update  | /posts/:id |    PUT    | **U**pdate |             Update a post              |
|  5  | Destroy | /posts/:id |  DELETE   | **D**elete |             Delete a post              |
|  1  | Create  |   /users   |   POST    | **C**reate |           Create a new user            |
|  2  |  Index  |   /users   |    GET    |  **R**ead  |   Get a list (or index) of all users   |
|  3  |  Show   | /users/:id |    GET    |  **R**ead  | Get an individual view (show one user) |
|  4  | Update  | /users/:id |    PUT    | **U**pdate |             Update a user              |
|  5  | Destroy | /users/:id |  DELETE   | **D**elete |             Delete a user              |

Notice that the following resource is not useful. It would only show the posts and no user names. Therefore, you would not build it.

|     | Action |  URL   | HTTP Verb |   CRUD   |            Description             |
| :-: | :----: | :----: | :-------: | :------: | :--------------------------------: |
|  2  | Index  | /posts |    GET    | **R**ead | Get a list (or index) of all posts |

But how can you relate these resources? How can you show a user profile AND their posts?

Your SQL query could be written like this:

```SQL
 "SELECT * FROM posts WHERE user_id=$1",
```

Let's imagine that the user view is just account information. Would you always need to load the posts? Probably not. You would most likely want a separate view where you can see the user's post.

In this case, you would create a nested route.

|     | Action |       URL        | HTTP Verb |   CRUD   |               Description               |
| :-: | :----: | :--------------: | :-------: | :------: | :-------------------------------------: |
|  1  |  Read  | /users/:id/posts |    GET    | **R**ead | See posts all posts belonging to a user |

You can create all the nested routes. Which would look like the following:

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

Would you need to create routes in the format `posts/:post_id/users` format? This resource would be described as _show one post and include all the users_. That doesn't make much sense. What about `posts/:post_id/users/:user_id`? This route makes more sense. When creating nested resources, it is not necessary to create every combination. You must design your routes to make sense. You should use user stories and other planning tools to guide your routes.

## Setting up Express for nested routes

To keep our code dry and have one `users` resource and one `posts` resource, you can configure the Express router to merge parameters, so you can easily create nested routes by passing an option into `express.Router()`.

```js
// controllers/postsController.js
const posts = express.Router({ mergeParams: true });
```

This will allow you to get the parameters from `/users` when going to the nested route like `/users/:users_id/posts`:

```js
// controllers/postsController.js
posts.get("/", async (req, res) => {
  const { users_id } = req.params;

  try {
    const allPosts = await getAllPosts(users_id);
    res.json(allPosts);
  } catch (err) {
    res.json(err);
  }
});
```

As you can see, the route here is `/posts/`, but the request can access the parameters from the user's route.

## Bonus: all users and all posts

How can you get the data for `/users/posts`?

You could make two database calls:

```js
const users = getAllUsers();
const posts = getAllPosts();
```

Since you cannot send back multiple responses, you must use JavaScript to create one object to hold all the data.

If you create a flat object like this

```js
{
  users, posts;
}
```

It's not very useful to the front-end developer.

Instead, you would want to create parent and child data:

```json
[
  {
    "user": {
      "id": 55,
      "name": "Freddie",
      "email": "mercury@champions.com",
      "posts": [
        {
          "id": 77,
          "title": "Champions",
          "content": "We are the"
        },
        {
          "id": 88,
          "title": "We will",
          "content": "Rock you"
        }
      ]
    }
  },
  {
    "user": {
      "id": 222,
      "name": "David",
      "email": "major.tom@diamond.dogs.com",
      "posts": [
        {
          "id": 111,
          "title": "Heroes",
          "content": "Let's Dance!"
        },
        {
          "id": 333,
          "title": "Under Pressure",
          "content": "Let's Dance!"
        }
      ]
    }
  }
]
```

What is more, to do this, you are making two database calls, which is ok for small apps and not many users. But in most cases, you'd instead make one database call. How can you get both users and posts with just one database call?

You would use a join table. Therefore your SQL query would need to be:

```SQL
SELECT
 user.name, user.id, post.id, post.title, post.content
FROM
 users
JOIN
 posts
ON
 user.id = posts.user_id;
```

Why is it critical NOT to send the user passwords in this view?

Now you end up with an unideal collection of data. For every review, the user data is repeated. Instead, you want one user and each review as an object inside an array belonging to the user. You can write a function in JavaScript to clean up the data.

## Reference build

Please see the lesson notes for an example.
