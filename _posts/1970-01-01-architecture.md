---
layout: post
title: Architecture
order: 60
---

MVC is not an app architecture. This idea is not so obvious due to reign of MVC-based frameworks, that teaches us to use Routing + Model + View render as the way to build web apps. Unfortunately Business Logic of the app has no place in this list.

"Fat Model, Skinny Controller" approach also does not solve the issue. It just swipes the dust under the carpet, but you will still suffer from Fat Models with numerous contexts. Changing code for one usage context will break another usage context.

As a general rule in OOP code - you should break the code into smaller classes with smaller responsibilities. Ideally this code should follow SOLID principles ( 
**S**ingle Responsibility, **O**pen-Closed, **L**iskov Substitution, **I**nterface Segregation, and **D**ependency Inversion”).

Also Sandi Matz in the "Practical Object-Oriented Design in Ruby: An Agile Primer" book proposes that being just `SOLID` in not enough, it should be `TRUE`.

> If you define easy to change as
* Changes have no unexpected side effects
* Small changes in requirements require correspondingly small changes in code
* Existing code is easy to reuse
* The easiest way to make a change is to add code that in itself is easy to change.
>
> Then the code you write should have the following qualities. Code should be
>
> * **T**ransparent - The consequences of change should be obvious in the code that is changing and in distant code relies upon it
* **R**easonable - The cost of any change should be proportional to the benefits the change achieves
* **U**sable - Existing code should be usable in new and unexpected contexts
* **E**xemplary - The code itself should encourage those who change it to perpetuate these qualities

