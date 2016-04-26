---
layout: post
title: Magical world of Metaprogramming
order: 80
---

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
