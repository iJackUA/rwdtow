---
layout: post
title: Alternative frameworks
order: 50
---

Ruby eco-system is not end up with a single RoR. There are a number of frameworks that are truing to propose alternative or lightweight ways to build web apps.
Let's see the most notable:
 
## Full-featured

### [Hanami.rb](http://hanamirb.org/)

It propose cleaner approach with less metaprogramming. The most interesting architectural decisions are: multi app architecture with shared parts (that delays and simplify decision to break your app into smaller parts), Data Mapper/Entity-Repository approach for persistence layer, separating actions, explicit variables exposition to views, etc.

* [What is Hanami? Where is it going?](https://discuss.hanamirb.org/t/what-is-hanami-where-is-it-going/222)

## Specialized 

### [Grape](http://www.ruby-grape.org/)

An opinionated framework for creating REST-like APIs in Ruby. It has built-in support for common conventions, including multiple formats, subdomain/prefix restriction, content negotiation, versioning and much more. All these things are described via simple DSL.
 
## Mini

Mini framework are concentrating to provide us with web requests routing layer. And leaving everything else on us.
All Ruby web frameworks are supporting or based on Rack - a Ruby Webserver Interface (it standartizes the minimal interface to get web .request, handle it and return response). The main thing to know here is that thanks to Rack we can combine a chain of Middlewares (small specific handlers) that gives us such basic things as variables parsing, handling cookies and session, etc - thins that we tend to consider as a "given".

* [dry-web](https://github.com/dry-rb/dry-web)
* [Padrino](http://padrinorb.com/)
* [Rack](http://rack.github.io/)
* [Roda](http://roda.jeremyevans.net)
* [Sinatra](http://www.sinatrarb.com/)
