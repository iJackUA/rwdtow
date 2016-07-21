---
layout: post
title: Ruby on Rails
order: 40
---

> Most Rails developers do not write object-oriented Ruby code. They write MVC-oriented Ruby code by putting models and controllers in the expected locations. Most will add utility modules with class-methods in lib/, but that’s it. It takes 2-3 years before developers realize: “Rails is just Ruby. I can create simple objects and compose them in ways that Rails does not explicitly endorse!”
[&copy; Mike Perham](https://www.mikeperham.com/2012/05/05/five-common-rails-mistakes/)

RoR is a good starting point for a young web developer. But the main task is not to stop our education on it. Learn to separate what is pure Ruby and what is Rails. Correctly recognize architectural and design issues that comes to your app. And that most of these issues are because of Rails oversimplified approaches misuse.

TODO: Add more points to be aware of in Rails

## Confusing environments

Rails comes with the following ways to alter config according to environment

* environment initializers (in `config/environments` folder) 
* YAML inheritance
* `ENV` variables
* or you can replace config files on deploy

Often all these ways are mixed without any separation and consideration.
Mostly all these ways could be replaces by `ENV` vars to change value of primitive configs and env initializers to make bigger structural changes in init flow.

## You don't need an ActiveRecord for every kind of Model

What Rails teachs you the first is to model data by AR classes. And that works great while you are "on the Rails Way". But unfortunately that also teaches to think that Model structure is unbreakable from persistence layer (literally from DB table structure).

That causes issues:

* all data that is not saved into DB directly flows in app as a primitive data (e.g. Array of Hashes)
* tendency to structure data in a way as it easier to map into relational DB, rather than how it is clearer according to Business Logic
* putting anything that is not AR class to `lib` folder

You need to understand that even PORO could be a Model while it encapsulate Data and Actions on some entity. And it is not a must for Model to have `find` and `save` actions (it is merely Active Record pattern, recognize it!). You can (and ideally should) separate Model as data container and some other classes to handle persistency. And it does not matter do we get/save it into DB or get/send it via REST API. 


## Before filter/action

The main purpose of `before_action` hook is to authorize action call. Literally decide allow this user to run the action or not. But often we can find "pre-population" code like this:

```ruby
before_filter :find_item, only: [:update, :show, :edit]
before_action :authenticate_user!

def show
end

private
def find_item
  @item = Item.find(params[:id])
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

## HTML helpers. Decorators.

This is the first place where young RoR developer puts his view helper code. It is fine for the very beginning to do things like this.

```ruby
module ApplicationHelper
  def page_title(title)
    title += " | " if title.present?
    title += "My Site"
  end
end
```

But unfortunately helpers became overcomplicated very fast with

* a lot of semi-domain logic
* mixed responsibility
* being included into controllers (because you also want to do some semi-domain logic in controller)
* a lot of HTML concatenation

I consider global view helpers almost as an antipattern, that leads to a code mess. The solution is to apply Decorator pattern approach. With view helpers encapsulated in specialized Decorator classes and helpers that generates a lot of HTML use partials with ERB instead of HTML strings concatenation.

Decorator could be as simple as that 

```ruby
class Coffee
  def cost
    2
  end

  def origin
    "Colombia"
  end
end

require 'delegate'

class Decorator < SimpleDelegator
  def class
    __getobj__.class
  end
end

class Milk < Decorator
  def cost
    super + 0.4
  end
end

coffee = Coffee.new
Sugar.new(Milk.new(coffee)).cost   # 2.6
Sugar.new(Sugar.new(coffee)).cost  # 2.4
Milk.new(coffee).origin            # Colombia
Sugar.new(Milk.new(coffee)).class  # Coffee
```

or it could be something more sophisticated like provided via gems Drapper or Disposable::Twin

* [Evaluating Alternative Decorator Implementations In Ruby](https://robots.thoughtbot.com/evaluating-alternative-decorator-implementations-in)
* [Gem: Draper](https://github.com/drapergem/draper)
* [Gem: Disposable](https://github.com/apotonick/disposable)
