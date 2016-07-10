# Angular and $uiRouter

## Learning Objectives

- Explain what dependency injection is and what problem it solves
- Explain the purpose of templates in Angular
- Create separate views and routes for each CRUD action
- Use the `ui-view` directive to load angular templates
- Use `$stateProvider` and `$state` to access query parameters and update the URL
- Define multiple controllers in a single module

## Framing (15 / 15)

So far we've been learning about Angular and its awesome power as front-end framework that allows us to easily build Single Page Apps.  

**Q**: What are some of the characteristics of SPAs?

> Single Page Applications are Web apps that load a single HTML page and dynamically update that page as the user interacts with the app. SPAs use AJAX and HTML5 to create fluid and responsive web apps, without constant page reloads.

### Turn and Talk: Problems with SPA's

Take 1 minute to brainstorm any potential problems with SPAs. Then take another minute coming up with a short list with your neighbor; we'll go around and share with the class.

---

### [Common Problems with SPA's](https://github.com/ga-wdi-lessons/angular-routing/blob/master/common-problems-with-spas.md)

Many of the common problems with SPAs have to do with the question of how to manage an application's state. This is most evident with issues related to **bookmarking** and **deep linking**.

### Application State Exemplified

In order to get a better grasp of what we mean by an app's state, let take a look at a prolific SPA in the wild, [Trello](https://trello.com/).

Trello is a productivity management tool, that allows a user to have many different "boards", or a list of lists, and each board is made up of different "cards", or items in a list.

Let's play around with a board, and see what happens to the url when we interact with the app.

## [UI Router](https://github.com/angular-ui/ui-router) to the Rescue

Today, we are going to be looking at one wildly popular solution in Angular to help address the problems we've uncovered with SPAs.

Specifically, Angular UI-Router is a client-side SPA routing framework that updates the browser's URL as the user navigates through the app.

As a result, this allows changes to the browser's URL to drive navigation through the app, thus allowing the user to create a bookmark to a location deep within the SPA.

## Let's Build an Angular App

Today, we are going to build off of what we learned in the intro class, and represent state utilizing `uiRouter`.

## Please Sit Back and Enjoy the Ride for this one

```
$ git clone https://github.com/ga-wdi-exercises/angular-ui-router-stoplight.git
$ cd angular-ui-router-stoplight/
$ hs -p 9000
$ open http://localhost:9000/
```

### What works so far

Clicking on a bulb illuminates the correct color.

### What we'll do

- Update the URL when clicking on a bulb
- When the page loads, show the color corresponding to the URL
- Let the router decide which controller to use
- Create a `ui-view`

#### Inject the `ui.router` dependency

```js
angular.module("stoplight", ["ui.router"])
```

Here we've done two things: we've created a new module called `grumblr`, and we've told it to require another module called `ui.router`. What we see in the array brackets is Angular's way of "requiring" modules.

##### Dependency Injection

The process of requiring dependencies in Angular is called **dependency injection**. It's an extremely important part of Angular since this framework is all about modules being dependent on other modules.

What happens if we were to remove the Array all together?

We get an error. In order to create a module we have to specify the number of dependencies it has, even if that number is zero.

A `.module` with an array creates a new module; without an array it looks up an existing module of that name.

[Bonus! Use strict && IIFEs](styleguide.md)

###### Configuring ui.router

Remember in Rails we had a `config/routes.rb` file with all of the routes defined in it. Here, we put the `routes` inside a `config` module.

Let's add `.config` to our `app.js`:

```js
angular
.module("stoplight", ["ui.router"])
.config(["$stateProvider", Router])

function Router($stateProvider){

}
```

> **Note**: You'll notice `$stateProvider` is in quotes. You'll also notice that if we were to refresh the page, we'll get an error. That's OK -- we're going to talk about `$stateProvider` first and then we'll address the error.

###### `$stateProvider`

The `ui.router` module we're requiring includes factories, services, providers, and more. We're not going to talk about the differences between those for now. Just know that `$stateProvider` is something `ui.router` gives us to manage **states**.

A **state** in Angular is basically a route: it's an umbrella term for a URL, the view associated with it, and any controllers used in that view.

Modify the `Router` function to look like this:

```js
function Router($stateProvider){
  $stateProvider
  .state("color", {
    url: "/:color"
  });
}
```

We've just defined the first **state**. Remember, we said earlier that a state is a lot like a route in Rails: it's a URL, often with an associated view and controller.

In our browser, let's visit `http://localhost:8080/#/red`. (We'll talk about that weird hashmark in a second.)

.....and we shouldn't see anything exciting.