TODO: Describe approaches like Form Model, Service Object, Context
* [Gem: Active Interaction](https://github.com/orgsync/active_interaction)

* [SOLID Design Principles](https://www.practicingruby.com/articles/solid-design-principles)

### [Trailblazer](http://trailblazer.to)
 
The most powerful architectural framework to be used independently (with any Ruby web framework) or upon Rails (has special Rails adapters). It provide missing pieces to organize Business Logic. 

* Operation - composable entity to encapsulate some action with context, validation and permission check. Here you should put almost everything you "normally" write in controller.
* Active Record model is left only for simple find/save and managing relations, it is limited to Single Responsibility - data persistency operations 
* Forms are provided per each operation, not to one Context of a Fat Model.
* Cell - encapsulation for small parts of reusable View logic. Replaces messy app helpers.
* Representer - describes presentation rules to serialize and deserialize documents (variety of it's usage is from internal params representation in Operations - to data representation in JSON API). 


**Operation example**

```ruby
# CRUD action Operation
class Comment::Create < Trailblazer::Operation
  include Model
  model Comment, :create

  contract do
    property :body, validates: {presence: true}
  end

  def process(params)
    validate(params[:comment]) do
      contract.save
    end
  end
end

# Run Operation
op = Comment::Create.(comment: {body: "MVC is so 90s."})
# Get a Model from it
model = op.model
```

**Cell example**

```ruby
#Cell class
class Comment::Cell < Cell::ViewModel
    property :body
    property :author

    def show
      render
    end

  private
    def author_link
      link_to author.email, author_path(author)
    end
  end

# Template
- # app/concepts/comment/views/show.haml
  %li
    = body
    By #{author_link}

# Testing
  describe Comment::Cell do
    it do
      html = concept("comment/cell", Comment.find(1)).()
      expect(html).to have_css("h1")
    end
  end
```

**Representable example**

```ruby
# Class
class SongRepresenter < Representable::Decorator
  include Representable::JSON

  property :id
  property :title

  property :artist, decorator: ArtistRepresenter
end

# Serialize
SongRepresenter.new(song).to_json
#=> {"id": 1, title":"Fallout", artist:{"id":2, "name":"The Police"}}

# Restore object
song = Song.new # nothing set.

SongRepresenter.new(song).
    from_json('{"id":1,title":"Fallout",artist:{"id":2,"name":"The Police"}}')

song.artist.name #=> "The Police"
```


Trailblazer (#Trbr) concepts are not so easy to understand and use properly from the very beginning. But it definitely gets more and more sense when you try to get in touch with them. Also it is very hard to describe the whole Trailblazer philosophy in a short text. Nick Shutterer, the author of Trbr, has quite big documentation with detailed description and has written a book with step-by-step manual of building Rails app with Trbr. 

It is definitely worth to try if you want to start making your Ruby apps better.

* [Trailblazer](http://trailblazer.to)
* [Trailblazer Book](https://leanpub.com/trailblazer)


### [ROM.rb](http://rom-rb.org/) (Ruby Object Mapper)

Ruby Object Mapper (ROM) is a Ruby persistence library with the goal to provide powerful object mapping capabilities without limiting the full power of your datastore.

* Isolate the application from persistence details
* Provide minimum infrastructure for mapping and persistence
* Provide shared abstractions for lower-level components
* Provide simple use of the underlying datastore when desired

All ROM components are stand-alone: they are loosely coupled, can be used independently, and follow the single responsibility principle. A single object that handles coercion, state, persistence, validation, and all-important business logic rapidly becomes complex. Instead, ROM provides the infrastructure that allows you to easily create small, dedicated classes for handling each concern individually, and then tie it together in a developer-friendly way.

```
TODO: ROM example
```

* [rom-rb](http://rom-rb.org/)

### [dry-rb](http://dry-rb.org/)

Is a collection of next-generation Ruby libraries, each intended to encapsulate a common task. Decoupled and reusable.

TODO: Add extended dry-rb gems description

* [dry-rb](http://dry-rb.org/)
* [list of all dry gems](http://dry-rb.org/gems/)

### [Rectify](https://github.com/andypike/rectify)

Rectify is a gem that provides some lightweight classes that will make it easier to build Rails applications in a more maintainable way. It's built on top of several other gems and adds improved APIs to make things easier.

Currently, Rectify consists of the following concepts:

* Form Objects
* Commands
* Presenters
* Query Objects

You can use these separately or together to improve the structure of your Rails applications.

The main problem that Rectify tries to solve is where your logic should go. Commonly, business logic is either placed in the controller or the model and the views are filled with too much logic. The opinion of Rectify is that these places are incorrect and that your models in particular are doing too much.

Rectify's opinion is that controllers should just be concerned with HTTP related things and models should just be concerned with data relationships. The problem then becomes, how and where do you place validations, queries and other business logic?

Using Rectify, Form Objects contain validations and represent the data input of your system. Commands then take a Form Object (as well as other data) and perform a single action which is invoked by a controller. Query objects encapsulate a single database query (and any logic it needs). Presenters contain the presentation logic in a way that is easily testable and keeps your views as clean as possible.

Rectify is designed to be very lightweight and allows you to use some or all of it's components. We also advise to use these components where they make sense not just blindly everywhere. More on that later.

Here's an example controller that shows details about a user and also allows a user to register an account. This creates a user, sends some emails, does some special auditing and integrates with a third party system:

```ruby
class UserController < ApplicationController
  include Rectify::ControllerHelpers

  def show
    present UserDetailsPresenter.new(:user => current_user)
  end

  def new
    @form = RegistrationForm.new
  end

  def create
    @form = RegistrationForm.from_params(params)

    RegisterAccount.call(@form) do
      on(:ok)      { redirect_to dashboard_path }
      on(:invalid) { render :new }
      on(:already_registered) { redirect_to login_path }
    end
  end
end
```

The RegistrationForm Form Object encapsulates the relevant data that is required for the action and the RegisterAccount Command encapsulates the business logic of registering a new account. The controller is clean and business logic now has a natural home:

```
HTTP             => Controller   (redirecting, rendering, etc)
Data Input       => Form Object  (validation, acceptable input)
Business Logic   => Command      (logic for a specific use case)
Data Persistence => Model        (relationships between models)
Data Access      => Query Object (database queries)
View Logic       => Presenter    (formatting data)
```


### Learn OOP design

By learning to design small pieces - objects (in OOP), and put them together, you automatically learn how to make good app architecture in total. It is not a magical library just "suddenly" makes your code better, it is whole set of tiny aspects.  

* [Video: SOLID Object-Oriented Design by Sandi Metz](https://www.youtube.com/watch?v=v-2yFMzxqwU)
* [Video: All the Little Things by Sandi Metz](https://www.youtube.com/watch?v=8bZh5LMaSmE)
* [Video: Nothing is Something by Sandi Metz](https://www.youtube.com/watch?v=9lv2lBq6x4A)
* [Video: Therapeutic Refactoring by Katrina Owen](https://www.youtube.com/watch?v=J4dlF0kcThQ)
* [Video: Overkill by Katrina Owen](https://www.youtube.com/watch?v=GWEEPt8VvmU)
* [Book: Objects on Rails by Avdi Grimm](http://objectsonrails.com/)
* [Book: Practical Object-Oriented Design in Ruby (POODR) by Sandi Metz](http://www.poodr.com/)
