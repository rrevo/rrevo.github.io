---
layout: post
title:  Double Initialization pattern in Java
date:   2014-10-29
---

Java does not have a convenient literal syntax for creating lists or maps. However a neat hack called the Double Initialization pattern allows you to get close to this.

{% highlight java %}

Set<String> names = new HashSet<String>() { {
    add("Siddharth");
    add("Vedant");
} };

{% endhighlight %}

The outer set of braces creates a new Anonymous Inner class. And the second inner set of braces is a initializer block that runs when the instance is created.

The pattern is close enough to a literal list (or map) that is initialized with some values.
 