###### `ui-view`

That's because we haven't told Angular where to "insert" the view in the main page. We need something like `<%= yield %>` in Rails.

To fix this, let's replace the `ng-controller` with `ui-view`:

```html
<div ng-controller='stoplightController as vm'>
<!-- should be: -->
<div ui-view>
```

`index.html` is now like the `application.html.erb` file we had in Rails.

> **Note** that you may see `ng-view` a lot in Angular documentation. This is used with the `ngRoute` module, which is the original built-in router included with Angular. Although it's still supported, Angular itself recommends you use `uiRouter`. They do the same thing -- `uiRouter` is just better.

###### Let the state choose the controller

```js
function Router($stateProvider){
  $stateProvider
  .state("color", {
    url: "/:color",
    controller: "stoplightController",
    controllerAs: "vm"
  });
}
```

###### View the state params

Add `$stateParams` as a dependency to `stoplightController`

```js
.controller("stoplightController", ["$stateParams", stopLightController])
//...
function stoplightController($sateParams){
  console.log($stateParams)
  this.bg = $stateParams.color
}
```

###### Update the url on click

Inject `$state` to the controller

```js
.controller("stoplightController", ["$state","$stateParams", stopLightController])
//...
function stopLightController($state, $stateParams){
  this.bg = $stateParams.color
  this.colors = ['red', 'yellow','green']
  this.turn = function(color){
    $state.go("color",{color: color})
    this.bg = color
  }
}
```

---

## You do: Grumblr

To start, let's fork the `grumblr_angular` [repo](https://github.com/ga-wdi-exercises/grumblr_angular), then clone down locally

We can get today's **starter** code by checking out a new branch:

```
$ git checkout -b ui-router 1.0.0
```

Let's start our server locally:

```
$ http-server
```
Now if you go to `localhost:8080` you should see your app.

### Open the file in Atom and your browser

It's all terribly exciting: the word `Grumblr`, and that's pretty much it.

Grumblr is like Tumblr, only grumblier. Eventually, it will be a two-model CRUD app with posts and comments.

In the coming classes you're going to be interacting with data from an API that we provide. For this class, though, we'll just be hard-coding data and getting some views up and running.

### Steps for completing the assignment:

1. Create a module for grumblr and inject `ui.router`
1. Create an element with `ui-view` in index.html
1. Configure `ui.router`, and define `Router` function
1. When visiting `/grumbles` in url:
  - invoke the grumbles index controller
  - show the grumbles index template

## Create a module

```js
angular.module("grumblr", ["ui.router"]);
```

- In `app.js`: Define a module called `grumblr`, make sure to include any dependencies
- Configure the module you just created, by adding a function that will serve as your `RouterFunction`
  - Make sure to inject `$stateProvider`, and pass it in as an argument to your function
- Make you app "active" by including the necessary directive that links with you app's "main" module

### Templates

We can have Angular load and insert whole HTML files for us -- just like with *partials* in Rails.

Let's create a folder in which we can put some partials:

```
$ mkdir js/grumbles
$ touch js/grumbles/index.html
```

Let's put a piece of HTML into that `index.html`, just so we know it's working:

```html
<h2>I'm the Grumbles index!</h2>
```

```js
function RouterFunction($stateProvider){
  $stateProvider
  .state("grumbleIndex", {
    url: "/grumbles",
    templateUrl: "js/grumbles/index.html"
  });
}
```

- Make a directory for our main resource `grumbles`
- Inside of that directory, add a template for the `index` view
- In your `app.js`, define a `grumbleIndex` state by specifying the associated url and template to render

### Index Controller

We're just going to make one controller for now: the `index` controller:

```
$ touch js/grumbles/index.controller.js
```

This naming convention is used because it puts the `index` controller alphabetically right next to the `index` view. Since each view is going to have a controller, we're not going to put all the controllers together the way we would in Rails: that would result in a lot of jumping around from file-to-file.

In our `index controller`, let's add the following code:

```js
"use strict";

(function(){
  angular
  .module("grumbles")
  .controller("GrumbleIndexController", [
    GrumbleIndexControllerFunction
  ]);

  function GrumbleIndexControllerFunction(){
    console.log("I'm in the controller!");
  }
}());
```

## Moar States

In our application we want to be able to view info about one `grumble`, so let's add a another state for our `show` page.

To do this, we can chain an additional `.state` onto the earlier one:

```js
function RouterFunction($stateProvider){
  $stateProvider
  .state("grumbleIndex", {
    url: "/grumbles",
    templateUrl: "js/grumbles/index.html"
  })
  .state("grumbleShow", {
    url: "/grumbles/:id",
    templateUrl: "js/grumbles/show.html"
  });
}
```

