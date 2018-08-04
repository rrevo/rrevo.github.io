---
layout: post
title:  Boring Software
tags:   architecture
---

I'm growing old. I've worked in companies in different sizes - small, medium and large and have worked on multiple projects - sucessful and failed for long enough that I've started to form strong opinions.

<img src="https://vignette.wikia.nocookie.net/lotr/images/e/e7/Gandalf_the_Grey.jpg/revision/latest/scale-to-width-down/700" width="270" height="350" />

Now, I'm not Gandalf wise.. but at least I'd like to believe that I'm tending there.

## Shiny

In my early days of programming (like most engineers) I was most excited by the latest shiny tools in the [news](http://news.ycombinator.com/).

The software industry similar to the fashion industry (not that I know anything about fashion) always has some fashionable *trend* of the month. Currently [React Native](https://facebook.github.io/react-native/) is passe and native iOS and Android programming is fashionable. GraphQL is cool and REST is not. You've got to be using microservices. And also re-writing in [Kotlin](http://kotlinlang.org).

## Cost $$$

Modern software is complex. I get shocked with the number of dependencies that projects require. With so many dependencies it is hard (I'd argue impossible) to be aware of the complete details of system.

I also need to work on multiple projects at the same time. These projects are in multiple languages. One java project actually uses 3 functional programming libraries. All of these libraries have slightly differerent meanings of flatMap. Essentially the cognitive overhead of all of these choices has an enormous cost in terms of maintenance and shipping new features. In the small, these choices have valid reasons; but in the large they have a massive cost. xkcd had a similar comic on standards-

<img src="https://imgs.xkcd.com/comics/standards.png" />

## Release

Ultimately what we release to customers and at what cost is what matters. We should try and architect simple solutions that meet business requirements. Use proven solutions that you can support. Some better ideas-

* Build monoliths. After understanding usage patterns, you can split into microservices if needed. And if you cannot share libraries effectively to write a good monolith, then you certainly will fail writing microservices
* Understand your stack and failure modes.
* Write good tests and automate builds and deployment.
* Use feature flags and ship and release more often
* Use metrics to understand how code is behaving in production.
* Update dependencies often. Refactor code often
* Release often with minimal changes to catch failures early

If there is a valid need for something new; run verifiable experiments and adapt. Change is necessary. Just don't blindly follow.

## Other Tough problems

A primary reason that engineers like new shiny tools is the challenge of learning something new. As appealing as that it; looking at the bigger picture is also important.

Here are some tougher problems to solve-
* Designing software with [Minimal architecture](https://en.wikipedia.org/wiki/Minimalism)
* Finding product market fit for a problem. Creating something new that is useful is enormously difficult. In a smaller scope, figuring out what features are important to your customers.
* Reducing technical debt actively while shipping new features.
* Reducing cost by standardization and reuse of solutions.
* Building a high performing team.

## More

A fantastic blog by [Dan McKinley](http://mcfunley.com/choose-boring-technology) on choosing boring technology which actually mirrored my thought pattern almost in the same way!

Onwards to even more experiences and learning.
