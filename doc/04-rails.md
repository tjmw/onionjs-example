# Rails

OnionJS can be used with your web development stack of choice. Now, if you are using Ruby on Rails, here are some tips that may prove useful for you.

## Modules

We like to split our application into **modules**. A module is a set of controller + view + template + stylesheet. I am mentioning stylesheets for the first time, so I'll dedicate a brief paragraph to these.

Stylesheets should contain style rules for the current module and nothing else. Ideally, not even style rules for child modules, although sometimes it's handy to break this rule. If you are familiar with [SMACSS](http://smacss.com/), this should tie in very nicely with our modules. If you are not familiar with SMACSS, go acquaint yourself with it now! It's great.

## Directory structure

In Rails applications, resources such as JavaScript files or CSS stylesheets are placed under 'app/assets'. These are in subdirectories such as 'javascripts' and 'stylesheets'. However, our modules have JS, CSS and Mustache templates all together. Each module is a unit, and it doesn't make sense to put the files in separate directories. Therefore, we prefer to create a new directory in the hierarchy called 'modules' where we can put our modules:

    $ tree -d -L 1 app/assets
    app/assets
    ├── images
    ├── javascripts
    ├── modules
    └── stylesheets

## Configuration

There are three things you need to make this work in Rails. First, make sure that Rails is reading stylesheets from `app/assets/modules`. This would be the way to achieve this using Rails's default `application.css`:

    /* app/assets/stylesheets/application.css */
    /*
     *= require_self
     *= require_tree .
     *= require_tree ../modules
     */

The only line I have added is the one that reads `*= require_tree ../modules`. This will ensure Rails will pick up your module stylesheets and adding them whenever you use `application.css`.

That will do it for the stylesheets. However, the JavaScript files and Mustache templates are not put together by Rails, but instead retrieved using RequireJS. Rails must then be told that it should serve files from the 'modules' directory. Also, it needs to know that '*.mustache' files should be served too. For this, we need to add two lines to `application.rb`:

    config.assets.paths << Rails.root.join('app/assets/modules')
    config.requirejs.logical_asset_filter += [/\.mustache$/]

And you are set! Asumming that RequireJS is working properly, that is...

## RequireJS

If you are familiar with RequireJS, you may want to set it up manually yourself. However, we prefer to use [John Whitley's requirejs-rails gem](https://github.com/jwhitley/requirejs-rails). So add this to your Gemfile:

    gem 'requirejs-rails'

Get your bundle together:

    $ bundle install

Add this to your application.html.erb:

    <%= requirejs_include_tag %>

And you are ready to go!