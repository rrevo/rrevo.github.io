---
layout: post
title:  DynamoDB Single Table Field Pattern
tags:   dynamodb
---

## DynamoDB Data Modelling

[DynamoDB](https://aws.amazon.com/dynamodb) is a managed NoSQL database service. Unlike a traditional RDBMS, there are no concepts like [Foreign Keys](https://en.wikipedia.org/wiki/Foreign_key) to help with modelling relationships. Data also needs to be [partitioned](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.Partitions.html) based on access patterns so that access can be fast via [queries](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html) rather than complete table [scans](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Scan.html).

In this post I'll cover modelling 1:N relationships using a new pattern called **Single Table Field Pattern**. To set the context I'll take an example and discuss a single and multiple table approach first.

Consider designing the schema for a Directory of companies with the following information per company -

{% highlight plain %}
class Company {
    String id;
    String name;
    String stock;
    List<Person> people;
    List<Office> offices;
}
{% endhighlight %}

A **Company** has some top level fields like id, name and stock. It also has two parent-child relationships to **Person** and **Office** models which can have fields of their own. All of these entities have unique ids.

Sample data is-

{% highlight plain %}
id1 -> name1, stock1, people [pid1 -> {}, pid2 -> {}], offices [off1 -> {}]
id2 -> name2, stock2, people [pid3 -> {}], offices [off3 -> {}]
{% endhighlight %}

## Single Table Single Item Pattern

A single table can be used for all the companies. The id of the company becomes the Partition Key and the complete map of the instance can be store in the attributes since lists and maps are supported.

| Partition Key (id) | Attributes  |
| ------------- |-------------|
| id1           | id1, name1, stock1, people[pid1 {pid1 attrs}, pid2 {pid2 attrs}], offices[off1 {off1 attrs}] |
| id2           | id2, name2, stock2, people[pid3], offices[off2]       |

Pros-
* Simpler code because of single table

Cons-

Best practice recommendations are to [Use One-to-Many Tables Instead Of Large Set Attributes
](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GuidelinesForItems.html#GuidelinesForItems.OneToMany). Problems include

* Limit on size of attributes
* Retrieval of subset of data
* Cannot provide alternative access patterns for Person or Office since fields are not common.

## Multiple Table Pattern

Each entity can be modelled as a top level table. Attributes of the individual entities are stored separately.


Company Table-

| Partition Key (id) | Attributes        |
| --------------|-------------------|
| id1           | id1, name1, stock1, people [pid1, pid2], offices [off1] |
| id2           | id2, name2, stock2, people [pid3], offices [off3] |

People Table-

| Partition Key (id) | Attributes    |
| --------------|---------------|
| pid1          |  pid1 attrs   |
| pid1          |  pid2 attrs   |
| pid3          |  pid3 attrs   |

Office Table-

| Partition Key (id) | Attributes    |
| --------------|---------------|
| off1          | off1 attrs    |
| off2          | off2 attrs    |

Pros-

* Better query access patterns
* Parent to Child and vice-versa traversal can be modelled.
* Can reduce cost by reading or writing specific entities.

Cons-

* Implementation code is more complex.
* [Streams replication](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html) order is guaranteed within a table. Code needs to handle cross table sync issues.


## Single Table Field Pattern

The Single Table Field Pattern is a hybrid technique to model the same information. It solves the major cons of a pure single table approach while not being as complex (and flexible) as the multi table approach.

The core idea is to split a single entity into its scalar and top level list fields. One required item is stored with the basic scalars called root. Optional additional items are store based on the cardinality of the relationships. The sort key is used with pre-defined prefixes for each field.
A field GSI is added for direct access to information in the sub-entities like getting a person by id.

Sort key prefixes are-
* *root_* prefix for the top level scalar values of the Company like name and stock. This item is always required.
* *people_* prefix for the Person entity. One item per instance in people list.
* *office_* prefix for the Office entity. One item per instance in offices list.

| Partition Key (id) | Sort Key (field)    | Attributes |
| --------------|--------------|--------------------|
| id1           | root_id1     | id1, name1, stock1 |
| id1           | people_pid1  | id1, pid1 attrs    |
| id1           | people_pid2  | id1, pid2 attrs    |
| id1           | office_off1  | id1, off1 attrs    |
| id2           | root_id2     | id2, name2, stock2 |
| id2           | people_pid3  | id2, pid3 attrs    |
| id2           | office_off3  | id2, off2 attrs    |

Operations-
* Retrieve the complete Company - Run query using Partition Key and create object by iterating through all items
* Retrieve a nested entity like Person by pid1 - Run a query on the field GSI and fetch that information directly.
* Update single field - Update specific item.

Pros-

* Better query access patterns
* Parent to Child and vice-versa traversal can be modelled.
* Can reduce cost by reading or writing specific entities.
* New fields can be added in the future.

Cons-

* Implementation code is more complex.
* Cannot update multiple items in one operation.
* Limited GSIs.
* Limited for multi level nested objects.

## Other References

* [Data Types](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.NamingRulesDataTypes.html#HowItWorks.DataTypes)
* [GSI](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html)
