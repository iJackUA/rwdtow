---
layout: post
title: Performance
order: 220
---

As a general rule if your web page generation time in production is close to 1 second (or more) - that is not perfect. And you should invest some time in performance optimisation. But before to start optimization, you should locate the bottlenecks. Almost all the time the issues is not where you think it is. Measure!

You can use special profilers that gives detailed stats per function call like PerfTools. Or make continuos high-level performance monitoring with widget-like addons like Rack mini profiler. It will be displayed as a toolbar on top of all pages. With a raw page load time breakdown. 

There are also external services like New Relic, that can gather code runtime peformance even on production server. It can give general insight on most time consumig code and SQL queries.


TODO: Add gems that help to track N+1 queries and SQL performance

* [Rack mini profiler](https://github.com/MiniProfiler/rack-mini-profiler)
* [PerfTools.rb](https://github.com/tmm1/perftools.rb)
* [Peek RBLineProf](https://github.com/peek/peek-rblineprof)
* [New Relic](http://newrelic.com)

## Benchmarks

Sometimes on development stage you need to measure performance of couple different implementations. To get measurable time frames this code run should be repeated a lot of times. There are ready-made tools that can help with such measurements. 

* [Benchmark ips](https://github.com/evanphx/benchmark-ips)
* [Scientist](https://github.com/github/scientist)