Note: **We can use URL parameters** in Angular the exact same way we did in Rails! The URL parameter in this case is `:id`. It behaves just like in other frameworks.

Now, we need to create a `show.html` page as well:

```
$ touch js/grumbles/show.html
```

## Controllers (10 / 120)

The progression that we're following for creating this app is the same that we would follow for creating a "real" app: creating some routes is an easy win, so we'll do those first and test out the URLs. Then we'll worry about what we're going to do next: putting in data.

**Q**: In Rails, what do you call the thing that controls which data should be available to a user at which route?

---

> A controller.

A controller in Angular is similar: it's where we control which data is available on the view. However, Rails controllers usually contained the logic for a bunch of routes -- that is, a bunch of **actions**. In Angular, it's considered good style to have **one controller for one action**.

**Q**: If we've defined the `show` and `index` actions, how many controllers should we have?

---

> A: Two controllers: one for each action.

> **Note**: `GrumbleIndexControllerFunction` is a super-long function name. A better name might be just `ControllerFunction`. We just used this long name to be a little more explicit for instructional purposes.

Then, in the app's **main** `index.html`, include the controller file right below `grumbles.module.js`:

```html
<script src="js/app.js"></script>
<script arc="js/grumbles/grumbles.module.js"></script>
<script src="js/grumbles/index.controller.js"></script>
```

**Q**: Why isn't there an array in this `.module`?

---

> Because the `grumbles` has already been created; we're adding something *to* the module. Angular thinks any `.module` with an array in it is creating a new module. This is the difference between referencing a module vs. defining a module. Here we need to *get* the module so we can add to it

**Q**: This controller isn't doing anything yet. How can we tell?

---

> A: We'd see "I'm in the controller!" console logged.

To activate this controller, we need to tell the router that it should use this controller for the `grumbleIndex` state:

```js
function RouterFunction($stateProvider){
  $stateProvider
  .state("grumbleIndex", {
    url: "/grumbles",
    templateUrl: "js/grumbles/index.html",
    controller: "GrumbleIndexController"
  })
  .state("grumbleShow", {
    url: "/grumbles/:id",
    templateUrl: "js/grumbles/show.html"
  });
}
```

Now you should see that console logging.


## Instantiation (10 / 140)

So far, we've defined and wired our app together, but how can we get data to display in our view?

When we added `controller: "GrumbleIndexController"` to our `grumbleIndex` state, what's actually going on here is Angular is running `new GrumbleIndexController()`; that is, its using our controller definition as a constructor function to create a new instance of that object.

We can do this with the `controllerAs` syntax:

```js
function RouterFunction($stateProvider){
  $stateProvider
  .state("grumbleIndex", {
    url: "/grumbles",
    templateUrl: "js/grumbles/index.html",
    controller: "GrumbleIndexController",
    controllerAs: "GrumbleIndexViewModel"
  })
  .state("grumbleShow", {
    url: "/grumbles/:id",
    templateUrl: "js/grumbles/show.html"
  });
}
```

Now, add to our `grumbles/index.html`:

```html
<h2>I'm the Grumbles index!</h2>
{{GrumbleIndexViewModel.grumbles}}
```

Refresh, and we should see the text show up!

## Faking data

Let's make this index more index-y so that it shows multiple grumbles.

Normally, at this point, we'd add in some actual data. We're going to be using the Grumblr API eventually, but that involves using factories, which we'll get into in another class.

For now, we're just going to "fake" the data by creating a global variable containing an array of fake grumbles. Just to serve as a reminder that **you would never actually do this in a real app**, we're going to put this fake data in the main `index.html`.

Before `<script src="js/app.js">`, let's add this:

```html
    <script>
      var grumbles = [
        {
          title: "I am Grumble One"
        },
        {
          title: "I'm another Grumble"
        }
      ]
    </script>
    <script src="js/app.js"></script>
```

We can access this global variable in all the other files. Set `this.grumbles` equal to that variable in our controller:

```js
function GrumbleIndexControllerFunction(){
  this.grumbles = grumbles;
}
```

Now, moving back to our view in  `grumbles/index.html`:

```html
<h2>I'm the Grumbles index!</h2>
{{GrumbleIndexViewModel.grumbles}}
```

Instead of printing out the literal data, we want to grab each individual `grumbles`' name...

```html
<div data-ng-repeat="grumble in GrumbleIndexViewModel.grumbles">
  <p>{{grumble.title}}</p>
</div>
```

