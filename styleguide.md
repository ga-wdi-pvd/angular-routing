## Module style conventions

We're still not going to make this module do anything yet. First, we're going to talk about the proper way to write a module. You'll just have to be content that the module isn't throwing errors.

Angular is complex, and as such there's a big movement to standardize how people write it. The go-to style guide is this:

https://github.com/johnpapa/angular-styleguide

> John Papa has, I believe, no relation to Papa John's.

The fact that this thing has almost 17,000 stars on Github should give you an idea of how well-respected and widely-used it is.

As with all style conventions, the ones detailed in here won't impact the performance or functionality of our app at all. The purpose is just to make things easier to read and more standard for developers.

## IIFEs

The "correct" way to write a module is to wrap the whole thing in an IIFE, or an **immediately-invoked function expression**.

Q. What the heck is an IIFE? What's the point?

---

> It's a function that is called as soon as it's defined. The point is that any variables declared inside it won't exist *outside* it. This is useful when you have some procedural code you need to run and don't want a bunch of global variables and functions bogging down the browser.

To use this convention, rewrite our `app.js` to look like the following:

```js
(function (){
  angular
  .module("grumblr", [
    "ui.router"
  ]);
})();
```

We'll be writing everything in Angular like this from now on. Notice things have been spaced out onto separate lines, too.

Q. Why is `ui.router` off on its own line?

---

> Presumably we're going to add more dependencies later on. This way, they're visually in a nice list instead of a big long line.

## [Strictness](https://github.com/ga-wdi-lessons/angular-routing/blob/master/common-problems-with-spas.md)

Now for something weird: let's make the very first line of this file `"use strict";`. You may have seen this when Googling stuff. It looks like we're just writing a weird little random string here. What purpose can it possibly have?

Basically, `"use strict"` forces us to write better Javascript. The big thing here is that it does not allow makes us to use a variable without first declaring it. [There are many other uses as well.](http://www.w3schools.com/js/js_strict.asp)

With all our ducks in a row, we're now ready to make this module actually do something.
