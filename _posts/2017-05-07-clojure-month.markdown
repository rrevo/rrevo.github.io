---
layout: post
title:  A Month with clojure
date:   2017-05-07
summary:
tags:   clojure
---

I've been using clojure actively for almost a month now. Here are some of my experiences so far.

## Good

* Getting started with the language is not that hard. Mastery is going to take a long time.
* [Cursive](http://cursive-ide.com/) is a good extension of Idea with clojure support. Additional tooling like build via [Leiningen](https://leiningen.org/), git, debug  just work as expected. 
* [Clojars](http://clojars.org/) is a great resource to find community built clojure libraries. Since leinigen can also download from maven repositories, there is not much of a library problem. There exist quality libraries for web stuff, testing, databases etc.
* [Clojuredocs](https://clojuredocs.org/) is a life saver for understanding functions especially because of the examples.
* [core.async](https://github.com/clojure/core.async) which implements CSP is pretty neat.
* Getting used to basic lisp syntax with parentheses does not take that long.
* Java interop takes some time taking getting used to with the order of object and method name, but it works great.
* Functional programming.
* Focus on data rather than classes.

## Bad

* Server start times are slow. Either leiningen or class generating is taking a whole lot of time. I guess I am not using the REPL correctly. Or I need to figure out hot-reloading of code.
* So far I've not been able to figure our [Paredit mode](https://cursive-ide.com/userguide/paredit.html).
* I've incorrectly matched the braces at times completely changing the meaning of the code. My return value from say a let expression changed completely and I've taken quite a bit of time to get back on track.
* Code becomes pretty compact very easily making it less readable. I've wanted to add log lines in an if-else block and then adding a complete do block feels dirty. Code reviews are also harder as a consequence.
* Java debug fails since generated code reuses the same code line and so next does not work as expected.
* Being a lisp, some libraries are implemented DSLs which makes code less readable initially. Even destructuring trips me off right now. I'm just about getting comfortable with the -> and ->> macros.

Essentially most of my negative comments are early pain points getting started with a language and ecosystem.
