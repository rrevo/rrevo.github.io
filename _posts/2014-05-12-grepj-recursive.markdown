---
layout: post
title:  "Grepj - Finding classes in jar files"
date:   2014-05-12
---

[grepj](https://github.com/rrevo/grepj) is a tool for finding class files in a jar or war. Essentially it is grep for java classes. This is something that I need often on deployment systems; usually when trying to find which version of a class has been packaged and in which jar.

I recently implemented a feature to search for a class files within nested jars within say a war file. All of these containers are really zip files. So if you look at the code it is recursively opening zip files looking for the name of the file that would correspond to the class name substituting package names for folder names.

Being a language junkie I chose to implement grepj in go. Go is a relatively new language and a project like this helps me learn something new which also building something that is useful. So far just the compilation speed has been a massive plus. The syntax is succinct and the language has a dynamic feel although it is statically typed and also compiles to native binaries. Object orientation with structs' and how composition works is cool though I still need to get a feel for how it would work in a larger project. I also have to figure out concurrency and so many other things.

Building such small tools without any massive architecture brings a certain joy ;)
