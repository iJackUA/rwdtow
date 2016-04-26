---
layout: post
title: Ruby on Rails
order: 40
---

Most of current Ruby developers came in this ecosystem via Rails. It is a good starting point for a young web developer. But the main task is not to stop our education on it. Learn to separate what is pure Ruby and what is Rails. Correctly recognize architectural and design issues that comes to your app. And that most of these issues are because of Rails oversimplified approaches misuse.

TODO: Add more points to be aware of in Rails

### Confusing environments

Rails comes with the following ways to alter config according to environment

* environment initializers (in `config/environments` folder) 
* YAML inheritance
* `ENV` variables
* or you can replace config files on deploy

Often all these ways are mixed without any separation and consideration.
Mostly all these ways could be replaces by `ENV` vars to change value of primitive configs and env initializers to make bigger structural changes in init flow.

### You don't need an ActiveRecord for every kind of Model

What Rails teachs you the first is to model data by AR classes. And that works great while you are "on the Rails Way". But unfortunately that also teaches to think that Model structure is unbreakable from persistence layer (literally from DB table structure).

That causes issues:

* all data that is not saved into DB directly flows in app as a primitive data (e.g. Array of Hashes)
* tendency to structure data in a way as it easier to map into relational DB, rather than how it is clearer according to Business Logic
* putting anything that is not AR class to `lib` folder

You need to understand that even PORO could be a Model while it encapsulate Data and Actions on some entity. And it is not a must for Model to have `find` and `save` actions (it is merely Active Record pattern, recognize it!). You can (and ideally should) separate Model as data container and some other classes to handle persistency. And it does not matter do we get/save it into DB or get/send it via REST API. 


### Before filter/action

The main purpose of `before_action` hook is to authorize action call. Literally decide allow this user to run the action or not. But often we can find "pre-population" code like this:

```ruby
before_filter :find_item, only: [:update, :show, :edit]
before_action :authenticate_user!

def show
end

private
def find_item
  @iten = Item.find(params[:id])
end
```

1st - there is no difference between before_filter and before_action it is aliases, and `before_filter` is deprecated in Rails 5.0.
2nd - do you think it is DRY? No. It is just misleading code, when you forget what `@variables` are available in what actions/views.
Be explicit in such cases:

```ruby
  before_action :authenticate_user!

  def show
    @item = find_item(params[:id])
  end

  private
  def find_item(id)
    Item.find(id)
  end
```

That does not break DRY principle even if you repeat `find_item` call 10 times in different actions.
