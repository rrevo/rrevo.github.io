---
layout: post
title:  Kotlin Example App
tags:   kotlin
---

## Kotlin Example


### Common

Common is the [cross platform code](https://kotlinlang.org/docs/reference/multiplatform.html). It contains the shared data model. Gradle config at `apply plugin: 'kotlin-platform-common'`

Use kotlinx-serialization for serialization

Lots of nulls in the data model.

Date is an `expect class`

Common-jvm (apply plugin: 'kotlin-platform-jvm') and common-js have actual implementations of the Date class. Jvm version uses java.util.Calendar.*. Js version has same thing done by standard library.

### Backend


mainClassName = "org.jetbrains.kotlinconf.backend.ServerKt"

ServerKt {
    val server = embeddedServer(Netty, commandLineEnvironment(args))
    server.start()
}

Use the typesafe.config project - https://github.com/lightbend/config

ktor.application.modules = [ org.jetbrains.kotlinconf.backend.MainKt.main ]
  public final static main(Lio/ktor/application/Application;)V

```
private fun instantiateAndConfigureApplication(classLoader: ClassLoader): Application {
    val application = Application(this)
    safeRiseEvent(ApplicationStarting, application)

    moduleFunctionNames?.forEach { fqName ->
        executeModuleFunction(classLoader, fqName, application)
    }

    if (watchPatterns.isEmpty()) {
        modules.forEach { it(application) }
    }

    safeRiseEvent(ApplicationStarted, application)
    return application
}
```

start causes loading of modules

MainKt.main which has an application

Bytecode is the inverted form of the extension call

Loads of extensions the types Application and Routes

Like Application.main
