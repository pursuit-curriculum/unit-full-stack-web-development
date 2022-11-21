# CSS Bootstrap

## Code Libraries and Frameworks

Oftentimes, developers find that they have the same problems in many projects.

When it comes to CSS, a number of problems happen over and over again. For example:

- Consistency across browsers
- Making a responsive/mobile and desktop version
- Styling forms to look more modern and user friendly

Rather than each developer coding their own solution and using it over and over again, developers get together and make a code library or framework.

One popular CSS framework is called Bootstrap. It was initially developed at Twitter and was made open source in 2011. As an open source project, anyone can use these code solutions for free and they can also contribute to improving the project.

## Shifting Strategies

While a base understanding of CSS is critical in building websites, in order to get the most out of a code library/framework is the ability to read the documentation and learn to use the built-in features.

[Bootstrap's documentation](https://getbootstrap.com/docs/5.0/getting-started/introduction/) is quite good. As you look through it, you will see that it has many features. The more you build with it, the more you'll learn. Today we'll focus on getting set up and looking at some basic features.

## Get coding: Get the starter code

We'll be building Rosier. The html is complete all we have to do is add the bootstrap code library and apply the appropriate classes.

**Important**: Bootstrap is responsive, so the way things appear will vary based on the width of the browser window. If things don't look right, try changing the width. If your CSS does not look perfect, that's ok. This is a practice lesson for you to learn. Close enough is good enough.

![rosier compelte](./assets/rosier-complete.png)
..
..
..

Starting appearance

![](./assets/rosier-starter.png)

## Pro-tip

Our HTML is quite long and busy. When we work on sections we can visually fold up our code with our text editor.

![](./assets/code-folding.gif)
This will make it easier to stay focused on the component we need to style.

## Getting started

- [Clone this project onto your computer ](https://github.com/joinpursuit/Intro-to-Bootstrap-CSS-Code-Along/tree/main)
- Navigate to the `build` folder and open this project with your code editor
- You will be working in mostly `index.html` and little bit `main.css`
- You have access to the `complete` build, because catching errors in HTML and CSS can be really hard, so you have a solution, even if you have a typo during the class build as a reference.

**Note:** some classes for images are already set so that they are not too large to work with as we build. The images are also set to `width:100px` in the `main.css` until we are ready to size them correctly too. Once they are sized with bootstrap, we can remove this code from the `main.css`

There are a few ways to get Bootstrap into your project. We'll start with just adding a `link` tag. This tag works just like any css you'd write yourself. It is hosted elsewhere on the internet, but it will bring in all the code.
[Get the link](https://getbootstrap.com/docs/5.0/getting-started/introduction/)

Looks like this:

`<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta1/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-giJF6kkoqNQ00vy+HMDP7azOuL0xtbfIcaT9wjKHr8RbDVddVHyTfAAsrekwKmP1" crossorigin="anonymous">`

Paste it inside the `head` tag of the `index.html`

**Note**: when loading more than one css file, it is treated as one long file. Therefore the file that comes last 'wins' if there are the same selectors altering a particular style. Therefore, Bootstrap should come first, then your personal styles should come later. So, make sure your Bootstrap link is above the link for `main.css`

Further, there are more ways to customize bootstrap, but we won't cover it today.

The most notable thing will be that the font has changed.

![](./assets/bootstrap-linked.png)

## Hero

Let's style the main image. It is sometimes referred to as a `hero`, `jumbo` or `jumbotron`image.

Below the `nav`, there is a div with an `id` of hero. Let's add the appropriate class: `container-fluid` - this will make the image stretch 100% of the page, no matter the width.

```html
<div class="container-fluid" id="hero"></div>
```
**Note** - in `main.css` all images are set to `width:100px` - that was just to help us see the starting page. We can remove or comment out that code now. 

Next, in the img inside of that div, add the class `img-fluid` - this will also keep the image at 100% width.

```html
<img
  src="../assets/lauza-loistl-pkFo3Lm-d0s-unsplash.jpg"
  class="img-fluid"
  alt="forest view"
/>
```

## Just Restocked

Move down to the `div` that contains the `h3` that reads `On Sale Now` and add the class `container` - When the width is below a certain amount of pixels, the display will be a wider percentage, when the browser is wider, there will be more space around the sides of the container.

Let's add a class to the `h3` to style it more as a display. Generally, text on a web page serves two purposes - as a title/display eye-catching part of the page, or as text that is meant for easy reading. Adding the class `display-4` will increase the size of the font of the `h3`

## A Row of Cards

If we go back to our starter image we have a row of 3 cards. Let's set up the div that contains these cards as a row.

**Note:** The `...` represents many lines of code that are already there.

```html
<div class="row">...</div>
```
<details><summary>
  Other approaches to consistent design
  </sumary>
 Bootstrap can solve a lot of things, but it can't solve all the things. Here are some things that are better solved in a different way:
  
One image does not have the same aspect ratio as the other two.  When working with images, the best soution is to edit them with a photo/image editor. Many images from places like Unsplash are simply too large in file size for the browser view. Reducing the image sizes to an appropriate for web browsing size will help decrease load times. Additionally, an image editor can help set the correct aspect ratio/sizes so that your images have consistent sizing, which will make yours CSS work much, much easier. 

Another thing we notice is that the text for the Yellow rose, in most views, wraps to another line, also causing our card to lack consistent design. One way we can overcome this is to customize the appearance ourselves
  
 

**main.css**
```
.card-text {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
```
 </details>
 
We then have three cards that we need to add the same styles

- The outer div will be `card col`
- The image inside `card` will be `card-img-top`
- The next div will be `card-body` inside the `card-body`

  - `h5` with a class `card-title`
  - `p` with a class of `card-text`
  - `a` with two classes `btn` and `btn-primary`

  ```html
  <div class="row">
    <!-- ************************** -->
    <!-- First card -->
    <!-- Set classes card col on outer div -->
    <!-- set class card-body on inner div after img -->
    <!-- Set class card-title on h5 -->
    <!-- Set class card-text on p tag -->
    <!-- Set classes btn btn-primary on a tag -->
    <!-- ************************** -->

    <div class="card col" style="width: 18rem">
      <img
        src="../assets/anastasia-zhenina-3YiZ0B2W8c0-unsplash.jpg"
        class="card-img-top"
        alt="Yellow rose"
      />
      <div class="card-body">
        <h5 class="card-title">Yellow</h5>
        <p class="card-text">A perennial showstopper!</p>
        <p>
          <span
            >Photo by
            <a
              href="https://unsplash.com/@disguise_truth?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText"
              >Anastasia Zhenina</a
            >
            on
            <a
              href="https://unsplash.com/s/photos/roses-yellow?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText"
              >Unsplash</a
            >
          </span>
        </p>
        <br />
        <a href="#" class="btn btn-primary">Gift it</a>
      </div>
    </div>
    <!-- Card 2 & Card 3 -->
    <!-- ... -->
  </div>
  ```

The remaining two cards have had the same classes applied in the interest of time.

## Styling a Table - Best Sellers Section

Add a value of `display-5` for the h3 in the Best Sellers section.

```html
<!-- ************************** -->
<!-- Best Sellers table -->
<!-- Set class display-5 on h3 class -->
<!-- ************************** -->

<h3 class="display-5">Best Sellers</h3>
```

Scroll down to the `table`

For certain elements, like `tables`, despite already being a `table` element, you still have to use a class to opt in to Bootstrap's table styling. You will find this true for `button`s and other elements as well.

```html
<table class="table"></table>
```

Let's add another class `table-striped` that will allow for better differentiation between rows

```html
<table class="table table-striped"></table>
```

If we want to center the text elements in our table, we could write our own `css` in the `main.css` file. But it is better to use bootstrap, whenever possible. Bootstrap has a class called `text-center` that will center our text. This will allow for more consistent styling and less unexpected behaviors with our styles.

```html
<table class="table table-striped table-hover text-center"></table>
```

Finally, we can imagine that each row would be a link to an individual view of each tree with more information and the ability to purchase it. To assist our users in navigating the site we can add a hover effect.

```html
<table class="table table-striped table-hover text-center table-hover"></table>
```

## Styling the Form

Below the table, let's add a `class` of `container` to the `div`

In the first two `div`s inside the `form`, add the class `mb-3`- this will add some bottom margins.

`mb` is short for `margin-bottom`, there are others like `mt` for `margin-top`, `ml` - left, `mr` -right, `mx` - left and right (x-axis), `my` - top and bottom (y-axis).

In `mb-3` The value is a multiplier. So if the default margin is 1em, this will multiply the spacer value by 1.

[Learn about the full breakdown here](https://getbootstrap.com/docs/4.0/utilities/spacing/)

### Text Inputs

Add the class `form-label` to the two labels inside the form.

For the two text inputs, add the class `form-control`

This should update our form to look like this:

![](./assets/form-text-input-styled.png)

### Form Checkbox

Add the classes `mb-3 form-check` to style the div that contains the input with type checkbox.

For the input with the type `checkbox` add a class `form-check-input`

For the label add the class `form-label`

### Form Select/Options

In the `select` element, add the class `form-select`

### Checkbox 2 - Style as a Switch

Add the classes `form-check form-switch` to the div that contains an input with type `checkbox`

For the final input with type `checkbox` add the classes `form-check-input`

Finally, let's style the submit button by adding the classes `btn btn-primary`

## Bonus - Finished early?

Try adding different fonts, font color and background-color, to add your own style in the `main.css` file.

You'll see in the `main.css` file that the `nav` `display` is set to `none`. Remove that code and go back to the mockup image. Use the Bootstrap documentation to get it styled like the mockup.

This navigation bar is responsive, so just adding the right classes should automatically change the appearance based on the width of the browser window.

![nav bar short width](./assets/nav-bar-long.png)

![nav bar larger width](./assets/nav-bar-short.png)

## Bonus Bonus

Bootstrap has also been built to integrate with react with custom components. The npm package is called [react-bootstrap ](https://react-bootstrap.github.io/getting-started/introduction).

Rebuild Rosier in React. Be sure to use all the components available (see the left menu under `components`. For example, be sure to check out: 
- cards
- table
- buttons
- forms
- navs