We have some semblance of an `index` page; let's head toward `show` pages for each `grumble`.

## Display Data in the Index View (5 / 150)

- Define a global variable called `grumbles` in your main `index.html` that is an array of objects with a `title` property
- Attach a property inside the controller function's definition called `grumbles` equal to the global variable you defined before
- Update your `grumbleIndex` state definition to specify what the instance of the controller should be called
- In your `index` view, make each individual `grumble`'s `title` display by accessing the property you defined on the instance of your controller, or viewmodel

### `ui-sref`

Before we make the show pages themselves, we're going to create some links to them.

```html
<h2>I'm the Grumbles index!</h2>
<div data-ng-repeat="grumble in GrumbleIndexViewModel.grumbles">
  <p><a data-ui-sref="grumbleShow({id: 42})">{{grumble.title}}</a></p>
</div>
```

Q. What does `sref` stand for? Where have we seen `grumbleShow` before?

---

> It stands for "state".

We're referring to one of the states defined earlier in the router. This little `sref` thing checks whether or not a state exists, and if it does, it returns the URL for it. If that URL has parameters -- `:id` in this case -- you can supply a value for that parameter and it'll add it into the appropriate place in the URL.

This is cool because I can change the router all sorts of ways and `ui-sref` will still provide the correct URL.

### `$index`

Obviously we don't want to hardcode the ID. The problem is, these grumbles don't actually have IDs -- they're just items in an array.

Q. Hmm. "Items in an array..." What do items in an array have that's sort-of like an ID?

---

> An index.

Inside `ng-repeat`, you automatically have access to a variable called `$index`. This refers to the index of the current item in the thing being repeated.

```html
<h2>I'm the Grumbles index!</h2>
<div data-ng-repeat="grumble in GrumbleIndexViewModel.grumbles">
  <p><a data-ui-sref="grumbleShow({id: $index})">{{grumble.title}}</a></p>
</div>
```

Now you can see the URL of each grumble reflects its index in the global array of grumbles.

**Q**: How would we make the main `Grumblr` header at the top into a link to the index page?

---

> A: `<h1><a data-ui-sref="grumbleIndex">Grumblr</a></h1>`

### [`$state`](http://angular-ui.github.io/ui-router/site/#/api/ui.router.state.$state)

uiRouter also comes out of the box with `$state`, a service responsible for representing states as well as transitioning between them. It also provides interfaces to ask for current state or even states you're coming from.

We can call methods on `$state` to interact with our applications state, as well as trigger which state is active.

For example, the closets thing Angular has to a redirect, like we had in Rails, is by using the `.go` method and passing in the name of a state like so:

```js
"use strict";

(function(){
  angular
  .module("grumbles")
  .controller("GrumbleIndexController", [
    '$state',
    GrumbleIndexControllerFunction
  ]);

  function GrumbleIndexControllerFunction($state){
    this.goToIndex = function(){
      $state.go("grumbleIndex")
    }
  }
}());
```

Important to note, `$state` needs to be added as a dependency, and passed into the function we are going to use it in.  This is most commonly used in controller methods that do back-end data handling such as `create` and `update`, when we to "redirect", or take the user back to a different view.


### Show Page

**This is the point at which tomorrow's lesson plan is going to start.** At this point you'll start pulling in actual API data instead of hardcoded stuff. However, to get some more repetition in playing around with the different pieces of Angular, we'd like you to add in a `show` route, and eventually full CRUD, still using hardcoded data.

Bear in mind, though, that this means you won't be re-using the code you wrote tonight -- so don't get too attached to it. Its purpose is to be a playground for repetition.

---


Now we'll actually make the show pages themselves. This means creating a new controller, because we're doing **one controller, one view**.

```
$ touch js/grumbles/show.controller.js
```

Be sure to include it in your main `index.html`:

```html
<script src="js/app.js"></script>
<script src="js/grumbles/grumbles.module.js"></script>
<script src="js/grumbles/index.controller.js"></script>
<script src="js/grumbles/show.controller.js"></script>
```

#### Set up

To start the show controller, We're just going to copy the index controller. We'll change `index` to `show`, and change `this.grumbles` to `this.grumble` since we're just showing one:

```js
"use strict";

(function(){
  angular
  .module("grumbles")
  .controller("GrumbleShowController", [
    GrumbleShowControllerFunction
  ]);

  function GrumbleShowControllerFunction(){
    this.grumble = {}
  }
}());
```

We'll update the router accordingly to reference the new controller:

```js
.state("grumbleShow", {
  url: "/grumbles/:id",
  templateUrl: "js/grumbles/show.html",
  controller: "GrumbleShowController",
  controllerAs: "GrumbleShowViewModel"
});
```

