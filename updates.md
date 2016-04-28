- basic app
  - ng-app
  - errors
  - IIFEs
  - strictness
- controllers
  - module
    - style conventions
  - index controller
    - ui-sref
    - $index
    - $state
    - $stateParams
  - data
  - show controller
- multiple controllers


- config
  - dependency-injection
  - $stateProvider
  - config functions
  - states
  - ui-view

- Templates

- Framing
  - problems with SPA's
    - bookmarking/deep linking
    - SEO
    - support without JS

---

- Set up Grumblr API
  - $http
- Factories & Services
  - $resource & ngResource
    - https://docs.angularjs.org/api/ngResource/service/$resource
    - http://www.sitepoint.com/creating-crud-app-minutes-angulars-resource/
  - Factory
  - Service
  - What's the Difference?

---

- **Model** - The values that are stored in variables on the scope

- **View** - The loaded, transformed/compiled and rendered DOM

  - **Template** - HTML with additional markup

- **Controller** the business logic behind views

- **Scope** - The context where the model is stored so that controllers, directives and expressions can access it. Scope is the glue between application controller and the view




# Angular and $uiRouter

### Screencasts

- Dec 15, 2015, (Robin)
  - [Part 1](https://youtu.be/FurQ9FGzJwk)
  - [Part 2](https://youtu.be/CtV0ULLlLf0)

### Learning Objectives

- Explain the purpose of templates in Angular
- Use the `ui-view` directive to load angular templates
- Use `$stateProvider` and `$state` to access query parameters and update the URL
- Define multiple controllers in a single module
- Compare '$state' to '$scope'

### Framing (15 / 15)

So far we've been learning about Angular and its awesome power as front-end framework that allows us to easily build Single Page Apps.  

**Q**: What are some of the characteristics of SPAs?

> Single Page Applications are Web apps that load a single HTML page and dynamically update that page as the user interacts with the app. SPAs use AJAX and HTML5 to create fluid and responsive web apps, without constant page reloads.

### Turn and Talk: Problems with SPA's

Take 1 minute to brainstorm any potential problems with SPAs. Then take another minute coming up with a short list with your neighbor; we'll go around and share with the class.

---

### [Common Problems with SPA's](https://github.com/ga-wdi-lessons/angular-routing/blob/master/common-problems-with-spas.md)

Many of the common problems with SPAs have to do with the question of how to manage an application's state. This is most evident with issues related to **bookmarking** and **deep linking**.

# Application State

### Real world example: Trello

In order to get a better grasp of what we mean by an app's state, let take a look at a prolific SPA in the wild, [Trello](https://trello.com/).

Trello is a productivity management tool, that allows a user to have many different "boards", or a list of lists, and each board is made up of different "cards", or items in a list.

Let's play around with a board, and see what happens to the url when we interact with the app.

- **Note** - Changes to the url by default do not alter the app state. Developers had to explicitly connect the 'application state' to the url, so that when things in the app change the changes are reflected in the url.

### [UI Router](https://github.com/angular-ui/ui-router) to the Rescue

Today, we are going to be looking at one wildly popular solution in Angular to help address the problems we've uncovered with SPAs.

Specifically, Angular UI-Router is a client-side SPA routing framework that updates the browser's URL as the user navigates through the app.

As a result, this allows changes to the browser's URL to drive navigation through the app, thus allowing the user to create a bookmark to a location deep within the SPA.

- 'state' of angular app - application state
- state in ui-router

```js
(function() {
  angular
    .module("app", [
      "ui.router"
    ])
    .config([
      "$stateProvider",
      "$locationProvider",
      RouterFunction
    ])

  function RouterFunction($stateProvider, $locationProvider) {

    $locationProvider.html5Mode({
      enabled: true,
      requireBase: false
    });

    $stateProvider
      .state("grumbleIndex", {
        url: "/grumbles",
        template: "<h1>Grumbles Index</h1>"
      })
      .state("grumbleShow", {
        url: "/grumbles/:id",
        template: "<h1>Grumbles Show</h1>"
      });
  }
})();
```
