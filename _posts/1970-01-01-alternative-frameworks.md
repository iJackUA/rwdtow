---
layout: post
title: Alternative frameworks
order: 50
---

The Ruby ecosystem does not end at Ruby on Rails. There are a number of alternative frameworks which excel in different areas or provide a more lightweight way to build web apps.
Here are some of the most notable:
 
## Full-featured

### [Hanami.rb](http://hanamirb.org/)

Hanami proposes a cleaner approach with less metaprogramming than Rails. Some interesting architectural decisions are: multi-app architecture with shared parts (that delays and simplify decision to break your app into smaller parts), data mapper/entity-repository approach for persistence layer, separating actions, explicit variables exposition to views and more.

* [What is Hanami? Where is it going?](https://discuss.hanamirb.org/t/what-is-hanami-where-is-it-going/222)

## Specialized 

### [Grape](http://www.ruby-grape.org/)

An opinionated framework for creating REST-like APIs in Ruby. It has built-in support for common conventions, including multiple formats, subdomain/prefix restriction, content negotiation, versioning and much more. All these elements are described via a simple DSL.
 
## Mini

Mini framework focuses on providing a web request routing layer and leaving everything else on us. All Ruby web frameworks are supporting or based on Rack - a Ruby Webserver Interface (it standardizes the minimal interface to get web request, handle it and return response). The main thing to know here is that thanks to Rack we can combine a chain of "middlewares" (small specific handlers) this gives us many basic things such as variable parsing, cookie and session management, etc - things that we tend to consider as a "given".

* [dry-web](https://github.com/dry-rb/dry-web)
* [Padrino](http://padrinorb.com/)
* [Rack](http://rack.github.io/)
* [Roda](http://roda.jeremyevans.net)
* [Sinatra](http://www.sinatrarb.com/)
