---
layout: post
title: The magical world of Metaprogramming
order: 80
---

Metaprogramming is a special feature of some languages (including Ruby) used to define code dynamically, at runtime. It is code that generates other code. This is responsible for a lot of the "magic" in Rails, for example: `some_route_path` helpers, and `find_by_%attr_name%` in ActiveRecord.

It seems like an awesome feature at first, until it is misused, and unfortunately it is misused most of the time.
The downsides of metaprogramming are:

* Difficult to locate method source code.
* Hidden intention in the codebase.
* IDEs can't locate these methods for auto-complete.

There is a famous saying: "if you have a problem and think that metaprogramming could help you, then congratulations! Now you have two problems." Many times, the coding challenges that you solve with metaprogramming could be solved in a simpler way, with better code quality, separation of concerns, and clearness.

Here is an example of unnecessary metaprogramming in the [`rest-client`](https://github.com/rest-client/rest-client/blob/master/bin/restclient) gem:

```ruby
POSSIBLE_VERBS = ['get', 'put', 'post', 'delete']

POSSIBLE_VERBS.each do |m|
  define_method(m.to_sym) do |path, *args, &b|
    r[path].public_send(m.to_sym, *args, &b)
  end
end
```

* [Bala Paranj: 7 Deadly Sins of Ruby Metaprogramming](https://www.codeschool.com/blog/2015/04/24/7-deadly-sins-of-ruby-metaprogramming/)
