---
layout:  post
title:   Getting Started with OrientDB
date:    2015-04-05
summary: First experiments with a Graph database
---

I've been looking around for a new NoSQL database for a new application. Based on some preliminary [Graph databases](https://en.wikipedia.org/wiki/Graph_database) fit the sort of requirements that I have. (Then again every model is a Graph!) In particular I am looking at the implementations of the [Property Graph Model](https://github.com/tinkerpop/blueprints/wiki/property-graph-model). 

### Property Graphs

![Property Graph](http://dev.assets.neo4j.com.s3.amazonaws.com/wp-content/uploads/graphdb-gve.png)

A graph contains Vertices (or Nodes) connected via Edges (or Relationships). Vertices and Edges have labels (or identifiers). They can contain properties or a map of keys to values.

### Enter OrientDB

OrientDB is an [open-source](https://github.com/orientechnologies/orientdb), java based, Graph + Document database. They actually have a long list of [features](http://www.orientechnologies.com/why-orientdb/). Importantly OrientDB supports the [Tinkerpop API](https://github.com/tinkerpop/blueprints) which is supported by multiple Graph databases.

While OrientDB seems to have fair [documentation](http://www.orientechnologies.com/docs/last/index.html) I found it extremely confusing. They have many features and alternate APIs. In this blog I am going to provide a quick overview and focus only on getting started with the Graph API.

### Code

This blog post is backed by code on [github](https://github.com/rrevo/orientdb-example). Check the project out for complete working code samples.

### Architecture

<img src="{{ site.url }}{{ site.baseurl }}/imgs/orientdb-api-stack.png" />

There are 3 possible [storage types](http://www.orientechnologies.com/docs/last/Memory-storage.html) or engines-

* In memory
* Local database
* Remote server

The multiple APIs available are-

* Graph API compliant with TinkerPop.
* Document API.
* Object API which is JPA like.

### Getting started

#### Dependencies

Include the maven dependency [com.orientechnologies:orientdb-graphdb](http://mvnrepository.com/artifact/com.orientechnologies/orientdb-graphdb) to get started using OrientDB in your application. This pulls in a bunch of dependencies that can possibly be trimmed for production.

### Create a connection

Initialize a factory for graph connections in the future using the OrientGraphFactory. And obtain a transaction aware connection to the database by getting an OrientGraph instance.

{% highlight java %}
// Once in the application
OrientGraphFactory factory = new OrientGraphFactory("url");

// Get connection to database 
OrientGraph graph = factory.getTx();
try {
  ...
} finally {
  // Return connection to pool
  graph.shutdown();
}
{% endhighlight %}

### Create Vertex and Edges

OrientGraph has various APIs for graph operations

{% highlight java %}
OrientGraph graph = ...;

# Create vertex
Vertex v1 = graph.addVertex(null);
# Add property
v1.setProperty("name", "v1");

Vertex v2 = ...;

# Create an Edge from v1 to v2
Edge e = graph.addEdge(null, v1, v2, "relationship");

{% endhighlight %}

### Iterating and other operations

The Graph returns a list of all Vertices.

{% highlight java %}
OrientGraph graph = ...;

for (Vertex v : graph.getVertices()) {
  ...
}
{% endhighlight %}

Other CRUD operations on Vertices, Edges and their properties also exist.

### Java object transformation

In the example above I have shown the base API to create a common property based Vertex and Edge. However a better API is using POJO's that contain the fields for the model and then transparently persist them into the map.

TinkerPop frames allow transformation from a POJO to a Vertex and vice-versa. API example is-

    Graph graph = ... ;
    FramedGraphFactory factory = new FramedGraphFactory();
    FramedGraph<Graph> framedGraph = factory.create(graph);

    Foo f = framedGraph.getVertex(id, Foo.class);

[Frames documentation](https://github.com/tinkerpop/frames/wiki/Getting-Started) contains an example where annotations are used to describe the fields and relationships.

To access the underlying API, any class can be cast to a VertexFrame which can return the underlying Vertex.

Manually creating mapper classes is another option to convert from POJO's to Graph classes.

OrientDB also allows for an actual [Schema](http://www.orientechnologies.com/docs/last/orientdb.wiki/Graph-Schema.html). I'll need to investigate how this can be used.

### WIP

I'll be updating this blog with more basic APIs.

### Unknowns

* OrientDB seems to support so many features. How is performance?
* Hazelcast
* Indexes
* OrientDB schema-classes
* SQL

### Other references

* [Java API](http://www.orientechnologies.com/docs/last/Java-API.html)
* [TinkerPop API](http://www.orientechnologies.com/docs/last/Graph-Database-Tinkerpop.html)
