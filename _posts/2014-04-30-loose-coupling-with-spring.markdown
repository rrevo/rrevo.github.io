---
layout: post
title:  "Loose coupling with Spring"
date:   2014-04-30
summary: Overview of the Spring project and Dependency Injection
---

# Spring project

The [Spring framework](http://projects.spring.io/spring-framework/) is a Dependency Injection framework. Objects define dependencies and the framework then provides (or injects) them at runtime. So instead of tight coupling where you create dependencies the model is inverted thus leading to loose coupling.

Configuration about the java objects (or beans) can be specified in different ways. In earlier versions of Spring xml files contained bean metadata. Annotations are also supported for specifying this information. [JSR 330](https://jcp.org/en/jsr/detail?id=330) added Dependency Injection to java which standardized the annotations for this purpose.

# Example

{% highlight xml %}
<!-- beans.xml -->
<beans xmlns=".." xmlns:xsi=".." xsi:schemaLocation="..">
    <bean id="foo" class="..">
    	<constructor-arg ref="bar" />
    </bean>
    <bean id="bar" class=".." />
</beans>
{% endhighlight %}

In the example above the bean foo will get an instance bar passed in as a constructor argument during creation.

{% highlight java %}
ApplicationContext context = 
	new ClassPathXmlApplicationContext(new String[] {"beans.xml"});
Foo foo = context.getBean("foo", Foo.class);
{% endhighlight %}

This xml file will then be loaded into an ApplicationContext and you can get actual bean instances.

# Imports

For a simple project you may have a single file containing bean definitions. But for larger projects it is possible to split the definitions into multiple files. You can then merge multiple files into using the import tag.

{% highlight xml %}
<!-- beans.xml -->
<beans xmlns=".." xmlns:xsi=".." xsi:schemaLocation="..">
	<import resource="base-beans.xml"/>
</beans>
{% endhighlight %}

Now beans.xml aggregates the definitions from base-beans.xml

# Dynamic injection 

Even more powerful than explicit imports is loading multiple bean definitions in the ApplicationContext. Different bean definitions can be specified in different configuration files. They may reference beans from different places without even knowing how or when the dependencies will be satisfied.


{% highlight xml %}
<!-- beans-a.xml -->
<beans xmlns=".." xmlns:xsi=".." xsi:schemaLocation="..">
    <bean id="bar" class="..">
    </bean>
</beans>

<!-- beans-b.xml -->
<beans xmlns=".." xmlns:xsi=".." xsi:schemaLocation="..">
    <bean id="foo" class="..">
    	<constructor-arg ref="bar" />
    </bean>
</beans>
{% endhighlight %}

In the example above beans-a.xml and beans-b.xml might be declared in different projects altogether, maybe separate maven modules packaged individually. At runtime, the different classes, bean definitions need to be accessible for everything to fall into place.

And how can you wire all of these xml files together?

{% highlight java %}
context.setConfigLocations(new String[] {"classpath*:/beans-*.xml"});
{% endhighlight %}

using wildcards and dynamic loading to complete the magic. 

# References

* [Spring beans](http://docs.spring.io/spring/docs/current/spring-framework-reference/html/beans.html)

