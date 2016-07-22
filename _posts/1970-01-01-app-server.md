---
layout: post
title: Know your App Server!
order: 140
---

When you do default run of Rails app it starts a WEBrick server. It is enough for the dev env, but in any case not enough for production. As it runs code in single thread and can not provide concurency in requests handling.

It is very important to understand different operation models of Threaded and Event-loop based servers to select the most reliable and efficient server for your app.

Another aspect is handling static files. Do not give this task to Ruby server, do not waste CPU time. Give this work to reverse proxy servers like Nginx. It will also handle a lot more tings like proxying (e.g. 3000 port to 80 port), multi-domain setup, request per second limit to prevent DDoS, "slow client" attack etc.   

TODO: Make a brief description of different models  

* [Rubyraptor: Description of different operation models in Rack servers](http://www.rubyraptor.org/how-we-made-raptor-up-to-4x-faster-than-unicorn-and-up-to-2x-faster-than-puma-torquebox/)
* [A comparison of Rack web servers for Ruby web applications](https://www.digitalocean.com/community/tutorials/a-comparison-of-rack-web-servers-for-ruby-web-applications)
* [Puma](https://github.com/puma/puma)
* [Unicorn](https://github.com/defunkt/unicorn)
* [Passenger](https://www.phusionpassenger.com/)
* [Nginx](http://nginx.org/)
