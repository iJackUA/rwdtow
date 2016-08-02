---
layout: post
title: Ruby on Rails
order: 40
---

> Most Rails developers do not write object-oriented Ruby code. They write MVC-oriented Ruby code by putting models and controllers in the expected locations. Most will add utility modules with class-methods in lib/, but that’s it. It takes 2-3 years before developers realize: “Rails is just Ruby. I can create simple objects and compose them in ways that Rails does not explicitly endorse!”
– [Mike Perham](https://www.mikeperham.com/2012/05/05/five-common-rails-mistakes/)

RoR is a good starting point for a young web developer, but it is important that our education does not end there. Learn to separate what is pure Ruby and what is Rails. Correctly recognize architectural and design issues that appear in your app, and that most of these issues are caused by misusing oversimplified approaches from Rails.

TODO: Add more points to be aware of in Rails

## Confusing environments

Rails comes with the following ways to alter config according to the environment:

* Environment initializers (in the `config/environments` folder)
* YAML inheritance
* `ENV` variables
* Or you can replace config files on deploy

Often, all of these approaches are mixed without any separation or consideration.
They can usually all be replaced by `ENV` vars that change the value of primitive configs and env initializers, to make bigger structural changes in initialization flow.

## You don't need ActiveRecord for every kind of model

What Rails teaches you first is to model data with AR classes, and that works great while you are following "The Rails Way." Unfortunately, that teaches us to think that model structure is inseparable from the persistence layer (literally from the DB table structure).

That causes issues:

* All data that is not saved to the DB flows into the app as primitive data structures (e.g. Array of Hashes).
* There is a tendency to structure data in a way that is easy to map into a relational DB, rather than a way that clearly reflects the business logic.
* Anything that is not an AR class is put into the `lib` folder.

It is an open secret: even a PORO can be a model, if it encapsulates the data and actions of an entity. Models are not required to have `find` and `save` actions (those are merely the Active Record pattern. Recognize it!). You can, and ideally should, separate models as data containers from other classes that handle persistence. It does not matter whether we get/save it via a DB, or get/send it via a REST API.


## Before filter/action

The main purpose of the `before_action` hook is to authorize the call to the action – literally, to decide to allow this user to run the action or not. But often we can find "pre-population" code like this:

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

Firstly, there is no difference between `before_filter` and `before_action`. They are aliases, and `before_filter` is deprecated in Rails 5.0.

Secondly, do you think this is DRY? No. It is misleading when you forget what `@variables` are available in what actions/views.
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

This does not violate the DRY principle, even if you repeat the `find_item` call 10 times in different actions.

## HTML helpers. Decorators.

This is the first place where young RoR developers put their view helper code. It is fine to do things like this, in the very beginning.

```ruby
module ApplicationHelper
  def page_title(title)
    title += " | " if title.present?
    title += "My Site"
  end
end
```

Unfortunately, helpers become overcomplicated very quickly with:

* a lot of semi-domain logic
* mixed responsibilities
* being included into controllers (because you also want to do some semi-domain logic in controller)
* a lot of HTML concatenation

I almost consider global view helpers as an antipattern which leads to messy code. The solution is to apply the Decorator pattern approach, with view helpers encapsulated in specialized Decorator classes, and helpers that generate a lot of HTML using partials with ERB instead of HTML string concatenation.

A Decorator could be as simple as this:

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

class Sugar < Decorator
  def cost
    super + 0.2
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

Or it could be something more sophisticated, like the functionality provided in the Drapper or Disposable::Twin gems.

* [Evaluating Alternative Decorator Implementations In Ruby](https://robots.thoughtbot.com/evaluating-alternative-decorator-implementations-in)
* [Gem: Draper](https://github.com/drapergem/draper)
* [Gem: Disposable](https://github.com/apotonick/disposable)

## ActiveJob and business logic

ActiveJob is a very convenient tool, but it is very easy to write unmaintenable code with it. When you put your actual Job logic inside `process` method it is very hard to test this logic and impossible to reuse without the ActiveJob context.

The main idea that is hidden behind ActiveJob - it is an application-framework boundary, where the framewrok got data from to "external world" and transfer these data to you application code. It should follow the same rules as controller does. If you follow the idea of "skinny controller", you should also apply the "skinny job" principle. It should not contain SQL queries or business logic manipulations inside, but just call to model or operation object.

Look at the following similiarities of `ActiveJob::process` and `Controller::[action_name]` classes:

* it is the first entry point where your code naturally "gets the wheel" and where you can do all the manipulations
* this method receives parameters from the "outside worlds"
* Browser and Job queue - are representatives of the "outside world"
* controller and job classes do parsing and unification of params for your code

Your job should like as simple as that

```ruby
class BigComplexJob < ApplicationJob
  queue_as :default
 
  def perform(*params)
    MyComplexLogicClass.do_a_lot_of_work(params)
  end
end
```

And you cover `MyComplexLogicClass` with simple Unit tests with the whole set of `params` conditions.

Another aspect is a job queue backend. Delayed Job seems to be a good strating point as it does not require additional infrastructure, just a database. But it quickly became a bottlneck if you have a lot of quick jobs. Redis (im-memory storage) based solutions should be used as more reliable and performant alternatives: [Resque](https://github.com/resque/resque) or [Sidekiq](http://sidekiq.org/).
