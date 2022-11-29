# Express CRUD/MVC

## Intro

Let's take a moment to think about a website like Etsy. Etsy has thousands of shops and millions of products.

When a shop owner decides to sell a new product, they enter the data about the latest product: name, price, image(s), description, etc. And almost immediately, a new web page appears!

How is this happening? Etsy doesn't have a bunch of devs sitting around at the ready to hand roll a new web page. Instead, the developers build an HTML template and then, based on the data provided **embed** (insert) the data into the HTML

Let's look at an example - this is some data provided by an Etsy shop owner through an online form:

![](./assets/form.png)

When the owner presses the `submit` button. The browser will send a `request` to a server. The server will save the data in a database. We can imagine the data looks something like this:

**DEMO, do not code**

```js
{
 name: 'Bismuth',
 price: 20,
 image: 'bismuth.png',
 description: 'Very cool mineral',
 shopName: 'Rock Shop'
}

```

The server will `respond` with confirmation that the `request` went through successfully or will provide an error message.

Then, when the owner wants to see the product. The owner will make a `request` to see the product in the browser. There will be a `response` that will involve some logic to **embed** the data inside HTML:

**DEMO, do not code**

```html
<h1>{rock.shopName}</h1>
<div class="card">
  <img src="{rock.img}" alt="{rock.name}" />
  <h3><span>{rock.name}</span> {rock.price}</h3>
  <p>{rock.description}</p>
</div>
```

And then, it gets rendered in the browser:

![](./assets/bismuth-html.png)

There are four main things to do with data: **C**reate, **R**ead, **U**pdate, and **D**elete. In the above case, a user created some data and then read the newly created data. We can imagine, at some point, that the user may want to update the price or delete the item altogether.

We're going to be building a webpage bookmarks app. It will be a very simple app that will teach the fundamentals of building an API with express. Later, we'll add a react front-end to consume the API. Finally, we'll learn about databases and integrate a database into our simple app.

Once we have our fundamentals down, we can start building more complex apps with real-world applications.

## Software Architecture: MVC

The apps we are building in class are tiny. Even with our small apps, getting lost in our code can be very easy. Where did we create a particular function? What file is displaying what we see in the browser? Organizing and maintaining code is quite challenging!

Our problems are a microcosm of an app that is years old that has had tens or possibly hundreds of developers work on it. Therefore, developers who are far more experienced have worked on creating conventions, frameworks, styles, and patterns that are easy to maintain, collaborate and reuse.

**Bonus:** [Read more on software architecture](https://en.wikipedia.org/wiki/Software_architecture)

We will loosely follow a prevalent software design pattern called [MVC](https://en.wikipedia.org/wiki/Model–view–controller): Model-View-Controller.

Following this pattern allows developers to separate the concerns of the data, the view, and the controller.

A common metaphor for this is a restaurant. Imagine a customer sitting down and interacting with the menu and dishes (views). A chef in the back takes orders and makes food (models). Finally, a server goes between the two (controller).

Everyone in the restaurant has a clear idea of what their responsibility is. Otherwise, the restaurant likely would fail. Imagine if the customers could go in the back and start cooking or the servers stopped ringing up orders!

Even though there is a typical pattern for a restaurant, there are many variations, like buffets, drive-throughs, and more. A pattern is a recommendation, not a rule.

So let's get back to our application. We are going to have a place for our models (data), our views (what users will see in the browser), and the controllers (logic between the two).

In terms of code, we will have a folder called `models` and a folder called `controllers`. While it is possible to have a `views` folder (and many applications use this folder), we will serve our views not from inside our app but with a separate React app.

**NOTE:** With ExpressJS, you can name your files and folders whatever you want. If you want to call your `controllers` folder `asdf` and your `models` folder `batman` - you can. However, no one will understand how your code is organized with names that don't make sense/are not semantic.

Because ExpressJS is unopinionated, you will likely see various names for different files. For example, when it comes to controllers, you may notice:

- birdController.js
- birdsController.js
- birds.js
- birds-controller.js
- birds-controllers.js
- birds.controller.js

When you join a company, they will have chosen a naming strategy, and you should follow it. Within your work your naming should be consistent within each project.

As this curriculum is being built and improved, it can be evident that it was initially created without consensus on naming conventions. The curriculum is slowly being updated to follow conventions. By building before planning and agreeing as a company, this curriculum generated something called `technical debt` (choosing to deliver work ASAP and deciding to fix things later), where every individual contributing chose the style they liked. Any project will typically have some technical debt. However, over time, technical debt makes things harder to maintain and can generate a lot of additional work. Notice your own experience using the materials here and think about how the consistency would likely improve your experience, then try to carry this knowledge with you into your future projects.
