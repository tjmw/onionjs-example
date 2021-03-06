# Instroduction to OnionJS

## Our first app

This is about the simplest possible app you can write with OnionJS. It's split into several files:

### The base HTML file

    <!-- index.html -->
    <div id="first-app"></div>
    <script>
    require(['first/first_controller'], function(FirstController) {
      new FirstController().appendTo('#first-app')
    })
    </script>

### The controller

    // first/first_controller.js
    define([
      'onion/controller',
      'first/first_view'
    ], function (
      Controller,
      FirstView
    ) {

      return Controller.sub('FirstController')

        .view(FirstView)

        .after('init', function () {
          this.view.render({me: 'first'})
        })

    })

### The view

    // first/first_view.js
    define([
      'onion/mustache_view',
      'onion/vendor/text!first/first.mustache'
    ], function (
      MustacheView,
      template
    ) {

      return MustacheView.sub('FirstView')

        .template(template)

    })

### The template

    {{! first/first.mustache }}
    <div class="first-view">
      <p>Hello! Look at me I'm a {{me}}!</p>
    </div>

## How does the above actually work?

First, some quick notes:

* Applications are made up of **controllers**. These form its basic skeleton.
* Each **controller** has a **view**. This is in charge of its presentation.
* OnionJS provides `MustacheView`, a type of **views** that use [Mustache templates](http://mustache.github.io/).

With the above in mind, let's revisit the code, this time with commentary.

### The base HTML file

Using [RequireJS](http://requirejs.org), we load the controller, instantiate it, and append it to a div on the page. All contents rendered by the app will be contained in the `#first-app` div.

    <div id="first-app"></div>
    <script>
    require(['first/first_controller'], function(FirstController) {
      new FirstController().appendTo('#first-app')
    })
    </script>

### The controller

Again using RequireJS, we define a module for the controller. The dependencies are:

* `onion/controller`: the base class for controllers in OnionJS
* `first/first_view`: the view we will assign to this controller

Pay attention to the syntax used to declare a subclass of `Controller`. We do `Controller.sub('FirstController')`, which returns a class that we can extend. However we don't do any extending in this example. Instead we simply "decorate" the `init` method (part of `Controller`).

Using OnionJS's declarative syntax, we assign the view to the controller. Then, we wait for initialisation and ask the view to render itself, with an argument `{me: 'first'}`.


    // first/first_controller.js
    define([
    'onion/controller',
      'first/first_view'
    ], function (
      Controller,
      FirstView
    ) {

      // FirstController is a subclass of Controller
      return Controller.sub('FirstController')

        // Declare FirstView as our view
        .view(FirstView)

        // When you are done initialising, tell the view to render
        .after('init', function () {
          this.view.render({me: 'first'})
        })

    })

### The view

Many views are very simple and just delegate all work on the template. This is one such example.

As before, we declare a module and load its dependencies. These are:

* `onion/mustache_view`: a base class for views that use Mustache templates.
* `onion/vendor/text!first/first.mustache`: the template to render.

All we do here is declaring the view and telling it to use the correct template:

    // first/first_view.js
    define([
      'onion/mustache_view',
      'onion/vendor/text!first/first.mustache'
    ], function (
      MustacheView,
      template
    ) {

      // FirstController is a subclass of MustacheView
      return MustacheView.sub('FirstView')

        // Declare template as our template (d'oh!)
        .template(template)

    })

### The template

The template is extremely simple. Remember the first line is just a comment! (That's what comments look like in Mustache).

All there is to this template is that it renders the variables that were passed in. In this case, it's just the `{{me}}` tag, rendered as the value `first`. This is what we passed from the controller: `{me: 'first'}`.

    {{! first/first.mustache }}
    <div class="first-view">
      <p>Hello! Look at me I'm a {{me}}!</p>
    </div>

After rendering, the result will be this:

    <div class="first-view">
      <p>Hello! Look at me I'm a first!</p>
    </div>

### Back to the view

One last thing has to happen: the result of rendering the template has to go somewhere! You may remember that we started by appending the controller to a div with id `first-app`. The controller told the view about this, and it's here that the view will be showing itself. Therefore, this will be the final result:

    <div id="first-app">
      <div class="first-view">
        <p>Hello! Look at me I'm a first!</p>
      </div>
    </div>

Bear in mind that any contents inside `#first-app` will be deleted before inserting the new content. This will happen every time that `render` is called.