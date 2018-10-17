---
layout: post
title: Performance
order: 220
---

As a general rule, if your web page generation time in production is close to one second (or more), that is not perfect. You should invest some time in performance optimization. But before you start optimizing, you should locate the bottlenecks. Almost always, the issues are not where you think they are. Measure!

You can use special profilers that give detailed stats per function call, like PerfTools, or use continuous high-level performance monitoring with widget-like add-ons, like Rack mini profiler. Rack mini profiler will display a toolbar at the top of all pages, with a breakdown of the raw page load time.

There are also external services like New Relic that can gather code runtime performance, even on the production server. It can give general insight into the most time-consuming code and SQL queries.

TODO: Add gems that help to track N+1 queries and SQL performance

* [Rack mini profiler](https://github.com/MiniProfiler/rack-mini-profiler)
* [PerfTools.rb](https://github.com/tmm1/perftools.rb)
* [Peek RBLineProf](https://github.com/peek/peek-rblineprof)
* [New Relic](http://newrelic.com)

## Benchmarks

Sometimes, in the development stage, you need to measure the performance of a couple of different implementations. To get measurable timing results, the code being benchmarked should be run repeatedly, for many iterations. There are ready-made tools that can help with such measurements.

* [Benchmark ips](https://github.com/evanphx/benchmark-ips)