### `$stateParams`

Now we need a way of getting the ID from the URL. Angular makes this possible with a module called `$stateParams`, included with `ui.router`. We'll inject it into the controller the same way we injected into the router, and add a `console.log` so we can see what's in `$stateParams`:

```js
"use strict";

(function(){
  angular
  .module("grumbles")
  .controller("GrumbleShowController", [
    "$stateParams",
    GrumbleShowControllerFunction
  ]);

  function GrumbleShowControllerFunction($stateParams){
    console.log($stateParams);
  }
}());
```

You can see that it's a small object containing the URL parameters (or the URL's one parameter, in this case).

So, to get the index of the current grumble, you just need `$stateParams.id`:

```js
function GrumbleShowControllerFunction($stateParams){
  this.grumble = grumbles[$stateParams.id];
}
```

Finally, I'll put something in `show.html` to make this actually show up:

```html
<h2>{{GrumbleShowViewModel.grumble.title}}</h2>
```

Wha-bam! You have a little app!

## Bonus

### You do: CRD Grumbles

This data won't persist since we're not hooked up to a database: refresh the page and it's gone.

But being able to CRD grumbles, even if they just exist until you next refresh the page, will be really useful in doing it for realzies later on!

Q. Why are we just doing CRD? Where's the `U`?

---

> Thanks to two-way data binding, an "update" button is unnecessary! The grumble is updated automatically as you type.

To start, here's what you'll need to make "Create" work:

`data-ng-model="GrumbleIndexViewModel.newGrumble.title"`

`data-ng-click="GrumbleIndexViewModel.create()"`

```js
this.newGrumble = {};
this.create = function(){
  grumbles.unshift(this.newGrumble);
  this.newGrumble = {}
}
```

Take these snippets and incorporate them into your Grumblr in the appropriate way. Then, work on updating and deleting grumbles.

[The solution code is available here.](https://github.com/ga-dc/grumblr_angular/tree/ui-router-solution)

## Double Bonus

### $locationProvider

You've probably never seen an Angular app that has hashmarks in its URLs the way we have here. That's because they're ugly and Angular makes them super-easy to remove.

First, inject `$locationProvder` into your router. Then, add `$locationProvider.html5Mode(true)`. The result should be:

```js
// ...
  .config([
    "$stateProvider",
    "$locationProvider",
    RouterFunction
  ]);

  function RouterFunction($stateProvider, $locationProvider){
    $locationProvider.html5Mode(true);
// ...
```

If you refresh the page now and follow the error link, it'll tell you that `$location` needs a `<base>` tag.

> This is a standard but little-used HTML tag, the purpose of which is to say what URL all relative URLs should be based on.

Add this to your main `index.html`, right below the `<title>`:

```html
<base href="http://localhost:8080/" />
```

Go to `localhost:8080` and you should be able to click on URLs without seeing that hash.

### $locationProvider bugs

Note that if you actually type `localhost:8080/grumbles` into your browser's address bar it won't work.

That's because your `http-server` considers that to be a completely different route -- it doesn't know that you actually want `index.html`.

Remember that Angular is geared toward single-page apps. In the "real world", you'd probably have the server redirect every page to `index.html`.

This can cause some bugs due to browser caching. You can mitigate these bugs in Chrome by disabling caching when you have the console open. [This Gif has instructions.](http://i.imgur.com/p2TZixz.gifv)

## Sample Quiz Questions

- What's the difference between `.module("myModule")` and `.module("myModule", [])`?
> The first retrieves an existing module; the second creates a new module.

- What's an Angular "state"?
> A URL with an attached template, and usually a controller.

- Why does a state need both a `controller` and a `controllerAs` parameter?
> The first says which controller should be used; the second says into which variable its new instance (view model) should be saved.

- How many views should be managed by one controller?
> Just one: one view, one controller.

- What's the equivalent of `<%= yield %>` in Angular?
> `data-ui-view`

## Further Reading

- [Angular under the hood](angular_under_the_hood.md)
  - It was originally going to be part of this class, but is maybe a bit tangential -- although pretty cool if you want to get more into the guts of this thing!
- [UI Router Official Resources](https://github.com/angular-ui/ui-router#resources)
- [UI Router Docs](https://github.com/angular-ui/ui-router/wiki)

## Screencasts

- Dec 15, 2015, (Robin)
  - [Part 1](https://youtu.be/FurQ9FGzJwk)
  - [Part 2](https://youtu.be/CtV0ULLlLf0)
