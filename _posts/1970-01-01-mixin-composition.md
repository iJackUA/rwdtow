---
layout: post
title: Mixin/Module include - it is not composition
order: 90
---

“Divide et impera” (Divide and rule) is a well known saying from ancient Rome. In programming, we also try to break our code into smaller parts, compose them, and reuse them. In Ruby, we can use `include` and `extend` to add methods from other modules. But just because your code is DRY does not mean it exhibits _composition_.

Yes, now your class/object can respond to a new set of messages, but there is a dark side: mixins create implicit (hidden) pollution of an object's public interface. Do these methods really belong to this class/object? You should ask yourself this question every time you use a mixin (e.g. including a Concern in Rails).

Mixins make sense when they use the internal state of an Object (`@attributes` or methods of the host object). It also makes some sense to use them for static helpers that do not use the host object itself, only input parameters.

Real composition should be based on Dependency Injection and Separation of Concerns. Your methods should be grouped by an explicit receiver (e.g not `obj.a_m1`, `obj.a_m2`, `obj.b_m1`, but `obj.a.m1`, `obj.a.m2`, `obj.b.m1`). Composition in this case literally means opting not to mixin `A` and `B`, but to inject instances of `A` and `B`, and assign them to the `a` and `b` properties inside `obj`. In that way, your dependencies are not hard-coded, and you can substitute them dynamically. The main advantage of this approach is that `A` and `B` are self-contained, and can be reused.

Just think about the following use-cases, in the context of a Rails app. Do they really make sense?

* Path helpers mixed into controller/view
* Application helpers mixed into controller/view

Another issue is that mixin modules make too many assumptions about the host class interface. They expect the host class to have certain methods, or even worse, expect that the host class already includes another mixin. These expectations are completely hidden – for example, try to add the `errors` capability from ActiveModel into a PORO.
