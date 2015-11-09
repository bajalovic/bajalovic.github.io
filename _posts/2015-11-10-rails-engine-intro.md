---
published: false
layout: post
date: "2015-11-10 00:38"
tags: rails namespace engine
subclass: post
category: bajalovic
---

You probably already used a lot of gems that are actually a rails engine, e.g. `ActiveAdmin`, `Devise`, `Forem`, `Spree`, ...

I needed to create an independent part on my previous project, and it should not share the resources with the rest of the project. Initially, I thoght to create just a namespace, put controllers, views inside and use it like that. But then, I noticed that I should separate my assets, so this subproject do not load unnecessary javascript/css files. 

Then I remembered, Rails engine will do the trick. It works as a gem, that you can use inside your app, by requiring it in `Gemfile`.

~~~shell
$ cd myapp
$ rails plugin new admin --mountable
~~~

This will generate a `admin/` directory inside your `myapp` directory. It will contain `app`, `bin`, `config`, `lib`, `test` folders and `Gemfile`, `Rakefile` and `admin.gemspec`.

Edit your gemspec file.

Next, open your `Gemfile` and add:

~~~
gem 'admin', path: 'admin'
~~~

This will load your plugin as a gem. Now, we need to tell the router which url to point to our plugin. Run `bundle install`.

~~~ruby
mount Admin::Engine, at: "/admin/"
~~~

If you want to use some external gems, you should define them in `myapp/admin/Gemfile` and add a dependency in `myapp/admin/admin.gemspec`.

Do not forget to run `bundle install` after these changes.

Lets create `welcome_controller.rb` inside the `myapp/admin/app/controllers/admin/welcome_controller.rb`. 

~~~ruby
class Admin::WelcomeController < Admin::ApplicationController

	def index
    end
end
~~~

