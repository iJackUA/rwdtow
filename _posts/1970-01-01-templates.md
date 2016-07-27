---
layout: post
title: Templates
order: 170
---

Ruby comes with build-in templating solution `ERB`, that embeds Ruby into a text document. And it has a number of advantages. It is very close to pure HTML, that's why it's easy to convert HTML into ERB. Does not introduce any new entry barriers. Similar to other languages (PHP, ASP) approaches, therefore common web dev knowledge are relevant. 

As another popular alternative templating solution is Haml-like languages and it's successor Slim. Template code with Slim looks like this :

```slim
    div id="footer"
      = render 'footer'
      | Copyright © #{year} #{author}
```

Advantages of this approach are more clear markup without "unneeded" elements (like closing tags and extra brackets). But the disadvantages are obvious: bigger entry barrier, harder to migrate HTML markup to it (especially during big redesign sprints), and it's knowledge are barely relevant to other web dev world.

So while you are not completely sure that you are writing home-made 15 min blog or all your developers and markup designers are familiar with Slim -  try to keep up with ERB way.

* [An Introduction to ERB Templating](http://www.stuartellis.eu/articles/erb/)
* [Slim](http://slim-lang.com/)
