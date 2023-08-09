# Express Response

## Learning Objectives

By the end of this lesson, you should be able to:

- Explain what Express is and how it fits into building a full-stack web application.
- Create a basic server using Express that locally listens for a route and responds with data to any request.
- Create a route at a specific path that responds with some data.
- Identify the role of each argument within the Express route’s callback function.
- Manage a running server via a terminal window.

## Guiding questions and class activity

- What is full-stack?

- What is a front-end?

- What is a back-end?

- When you build a front-end application, is it the client or server?

- When you use a third-party API, is it a client or server?

- What is Express?

- How does Express fit into building a full-stack application?

- As a class, build a basic express server based on the pre-reading or [npm documentation](https://www.npmjs.com/package/express)

- Did you build it exactly as the documentation showed it? Or did you change anything like syntax or variable names?

- Identify the role of each argument within the express route's callback function:

```js
app.get("/", (request, response) => {
  response.send("I love express!");
});
```

- Talk through the following visual and the order of events

![Request response code](../assets/request-response-code.png)

- How can you run your application so that it automatically updates when you make changes to your code?

- What is `nodemon`? How do you install it?

- How do you use `nodemon`?

- Why is it important to always shut down your server?

- How can you add additional routes?

- What must you type in the browser URL to access the new routes?

- What is the data type of the response body?

- If you lose control of your server and it runs in the background, how can you stop it?
