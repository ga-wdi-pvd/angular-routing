---
---

<link rel='stylesheet' type='text/css' href='http://wdidc.org/~jesse/css/github-markdown.css'>
<style>
  .markdown-body{
    padding:1em;
  }
  section {
    display:none;
  }
  :target + section{
    display:block;
  }
</style>
<script>
  document.addEventListener("DOMContentLoaded", function(){
    var hTags = document.querySelectorAll("h1,h2");
    [].forEach.call(hTags, function(hTag){
      var link  = document.createElement("A");
      link.href = "#" + hTag.id;
      link.classList.add("nav");
      link.textContent  = (hTag.textContent);
      hTag.innerHTML    = link.outerHTML;
    });
  });
</script>

<div markdown='1' class='markdown-body'>

# Grumblr Lab

## Local Setup

<section markdown="1">
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

#### Open the file in Atom and your browser

It's all terribly exciting: the word `Grumblr`, and that's pretty much it.

Grumblr is like Tumblr, only grumblier. Eventually, it will be a two-model CRUD app with posts and comments.

</section>

## Create a module for grumblr and inject `ui.router`

<section markdown="1">

```js
// app.js
angular.module("grumblr", ["ui.router"]);
```

Add `ng-app` to index.html

```html
<!-- index.html -->
<body ng-app='grumblr'>
```

</section>

## Configure `ui.router`, and define `Router` function

<section markdown="1">

- Configure the module you just created, by adding a function that will serve as your `RouterFunction`
  - Make sure to inject `$stateProvider`, and pass it in as an argument to your function

```js
// app.js
angular.module("grumblr", ["ui.router"])
.config(["$stateProvider", Router])

function Router($stateProvider){
  console.log("working")
}
```

Refreshing the page should show "working" in the console.

</section>

## Create Index Controller

<section markdown="1">

We're just going to make one controller for now: the `index` controller:

```
$ touch js/grumbles/index.controller.js
```

This naming convention is used because it puts the `index` controller alphabetically right next to the `index` view. Since each view is going to have a controller, we're not going to put all the controllers together the way we would in Rails: that would result in a lot of jumping around from file-to-file.

In our `index controller`, let's add the following code:

```js
angular
.module("grumblr")
.controller("GrumbleIndexController", [
  GrumbleIndexControllerFunction
]);

function GrumbleIndexControllerFunction(){
  console.log("I'm in the controller!");
}
```

Link to this file in `index.html`

```html
<!-- after app.js -->
<script src='js/grumbles/index.controller.js'></script>
```

</section>


## Use Index controller when url is `/grumbles`

<section markdown="1">

```js
// app.js

function RouterFunction($stateProvider){
  $stateProvider
  .state("grumbleIndex", {
    url: "/grumbles",
    controller: "GrumbleIndexController",
    controllerAs: "GrumbleIndexViewModel"
  });
}
```


</section>

## Create an element with `ui-view` in index.html

<section markdown="1">

```html
<!-- ./index.html -->
<div ui-view></div>
```
Visit: http://localhost:9000/#/grumbles

You should see "I'm in the controller!" in the console.

</section>

## Load Template when url is `/grumbles`

<section markdown="1">

We can have Angular load and insert whole HTML files for us -- just like with *partials* in Rails.

Let's create a folder in which we can put some partials:

```
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
    controller: "GrumbleIndexController",
    controllerAs: "GrumbleIndexViewModel"
    templateUrl: "js/grumbles/index.html"
  });
}
```

</section>

## When visiting `/grumbles` in url:

<section markdown="1">

- You should see "I'm in the controller" logged to the console.
- You should see "I'm the grumbles index" in the browser

Things to check:

- Did you link to all your js files in index.html
- Are there any errors in the console?

```html
<script src="js/app.js"></script>
<script src="js/grumbles/index.controller.js"></script>
```
</section>

## Load fake data into index controller

<section markdown="1">

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
// js/grumbles/index.controller.js

function GrumbleIndexControllerFunction(){
  this.grumbles = grumbles;
}
```

</section>

## Loop through data and display info about each grumble

<section markdown="1">

```html
<!-- js/grumbles/index.html -->
<div data-ng-repeat="grumble in GrumbleIndexViewModel.grumbles">
  <p>{% raw %}{{grumble.title}}{% endraw %}</p>
</div>
```

You should see each of the grumbles when you refresh the page.

</section>

## Link to a show page

<section markdown="1">

Using `ui-sref`

Before we make the show pages themselves, we're going to create some links to them.

The problem is, these grumbles don't actually have IDs -- they're just items in an array.

Inside `ng-repeat`, you automatically have access to a variable called `$index`. This refers to the index of the current item in the thing being repeated.

```html
<h2>I'm the Grumbles index!</h2>
<div data-ng-repeat="grumble in GrumbleIndexViewModel.grumbles">
  <p><a data-ui-sref="grumbleShow({id: $index})">{{grumble.title}}</a></p>
</div>
```

**We just invented `grumbleShow`** - define the state in the next section.

</section>

## Show page for each grumble

<section markdown="1">

In our application we want to be able to view info about one `grumble`, so let's add a another state for our `show` page.

To do this, we can chain an additional `.state` onto the earlier one:

```js
function Router($stateProvider){
  $stateProvider
  .state("grumbleIndex", {
    url: "/grumbles",
    controller: "GrumbleIndexController",
    controllerAs: "GrumbleIndexViewModel",
    templateUrl: "js/grumbles/index.html"
  })
  .state("grumbleShow", {
    url: "/grumbles/:id",
    controller: "GrumbleShowController",
    controllerAs: "GrumbleShowViewModel",
    templateUrl: "js/grumbles/show.html"
  });
}
```

</section>

##  `show.html` page

<section markdown="1">

```
$ touch js/grumbles/show.html
```

```
<!-- js/grumbles/show.html -->
The show page
```

</section>


## Grumble Show Controller

<section markdown="1">
```
$ touch js/grumbles/show.controller.js
```

Be sure to include it in your main `index.html`:

```html
<script src="js/app.js"></script>
<script src="js/grumbles/index.controller.js"></script>
<script src="js/grumbles/show.controller.js"></script>
```

#### Set up

To start the show controller, We're just going to copy the index controller. We'll change `index` to `show`, and change `this.grumbles` to `this.grumble` since we're just showing one:

```js
angular
.module("grumblr")
.controller("GrumbleShowController", [
  GrumbleShowControllerFunction
]);

function GrumbleShowControllerFunction(){
  this.grumble = {}
}
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
</section>

## Retrieve index from url with `$stateParams`

<section markdown="1">

Now we need a way of getting the ID from the URL. Angular makes this possible with a module called `$stateParams`, included with `ui.router`. We'll inject it into the controller the same way we injected into the router, and add a `console.log` so we can see what's in `$stateParams`:

```js
angular
.module("grumblr")
.controller("GrumbleShowController", [
  "$stateParams",
  GrumbleShowControllerFunction
]);

function GrumbleShowControllerFunction($stateParams){
  console.log($stateParams);
  this.grumble = {}
}
```

You can see that it's a small object containing the URL parameters (or the URL's one parameter, in this case).

So, to get the index of the current grumble, you just need `$stateParams.id`:

```js
function GrumbleShowControllerFunction($stateParams){
  this.grumble = grumbles[$stateParams.id];
}
```
</section>

## Show template

<section markdown="1">

```html
<h2>{% raw %}{{GrumbleShowViewModel.grumble.title}}{% endraw %}</h2>
```

You should see the grumble in the browser

</section>

## Bonus

<section markdown="1">

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
</section>

</div>
