---
layout: post
title: Templates
order: 170
---

Ruby comes with a built-in templating solution, `ERB`, that embeds Ruby into a text document. It has a number of advantages. It is very close to pure HTML, so it is easy to convert HTML into ERB, and it does not introduce any new entry barriers. It is similar to approaches in other languages (PHP, ASP), so it is transferable, common web development knowledge.

Other popular alternative templating solutions include Haml, it's successor Slim, and other Haml-like languages. Slim template code looks like this:

```slim
    div id="footer"
      = render 'footer'
      | Copyright © #{year} #{author}
```

This approach has the advantage of being more clear, due to the removal of "unneeded" elements like closing tags and extra brackets. But the disadvantages are obvious: it has a larger entry barrier, it is harder to migrate HTML markup to it (especially during big redesign sprints), and it is not a transferable skill outside of Ruby.

So stick with ERB unless all of your developers and designers are familiar with Slim, or you are building a home-made, 15-min blog.

* [An Introduction to ERB Templating](http://www.stuartellis.eu/articles/erb/)
* [Slim](http://slim-lang.com/)
