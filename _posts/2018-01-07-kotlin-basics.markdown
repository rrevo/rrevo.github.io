---
layout: post
title:  Reading Kotlin
date:   2018-01-07
tags:   kotlin
---

[Kotlin](http://kotlinlang.org/) is a programming language that is gaining [popularity](http://steve-yegge.blogspot.com/2017/05/why-kotlin-is-better-than-whatever-dumb.html) of late. Kotlin is a general purpose language which is pragmatic because of java interop and concise in syntax. For me the killer feature of the language is [coroutines](https://kotlinlang.org/docs/reference/coroutines.html). Jetbrains has some great tooling which makes it easier to adopt.

Let me try to explain some kotlin features by looking at some [code](https://github.com/ktorio/ktor/blob/master/ktor-utils/src/io/ktor/util/Text.kt)

{% highlight kotlin %}
fun String.escapeHTML(): String {
    val text :String = this@escapeHTML
    if (text.isEmpty()) return text

    return buildString(length) {
        for (idx :int in 0 until text.length) {
            val ch :Char = text[idx]
            when (ch) {
                '\'' -> append("&apos;")
                '\"' -> append("&quot")
                '&' -> append("&amp;")
                '<' -> append("&lt;")
                '>' -> append("&gt;")
                else -> append(ch)
            }
        }
    }
}
{% endhighlight %}

fun is used to declare a function.

escapeHtml is an [extension function](https://kotlinlang.org/docs/reference/extensions.html#extension-functions) on the String type. It does not take any arguments and returns a String. In bytecode, this becomes a static method in the class io.ktor.util.TextKt with signature 

`public final static escapeHTML(String) -> String`

In the body, text is an immutable reference `val`. The value is set by `this@escapeHtml`. That happens to a an example of a [qualified this expression](https://kotlinlang.org/docs/reference/this-expressions.html#qualified). The value here is this itself or the string instance on which the method escapeHtml is invoked. Examples in the reference documentation show how the this expression can be use to choose scope of this when there are various nested scopes possible.

If empty, then the text is returned as-is.

The next line is a return expression which is very interesting. An inline extension function on StringBuilder, buildString is called. The code for buildString is 

{% highlight kotlin %}
public inline fun buildString(capacity: Int,
    builderAction: StringBuilder.() -> Unit)
    : String =

    StringBuilder(capacity)
      .apply(builderAction)
      .toString()
{% endhighlight %}

buildString takes an int capacity, and a [Function Literals with Receiver](https://kotlinlang.org/docs/reference/lambdas.html#function-literals-with-receiver) on StringBuilder. builderAction is a function on StringBuilder that does not take any additional args and returns void. `this` within the function block of builderAction is a StringBuilder. That's how append from StringBuilder is invoked on the right object.

The simplified implementation for Standard.apply is-

{% highlight kotlin %}
public inline fun <T> T.apply(block: T.() -> Unit): T {
    block()
    return this
}
{% endhighlight %}

Since apply and buildString are `inline`, the code in escapeHtml becomes a for loop along the length of the String in bytecode. Finally toString is called on the StringBuilder which is finally returned.
