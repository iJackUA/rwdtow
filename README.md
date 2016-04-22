# Intro
This guide is born after a question "Could you write a list of all the things, that a good RoR developer should know?". I decided to expand it to a whole Ruby Web development and related “Full Stack” skills (but also limit it to "Web", as it is not about Ruby in general).

I am inspired by ["PHP The Right Way"](http://www.phptherightway.com/) guide format (and advises). So this guide also contains sections dedicated to very important aspects of web dev, some explanation (if needed) and list of tutorial links.

Sometimes I will suggest some tools or Gems (with comparison if possible), but it is only to have a starting point. It is up to you to decide "use it or not".

> Important notice. All suggestions in this guide is my personal opinion. It is not an absolute truth or a 100% best practice. I just want to suggest the best I know.

> Also this guide is not complete tutorial - some clear steps like installing Ruby (via `rbenv` or `rvm`), managing dependencies via Bundler, etc. are not described due to wide coverage of these aspects in other tutorials (and there are no fatal issues regarding of e.g. how you install Ruby, if it works – that's fine).

## Why not "Ruby On Rails" and not "The Right Way"?
I am glad you are asking! :)  
It is not a secret that most of Ruby Web Dev came in Ruby via Rails. That is good and bad simultaneously. It simplifies the entry barrier, but also narrows knowledge range. This guide have special RoR section to cover some RoR-specific things, but mostly it will encourage you to look outside of Ruby on Rails and especially “Rails Way”.
And I can't call the way described here as "The Right Way". It is just another way to look on common things.

## Manifesto

* Prefer simple solutions ([Occam's razor](https://en.wikipedia.org/wiki/Occam%27s_razor))
* Configuration over Confusion
* Boilerplate over Magic

# Ground knowledge

## Web
Real shame starts from the very basic of Web development. We are doing web apps, but do not even know how the Internet (or the Web) works. That's why junior developers do not understand where from params appear and what makes different type of HTTP requests different. Unfortunately it prepares the ground to believe in magic in future.

* [Mozilla: How the Web works](https://developer.mozilla.org/en-US/Learn/Getting_started_with_the_web/How_the_Web_works)
* [What really happens when you navigate to a URL](http://igoro.com/archive/what-really-happens-when-you-navigate-to-a-url/)
* [What is HTTP](http://www.jmarshall.com/easy/http/)


## Linux
A lot of us come to web dev with Windows desktops. But even if Ruby can be installed on Windows - I recommend to use Linux machine for Ruby development. Real OS setup or a virtual machine.  
It requires to be familiar with some Linux distribution, for example Ubuntu or some other Top 10 distros.

* [Ubuntu](http://www.ubuntu.com/)
* [DistroWatch TOP](http://distrowatch.com/dwres.php?resource=popularity)

## IDE
You can write Ruby code in any text editor, but using more sophisticated IDE increases productivity.  
Editors like SublimeText and Atom require some additional plugin setup. The most full-featured IDE is RubyMine, but it is not free.  
Due to dynamic composition in Ruby it is hard for IDEs to do correct autocomplete most of the times, that's why RubyMine does not show it's full power. But it still provides quite a lot of additional tools integrated.

* [RubyMine](https://www.jetbrains.com/ruby/index.html)
* [SublimeText](https://www.sublimetext.com/)
* [Atom](https://atom.io/)
* [NetBeans](https://netbeans.org/features/ruby/index.html)

## Gems
Before adding any utility GEMs to the projects, try to search for alternatives and be sure that you chosen minimal solution, that still has developer support and community is contributing in it. Also useful to browse through curated "awesome" list of packages.

* [The Ruby Toolbox](http://www.ruby-toolbox.com/)
* [Curated Awesome Ruby List](http://awesome-ruby.com/)

**Be aware! Don't be Gems obsessed!** Most of the times you can write the code on your own, without any Gems. Our own code is much easier to customize, polish and build in your architecture. And it does only what it suppose to do. No ballast.

And even if you use a Gem, look inside its code, try to understand what it does and how it is implemented. Investigating others open source code gives you couple advantages:

* you can learn how to do something from other good coders
* you don't need a documentation for this Gem (if the code is clear), as you can see all the public interface and usage logic
* or you understand that this Gem is so bad and not optimized, that you definitely need to throw it away and write your own implementation (and you already know at least one way how you should NOT do)


# Ruby on Rails

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


## Alternative frameworks

Ruby eco-system is not end up with a single RoR. There are a number of frameworks that are truing to propose alternative or lightweight ways to build web apps.
Let's see the most notable:
 
### Full-featured

#### [Hanami.rb](http://hanamirb.org/)

It propose cleaner approach with less metaprogramming. The most interesting architectural decisions are: multi app architecture with shared parts (that delays and simplify decision to break your app into smaller parts), Data Mapper/Entity-Repository approach for persistence layer, separating actions, explicit variables exposition to views, etc.

### Specialized 

#### [Grape](http://www.ruby-grape.org/)

An opinionated framework for creating REST-like APIs in Ruby. It has built-in support for common conventions, including multiple formats, subdomain/prefix restriction, content negotiation, versioning and much more. All these things are described via simple DSL.
 
### Mini

Mini framework are concentrating to provide us with web requests routing layer. And leaving everything else on us.
All Ruby web frameworks are supporting or based on Rack - a Ruby Webserver Interface (it standartizes the minimal interface to get web .request, handle it and return response). The main thing to know here is that thanks to Rack we can combine a chain of Middlewares (small specific handlers) that gives us such basic things as variables parsing, handling cookies and session, etc - thins that we tend to consider as a "given".

* [Sinatra](http://www.sinatrarb.com/)
* [Padrino](http://padrinorb.com/)
* [dry-web](https://github.com/dry-rb/dry-web)
* [Rack](http://rack.github.io/)


## Architecture

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

### Learn OOP design

By learning to design small pieces - objects (in OOP), and put them together, you automatically learn how to make good app architecture in total. It is not a magical library just "suddenly" makes your code better, it is whole set of tiny aspects.  

* [Video: SOLID Object-Oriented Design by Sandi Metz](https://www.youtube.com/watch?v=v-2yFMzxqwU)
* [Video: All the Little Things by Sandi Metz](https://www.youtube.com/watch?v=8bZh5LMaSmE)
* [Video: Nothing is Something by Sandi Metz](https://www.youtube.com/watch?v=9lv2lBq6x4A)
* [Video: Therapeutic Refactoring by Katrina Owen](https://www.youtube.com/watch?v=J4dlF0kcThQ)
* [Video: Overkill by Katrina Owen](https://www.youtube.com/watch?v=GWEEPt8VvmU)
* [Book: Objects on Rails by Avdi Grimm](http://objectsonrails.com/)
* [Book: Practical Object-Oriented Design in Ruby (POODR) by Sandi Metz](http://www.poodr.com/)

## Dependency Injection and IoC containers

This technique is widely used in many programming languages from Java to PHP and JavaScript. Briefly it allows to remove direct dependencies in you classes from other classes. But to depend on abstraction. Needed implementation instances of required abstraction should be "injected" in initializer as params - that is Inversion of Control (not you class decides what exactly to instantiate, but "someone" outside of it). But as it is very messy to handle all dependencies manually (especially when you have a cascade dependencies). IoC Container is that mysterious "someone" who instantiate all objects in the app. It gets annotations of all required abstractions from target class and finds implementations for them to inject (very often they are specified in IoC config).
In languages with Interfaces they are treatet as natural abstraction identifier, bur Ruby does not have Interfaces. That's why classes should have some synthetic dependency specification.

Some time ago [DHH wrote a critic about DI usage in Ruby](http://david.heinemeierhansson.com/2012/dependency-injection-is-not-a-virtue.html), but [Piotr Solnica made a reasonable examples](http://solnic.eu/2013/12/17/the-world-needs-another-post-about-dependency-injection-in-ruby.html) of good DI usage in Ruby. Also Sandi Metz in her [POODR book](http://poodr.com) provides a good arguments and examples.

With `dry-container` and `dry-auto_inject` libs DI/IoC in Ruby could looks something like this.

```ruby
my_container = Dry::Container.new

my_container.register(:data_store, -> { DataStore.new })
my_container.register(:user_repository, -> { container[:data_store][:users] })
my_container.register(:persist_user, -> { PersistUser.new })

# set up your auto-injection function
AutoInject = Dry::AutoInject(my_container)

# then simply include it in your class providing which dependencies should be
# injected automatically from the configured container
class PersistUser
  include AutoInject[:user_repository]

  def call(user)
    user_repository << user
  end
end
```
* [David Heinemeier Hansson: Dependency injection is not a virtue](http://david.heinemeierhansson.com/2012/dependency-injection-is-not-a-virtue.html)
* [Piotr Solnica: The World Needs Another Post About Dependency Injection in Ruby](http://solnic.eu/2013/12/17/the-world-needs-another-post-about-dependency-injection-in-ruby.html)
* [Martin Fowler: Inversion of Control Containers and the Dependency Injection pattern](http://www.martinfowler.com/articles/injection.html)
* [dry-container](https://github.com/dry-rb/dry-container)
* [dry-auto_inject](https://github.com/dry-rb/dry-auto_inject)


## Magical world of Metaprogramming

Metaprogramming is a special feature of some language (Ruby is one of them) to make dynamic code definition in runtime. When code generates code. For example in such away made a lot of Rails magic e.g. `some_route_path` helpers, ActiveRecord `find_by_%attr_name%`. 

From the first look it seems an awesome feature. Until it is misused (unfortunately it is... most of the times).
The downsides of metaprogramming are:

* "WTF this method comes from ?" issue
* hard to locate method source code
* IDE autocomplete can't locate these methods

Let me remake one famous saying: "If you have one problem and think that Metaprogramming could help you. Congratulations! Nw you have two problems".
Most of the time coding challenges that you are solving with Metaprogramming could be be solved in a straightforward way. And that will result better code quality, separation of concerns and clearness.

Example of unneeded Metaprogramming in [`rest-client`](https://github.com/rest-client/rest-client/blob/master/bin/restclient) gem.

```ruby
POSSIBLE_VERBS = ['get', 'put', 'post', 'delete']

POSSIBLE_VERBS.each do |m|
  define_method(m.to_sym) do |path, *args, &b|
    r[path].public_send(m.to_sym, *args, &b)
  end
end
```

* [Bala Paranj: 7 Deadly Sins of Ruby Metaprogramming](https://www.codeschool.com/blog/2015/04/24/7-deadly-sins-of-ruby-metaprogramming/)

## Mixin/Module include - is not a composition

Well known practice ancient Rome “Divide et impera” (Divide and rule). In programing we also try to break our code into smaller parts, compose and reuse them. In Ruby we can `include` and `extend` modules to add methods from another modules. But the pure fact that your code is DRY does not allow to call it a "Composition".
Yes now your Class/Object can respond to a new set of messages. But there dark side is - mixin creates implicit (hidden) pollution of Objects public interface. Do these methods really belong to this Class/Object? You should as yourself this question every time when you do a mixin (include a Concern in Rails).

Mixin makes sense when it uses internal state on an Object (operates with `@attributes` or methods of the Host object). But makes a little sense with static helpers that make no use of Object by itself, but use only input params.

Real Composition should be base on Dependency Injection and Separation of Concerns. Your methods should be grouped by explicit receiver (e.g not `obj.a_m1`, `obj.a_m2`, `obj.b_m1`, but `obj.a.m1`, `obj.a.m2`, `obj.b.m1`). And Composition in this case literally means not to mixin `A` and `B`, but to Inject instances of `A` and `B`, and assign them to `a` and `b` properties inside `obj`. In that way you dependencies are not hard encoded, and you can substitute them dynamically. But the main advantage of this approach is that `A` and `B` are self-contained and could be really reused.

Just think about these use-cases (on example of Rails app). Do they really make sense?

* path helpers mixed in Controller/View
* application helpers mixed in Controller/View

Another issue that is introduced by mixins is that mixed class makes to much assumptions about host class interface (expect that host class has some methods). Or even worth expects that host class has already mixed other classes, but these expectations are completely hidden (for example try to add `errors` capability from ActiveModel to PORO).

## Document your code

Code documentation is not only writing a human-readable code comments. It it also about creating functions comments in specific format (like RDoc or YARDOC) that is clear to other developers adn could be read and structured by your IDE.

* [Rails API doc Guideline](http://edgeguides.rubyonrails.org/api_documentation_guidelines.html)
* [RDoc](http://docs.seattlerb.org/rdoc/)
* [YARDOC](http://yardoc.org/)

Remember that it can help your IDE to make correct autocomplete of returned values and object attributes/methods

* [RubyMine: Creating Documentation Comments](https://www.jetbrains.com/help/ruby/8.0/creating-documentation-comments.html#create_tag)
* [RubyMine: Using Annotations](https://www.jetbrains.com/help/ruby/8.0/using-annotations.html?origin=old_help)


## SQL

One of the biggest pluses of Active Record (that provides Rails) is that developer do not need to write raw SQL queries to make basic data manipulation in DB. And like everything in a nature abilities that are not in use are to go away completely. In that way we have a bunch of Rails developers that are scared of anything more complex than `SELECT * FROM users`. That is pretty depressing.

* Learn SQL and particular Databases capabilities!
* In case you use Rails look into Console and try to understand what SQL is composed as a result of given AR query syntax
* Don't start from the "simplest" AR code, that makes a lot of unneeded queries, N+1 queries etc. Think about any data query beforehand. Think in SQL!
* In case you use Rails, use the power of AR query composition, that's fine. You do not need to write raw SQL all the time. But when you need to compose complex query and do not know how to do it with AR - first step do it in SQL, and then gradually transform it into AR notation.
* Some queries (especially those using special abilities of DB, like `PARTITION BY` etc.) better to leave in raw SQL. Think about other developers who will need to support this code. Some things would be clearer in AR notation, some - in pure SQL. Just consider options.   

Also consider that different databases are best suitable for different cases. One web appliction can make use of a few database simultaneosly. Thik about data type you want to operate. You can even combine relational DB (PostgreSQL, MySQL) with document oriented DB (MongoDB). For graph like data ("friends of my friends" relations) there is own class of DBses like Neo4J. There are even multyparadigm DB like ArangoDB.

You should also keep an eye on DB world. It is changing as fast as web languages and frameworks. And it is up to web developer to propose and to use the best data storage accodring to data type and common query patterns.


* [Khan Academy. Intro to SQL: Querying and managing data](https://www.khanacademy.org/computing/computer-programming/sql)
* [PostgreSQL](http://www.postgresql.org/)
* [PostgreSQL Tutorials](http://www.postgresqltutorial.com/)
* [MongoDB](https://www.mongodb.org/)
* [Neo4J](http://neo4j.com/)
* [ArangoDB](https://www.arangodb.com/)


## Debug

Many people are creating a lot of apps without using even standard debugging technics. We tend to stay a "puts debuggerers" because it is easy, stable and "for sure". But we loose troubleshooting productivity a lot without a good debugging skills. First step is to discover Pry `binding.pry` and Byebug `byebug` debuggers. They allow you to set a breakpoint, stop app execution there and make a print of current variables stack. But debugging is not limited by it. The next step is learn how to walk step-by-step inside the app. Step into functions etc.

The best debugging experience can provide visual debugger like in IntelliJ RubyMine IDE. It is based on additional Gem and require to tun our app via this Gem launcher. But as a result you an set breakpoints ring in your IDE and walk through you code inside IDE. While seeing all variables stack on each step. Such a high density of visual debug info can dramatically increase productivity and decrease time to track an error.

* [Tenderlovemaking: I am a `puts` debuggerer](http://tenderlovemaking.com/2016/02/05/i-am-a-puts-debuggerer.html)
* [Debugging Rails Appliction](http://guides.rubyonrails.org/debugging_rails_applications.html)
* [Byebug](https://github.com/deivid-rodriguez/byebug)
* [Pry](https://github.com/pry/pry)
* [IntelliJ RubyMine Visual Debugger](https://www.jetbrains.com/help/ruby/2016.1/debugging.html)


## Tests

Ruby community is well known by it's good testing adoption. For example PHP community is yet not even close the level of understanding how important tests on any stage of app development. And more over not close to actually writing tests as much as Ruby devs do. One of the secrets of such a success is great testing tools, that provide powerful syntax and capabilities mostly due to metaprogramming capabilities and DSL of Ruby.

Don't be so happy to early. As always great power comes with great responsibility. It is two-side sword and you need to recognize good and "not so good" approaches - not to hurt yourself.

Testing rules:

* Test should be simple and obvious. It should not require to write test on test.
* Test only one thing.
* It should do clear assertions.
* It should be predictable and repeatable (produce the same result on each run). 

### Unit tests

Unit tests cover code level logic. It makes some object initialization and makes assertions on result of method or function call. There are two most popular solutions in Ruby: Minitest and RSpec.

**RSpec** is very popular Gem that provide excessive DSL for all testing aspects. It allows to write test in BDD "expects" style. It's assertions are tend to be human readable. There are a lot of special extensions for RSpec. But the main advantage became main drawback - you need to learn a lot of DSL and asserts for different cases. Any etensions of RSpec flow that you want to implement requires quite a big efforts and learning from you.

```ruby
TODO: Provide RSpec example
```

**Minitest** became a part of Ruby standard library and that's why is the most preferable testing way for Gems and libs (as it does not require additional dependencies). Also it provide very small assertions interface, that is easy to learn and adopt. Main advantage of test written with Minitest - it is PORO. That's why you don't need to learn anything new to provide your new assertions or flow - it is just pure Ruby, use the same technics as in your code. Code initialization and calls are almost identical to real usage.
IMHO Minitest is a better choice to write simple, clear and predictable tests. 

```
TODO: Provide Minitest example
```

* [**Minitest**](http://docs.seattlerb.org/minitest/)
* [RSpec](http://rspec.info/) 

## Integration tests

Ruby allows you to write Integration tests. Literally it is very close to real testing of web app in Browser. A set of commands to click on links, visiting the page, fill in test into inputs and validating result of these actions.
It is made by Capibara framework. Basically it uses Rack "fake browser" it makes requests and parse response, but is not capable to run JavaScript. To make full end user browser emulation you need to use Poltergeist or Capybara Webkit add-ons. They run the same commands inside headless Webkit browser. Of course it adds time penalty, tests with JS run slower than without JS. You need to understand that and activate JS per test only in test that requires it.

One thing to understand - Integration test should not expect to make some assertions on internal state of the web app. It should not make assertions on code variables etc. It should assert only on external output body and HTTP response codes.

Also do not overuse Integration test. Keepin mind that in any (ANY!) case Integration test are in magnitude slower than Unit tests. When you write code of bad quality, e.g. put a lot of logic into Controllers - the only way to test it is to `visit` the page with this Controller. You will need to execute the whole app request cycle to test ony tiny things. That should encourage you to separate Logic from the Controller, put it into separate Classes and just combine calls to them into Controller. In such case you can test 90% of the Logic via Unit tests (that are fast) and make only a few "smoke tests" on the action in Controller (to test success call and correct invalid call response, but not all way of calling that part of Logic). 


* [Capibara](http://jnicklas.github.io/capybara/)
* [Poltergeist](https://github.com/teampoltergeist/poltergeist) (PhantomJS)
* [Capibara-WebKit](https://github.com/thoughtbot/capybara-webkit)

### Test data: Fixtures vs Factories

* Factory Girl
* Faker

### Stub external services call

Your test should not depend on availability of external services on each run. Ideally you shoud be able to run all tests without the Internet access. But that doesn't mean that external integrations should not be tested.
If your app makes a request to external service - this request should be Stubbed. Instead of making a real call it should "pretend" and return read made response (there could be couple tests for success and failure response). In Ruby Gems like Webmock and VCR can help you to catch real response for the first time and then to reuse it. Or to compose own response contents.

Stub request with Webmock

```ruby
stub_request(:post, "www.example.com").with(:query => {'user' => 'Ievgen'}).to_return(:body => "Nice work!")
```

Catch requests and return a "fake" response with VCR

```ruby
  VCR.use_cassette("synopsis") do
      response = Net::HTTP.get_response(URI('http://www.iana.org/domains/reserved'))
      assert_match /Example domains/, response.body
    end
```

* [Stubbing external services in Rails](https://semaphoreci.com/community/tutorials/stubbing-external-services-in-rails)
* [Webmock](https://github.com/bblimke/webmock)
* [VCR](https://github.com/vcr/vcr)

### Speed-up test
Write effective tests, mock dependencies, stud behavior, heavy computations and external web calls.
Also you can run tests in parallel, another point to keep them completely independent one from another.

* [Gem: parallel_tests](https://github.com/grosser/parallel_tests)

### Learn Tests Design

To write cost and efforts effective tests (tests that should not be rewriten from scratch after any small refactoring) you should Design your Tests almost as good as you must Design your Code. 

* Cover with tests only public interface of classes
* Don't test tiny sensitive private methods (they are very likely to change often)
* Reuse repetitive test via mixins 
* Avoid hidden testing of the same functionality multiple times (our code should be organized in layers that are testable separately from each other) 

* [Video: The Magic Tricks of Testing by Sandi Metz](https://www.youtube.com/watch?v=URSWYvyc42M)
* [Book: Practical Object-Oriented Design in Ruby (POODR) by Sandi Metz](http://www.poodr.com/)


## Know your Server!

When you do default run of Rails app it starts a WebRick server. It is enough for the dev env, but in any case not enough for production. As it runs code in single thread and can not provide concurency in requests handling. 

It is very important to understand different operation models of Threaded and Event-loop based servers to select the most reliable and efficient server for your app.

Another aspect is handling static files. Do not give this task to Ruby server, do not waste CPU time. Give this work to reverse proxy servers like Nginx. It will also handle a lot more tings like proxying (e.g. 3000 port to 80 port), multi-domain setup, request per second limit to prevent DDoS, "slow client" attack etc.   

TODO: Make a brief description of different models  

* [Rubyraptor: Description of different operation models in Rack servers](http://www.rubyraptor.org/how-we-made-raptor-up-to-4x-faster-than-unicorn-and-up-to-2x-faster-than-puma-torquebox/)
* [A comparison of Rack web servers for Ruby web applications](https://www.digitalocean.com/community/tutorials/a-comparison-of-rack-web-servers-for-ruby-web-applications)
* [Puma](https://github.com/puma/puma)
* [Unicorn](https://github.com/defunkt/unicorn)
* [Passenger](https://www.phusionpassenger.com/)
* [Nginx](http://nginx.org/)


## Know your CLI!

Everybody knows that you should use Rake task to run some code in CLI. But we also need to understand that Rake is not something that comes by default from "Rails". It is external tool and ideally it also should be a matter of consideration to use it or not. Main downside of Rake task is that is hard to test (due to DSL nature it requires some special initialization). You should be very careful while putting some business logic in Rake tasks. It is considered is harmful, as putting logic inside web frameworks Controller.

As with any other tool Rake is not limited to `namespace`, `task` and `desk` - you should learn how to use all benefits and functionality hidden inside. Try to look into advanced Rake docs. 

Also we need to understand that there are much cleaner alternatives like Thor. That provides the same functionality to simplify writing own CLI tools, but do it in much cleaner OOP way.

* [Rake](http://docs.seattlerb.org/rake/)
* [Thor](https://github.com/erikhuda/thor)

## Admin
Prefer solutions that will quiclky generate Admin area instead fo ActiveAdmin-like DSL that makes admin are quickly, but barely customizable

* [**Administrate**](https://github.com/thoughtbot/administrate)
* [Active Admin](http://activeadmin.info/)
* [Rails Admin](https://github.com/sferik/rails_admin)


## Templating

Ruby comes with build-in templating solution `ERB`, that embeds Ruby into a text document. And it has a number of advantages. It is very close to pure HTML, that's why it's easy to convert HTML into ERB. Does not introduce any new entry barriers. Similar to other languages (PHP, ASP) approaches, therefore common web dev knowledges are relevant. 

As another popular alternative templating solution is Haml-like languages and it's successor Slim. Template code with Slim looks like this :

```slim
    div id="footer"
      = render 'footer'
      | Copyright © #{year} #{author}
```

Advantages of this approach is more clear markup without "unneeded" elements (like closing tags and extra brackets). But the disadvantages are obvious: bigger entry barrier, harder to migrate HTML markup to it (especially during big redesign sprints), and it's knowledge are barely relevant to other web dev world.

So while you are not completely sure that you are writing home-made 15 min blog or all your developers and markup designers are familiar with Slim -  try to keep up with ERB way.

* [An Introduction to ERB Templating](http://www.stuartellis.eu/articles/erb/)
* [Slim](http://slim-lang.com/)


## Cache

TODO

From filecache, to Memcached, Redis.
Main rule - cache key computation should not be more complex, than producing cached content by itself.

* [Redis](http://redis.io/)
* [Memcached](https://memcached.org/)

## Fulltext search

TODO

* [PostgreSQL full text seach with ts_vector](http://rachbelaid.com/postgres-full-text-search-is-good-enough/)
* [Sphinx](http://sphinxsearch.com/)
* [Elastic Search](https://www.elastic.co/products/elasticsearch)
* [Algolia](https://www.algolia.com/)

## Use Ruby style guide and style checker

While Ruby does not have a lot of curly brackets `{}`, code style issues are not so sharp as in languages like PHP. But in any case we an write code with different paddings, spaces and line breaks. To avoid confusion and continuos fixes in your team code - you should adopt some styleguide. All team members should be agree and follow it.

Also there are tools like RuboCop that can make automatic static code analysis and propose fixes or recommendations.

* [RuboCop Rails styleguide](https://github.com/bbatsov/rails-style-guide) 
* [GitHub Ruby styleguide](https://github.com/styleguide/ruby)
* [Airbnb Ruby styleguide](https://github.com/airbnb/ruby)
* [RuboCop](https://github.com/bbatsov/rubocop)
* [Reek](https://github.com/troessner/reek)



## JavaScript

TODO: Whole this section :)

### jQuery

### Debug JavaScript

* [Breakpoints](https://developers.google.com/web/tools/chrome-devtools/debug/breakpoints/?hl=en)

### Server-side rendered JS
"replaceWith" trick

### Modern JavaScript
Frameworks, Bower
Modules and loaders
Try to keep away from Sprockets

## HTML / CSS
Bootstrap
Foundation
SemanticUI



## Performance

As a general rule if your web page generation time in production is close to 1 second (or more) - that is not perfect. And you should invest some time in performance optimisation. But before to start optimization, you should locate the bottlenecks. Almost all the time the issues is not where you think it is. Measure!

You can use special profilers that gives detailed stats per function call like PerfTools. Or make continuos high-level performance monitoring with widget-like addons like Rack mini profiler. It will be displayed as a toolbar on top of all pages. With a raw page load time breakdown. 

There are also external services like New Relic, that can gather code runtime peformance even on production server. It can give general insight on most time consumig code and SQL queries.


TODO: Add gems that help to track N+1 queries and SQL performance

* [Rack mini profiler](https://github.com/MiniProfiler/rack-mini-profiler)
* [PerfTools.rb](https://github.com/tmm1/perftools.rb)
* [Peek RBLineProf](https://github.com/peek/peek-rblineprof)
* [New Relic](http://newrelic.com)

### Benchmark 

Sometimes on development stage you need to measure performance of couple different implementations. To get measurable time frames this code run should be repeated a lot of times. There are ready-made tools that can help with such measurements. 

* [Benchmark ips](https://github.com/evanphx/benchmark-ips)
* [Scientist](https://github.com/github/scientist)


## Deployment and Servers

TODO

* [Capistrano](http://capistranorb.com/)

Alternative solutions like Mina do not have any major advantages at the moment.
Or a custom Deploy workflow for big projects with tools like Ansible

### VPS, Dedicated server

* [DigitalOcean](https://m.do.co/c/20534050b97f) (my ref link ;) )
* [Amazon Web Services]()
* [Namecheap, domains registrator](https://www.namecheap.com/?aff=62428) (my ref link ;) )

### PaaS

* [Heroku](https://www.heroku.com/)

### CI 

* [Jenkins](https://jenkins.io/)
* [Travis CI](https://travis-ci.org/)
* [CircleCI](https://circleci.com/)

### DevOps

TODO

* Ansible
* Chef
* Puppet
* Docker

### Local development

TODO

* Vagrant
* Docker

### Local tunnels. Exposing dev env in the Internet

* Ngrok
* Vagrant share

### Protect non-production servers with HTTP Auth

Basic Auth could be used as the main auth way for very simple apps or admin backend. But often it is used to prevent access to stagin servers for strangers and search engines (to prevent indexing of non production serves).

In Rails the simplest basic auth could be added like this 

```ruby
class ApplicationController < ActionController::Base
  http_basic_authenticate_with name: "admin", password: "hunter2"
end
```

More advanced ways could be found in [Rails docs](http://api.rubyonrails.org/classes/ActionController/HttpAuthentication/Basic.html).

In any other Rack based server you can use `Rack::Auth::Basic` middleware

```ruby
use Rack::Auth::Basic, "Restricted Area" do |username, password|
  [username, password] == ['admin', 'pass']
end
```


## Web Application Security

Frameworks do a great job by not only simplifying our web app code, but also by silently providing some security restrictions and best practices in our apps. But this magic security works only while you do everything by default. When you start to do some "advanced" coding, you need to where common web apps vulnerability are hidden and where you need to take care of it.

Common things are:

* SQL injection: unfiltered parameter put into raw SQL query. Hacker can do malicious SQL query.
* CSRF: hacker can force/cheat some user of your app to submit form inside your app with fake data
* XSS: hacker can submit malicious JavaScript into e.g. comments on your site and steal session data of other users via this JavaScript

There are a lot more potentially dangerous things. You can find more info with details on OWASP website.     

* [OWASP Vulnerabilities list](https://www.owasp.org/index.php/Guide_Table_of_Contents)

## Stay open-minded, stay hungry!

Learn other languages to get fresh ideas and orthogonal points of view. Try to learn languages with completely different paradigms e.g. functional languages. It changes the way you look on Object Oriented Languages and approaches (and no, I do not say that you need to break with OOP).

* [Elixir](elixir-lang.org)

## Follow great Ruby developers

Person | Why? | Blog | Twitter| GitHub 
--- | --- | --- | --- | --- |
**Aaron Patterson** | active Ruby ecosystem contributor, great blogger | [tenderlovemaking.com](http://tenderlovemaking.com/) | [@tenderlove](https://twitter.com/tenderlove) | [@tenderlove](https://github.com/tenderlove)
**Luca Guidi** | author of Hanami.rb framework | [lucaguidi.com](https://lucaguidi.com/) | [@jodosha](https://twitter.com/jodosha) | [@jodosha](https://github.com/jodosha)
**Nick Sutterer** | author of Trailblazer | [nicksda.apotomo.de](http://nicksda.apotomo.de/) |[@apotonick](https://twitter.com/apotonick) | [@apotonick](https://github.com/apotonick)
**Piotr Solnica** | author of dry-rb | [solnic.eu](http://solnic.eu/) | [@_solnic_](https://twitter.com/_solnic_) | [@solnic](https://github.com/solnic) 
**Sandi Metz** | author of "POODR" book | [sandimetz.com](http://www.sandimetz.com/) | [@sandimetz](https://twitter.com/sandimetz) | [@skmetz](https://github.com/skmetz)
**Avdi Grimm** | author of "Objects on Rails" book | [devblog.avdi.org](http://devblog.avdi.org/) | [@avdi](https://twitter.com/avdi) | [@avdi](https://github.com/avdi)
**Katrina Owen** | creator of http://exercism.io, great speaker | [kytrinyx.com](http://kytrinyx.com/) | [@kytrinyx](https://twitter.com/kytrinyx) | [@kytrinyx](https://github.com/kytrinyx)

## Community

* [Official Ruby site](https://www.ruby-lang.org/en/)
* [Ruby IRC](irc://irc.freenode.net/ruby)
* [Conferences](https://www.ruby-lang.org/en/community/conferences/)
* [RubyFlow](http://www.rubyflow.com/) - community news

## Books

TODO: Add more books

* [Objects on Rails](http://objectsonrails.com/) by Avdi Grimm.