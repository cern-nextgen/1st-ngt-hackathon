---
title: C++20 introduced “coroutines”
layout: main
section: coroutines
---


In this group, we dealt with [C++20 introduced “coroutines”](https://en.cppreference.com/w/cpp/language/coroutines): 

They are very generic. All aspects of how/what to resume, how the exceptions are handled can be customised in the awaitable classes.

They allow in-thread suspension and resume with a very low overhead.
They are self contained and can be resumed() on different threads. This part is typically handled by libraries managing tasks and  thread pools.

Hopefully they could be used in our offline software for efficient asynchronous task scheduling.

Mateusz’ examples can be found in [this](https://github.com/cern-nextgen/wp1.7-coroutine-tests) repo. 

Toolkits/wrappers/abstraction layers also available:
- [https://github.com/David-Haim/concurrencpp](https://github.com/David-Haim/concurrencpp)
- [https://www.boost.org/doc/libs/1_88_0/libs/cobalt/doc/html/index.html](https://www.boost.org/doc/libs/1_88_0/libs/cobalt/doc/html/index.html)