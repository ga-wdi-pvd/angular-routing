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
    var copy = document.querySelector("[type='checkbox']")
    document.body.addEventListener("copy", function(e){
      if(copy.checked) e.preventDefault()
    })
  });
</script>

<div markdown='1' class='markdown-body'>

# Grumblr Lab

<label>
<input type='checkbox' name='prevent-copy'>
  Prevent copy/paste?
</label>


## Local Setup

<section markdown="1">
To start, let's fork the `grumblr_angular` [repo](https://github.com/ga-wdi-exercises/grumblr_angular), then clone down locally

We can get today's **starter** code by checking out a new branch:

```
$ git checkout -b ui-router 1.0.0
```

Let's start our server locally:

```
$ hs
```
Now if you go to `localhost:8080` you should see your app.

#### Open the file in Atom and your browser

You should see the word Grumblr.

</section>

## Create a module for grumblr and inject `ui.router`

<section markdown="1">

```js
// app.js
angular.module("grumblr", ["ui.router"])
```

Add `ng-app` to index.html

```html
<!-- index.html -->
<body ng-app='grumblr'>
```

</section>

## Configure `ui.router`, and define `Router` function

<section markdown="1">

- Configure the module you just created, by adding a function that will serve as your `Router` function
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

We're just going to make one controller for now: the `index` controller.

In `app.js`, let's add the following code:

```js
.controller("GrumbleIndexController", [
  GrumbleIndexControllerFunction
]);

function GrumbleIndexControllerFunction(){
  console.log("I'm in the controller!");
}
```

</section>


## Use Index controller when url is `/grumbles`

<section markdown="1">

```js
// app.js

function Router($stateProvider){
  $stateProvider
  .state("grumbleIndex", {
    url: "/grumbles",
    controller: "GrumbleIndexController",
    controllerAs: "vm"
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
Visit: http://localhost:8080/#/grumbles

You should see "I'm in the controller!" in the console.

</section>

## Load Template when url is `/grumbles`

<section markdown="1">

We can have Angular load and insert whole HTML files for us -- just like with *partials* in Rails.

Let's create a folder in which we can put some partials:

```
$ mkdir js/ng-views
$ touch js/ng-views/index.html
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
    controllerAs: "vm"
    templateUrl: "js/ng-views/index.html"
  });
}
```

</section>

## When visiting `/grumbles` in url:

<section markdown="1">

- You should see "I'm in the controller" logged to the console.
- You should see "I'm the grumbles index" in the browser

Things to check:

- Are there any errors in the console?

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

```diff
function GrumbleIndexControllerFunction(){
+  this.grumbles = grumbles;
}
```

</section>

## Loop through data and display info about each grumble

<section markdown="1">

```html
<!-- js/ng-views/index.html -->
<div data-ng-repeat="grumble in vm.grumbles">
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

Inside `ng-repeat`, you automatically have access to a variable called [`$index`](https://docs.angularjs.org/api/ng/directive/ngRepeat). This refers to the index of the current item in the thing being repeated.

```html
<h2>I'm the Grumbles index!</h2>
<div data-ng-repeat="grumble in vm.grumbles">
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
    controllerAs: "vm",
    templateUrl: "js/ng-views/index.html"
  })
  .state("grumbleShow", {
    url: "/grumbles/:id",
    controller: "GrumbleShowController",
    controllerAs: "vm",
    templateUrl: "js/ng-views/show.html"
  });
}
```

</section>

##  `show.html` page

<section markdown="1">

```
$ touch js/ng-views/show.html
```

```html
<!-- js/ng-views/show.html -->
The show page
```

</section>


## Grumble Show Controller

<section markdown="1">

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
  templateUrl: "js/ng-views/show.html",
  controller: "GrumbleShowController",
  controllerAs: "vm"
});
```

</section>

## Retrieve index from url with `$stateParams`

<section markdown="1">

Now we need a way of getting the ID from the URL. Angular makes this possible with a module called `$stateParams`, included with `ui.router`. We'll inject it into the controller the same way we injected into the router, and add a `console.log` so we can see what's in `$stateParams`:

```js
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
<!-- js/ng-views/show.html -->
<h2>{% raw %}{{vm.grumble.title}}{% endraw %}</h2>
```

You should see the grumble in the browser

[The solution code is available here.](https://github.com/ga-dc/grumblr_angular/tree/ui-router-solution)

</section>

## Bonus

<section markdown="1">

### You do: CRD Grumbles

This data won't persist since we're not hooked up to a database: refresh the page and it's gone.

But being able to CRD grumbles, even if they just exist until you next refresh the page, will be really useful in doing it for realzies later on!

Thanks to two-way data binding, an "update" button is unnecessary! The grumble is updated automatically as you type.

#### The Create State

1. Create a new state for creating a grumble
1. Create a new controller for creating a grumble
1. Link to the new state from the index state
1. Create a template containing a form for a new grumble
  - add `data-ng-model="GrumbleNewViewModel.newGrumble.title"`
  - add `data-ng-click="GrumbleNewViewModel.create()"`
1. on `ng-submit`
  - access grumble properties with `this.newGrumble` in the controller
  - push the new grumble into the global `grumbles` array
1. `$state.go` to the show view for the new grumble

#### Deleting a grumble

1. Add a link to delete a grumble
1. Add a method on the grumble show controller to remove that grumble from the array

</section>

## Double Bonus

<section markdown="1">

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
