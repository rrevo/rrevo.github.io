---
layout: post
title:  DynamoDB insert once 
tags:   dynamodb
---

## DynamoDB

[Amazon DynamoDB](https://aws.amazon.com/dynamodb) is a managed NoSQL database service with neat latency guarantees at large scale. Practical usage DynamoDB requires care in [modelling](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/BestPractices.html) and [querying](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/QueryAndScanGuidelines.html) 

## API

[Documentation](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html) for DynamoDB is extensive but also confusing. There are multiple ways to program for Dynamo.

<a href="https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Programming.SDKs.Interfaces.html">
<img src="https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/SDKSupport.SDKInterfaces.png" width="100%" height="100%" />
</a>

The [Object Persistence Interface](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Programming.SDKs.Interfaces.Mapper.html) feels most natural to use as a Java programmer. The [DynamoDBMapper](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBMapper.html) class implements the DynamoDB object persistence interface providing methods like save() and load(). The DynamoDBMapper acts as a wrapper around the low-level client (AmazonDynamoDB).

[Low level interfaces](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Programming.SDKs.Interfaces.LowLevel.html) map closer to the DynamoDB REST API payload. This API requires more work from the programmer.

## Preventing Overwrites of an Existing Item 

Items are identified by a primary key. If multiple `PutItem` operations are done for the same key, then data will be overwritten. To prevent this, [documentation](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.ConditionExpressions.html#Expressions.ConditionExpressions.PreventingOverwrites) states that a condition expression needs to be used. This will allow the write to proceed only if the item in question does not already have the same key-

{% highlight shell %}
aws dynamodb put-item \
    --table-name ProductCatalog \
    --item file://item.json \
    --condition-expression "attribute_not_exists(Id)"
{% endhighlight %}

However this API is not present in the DynamoDBMapper interface!

The DynamoDBMapper uses [legacy Condition Operators instead](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/LegacyConditionalParameters.Conditions.html). The equivalent operation can be done by using `Exists` like-

{% highlight shell %}
aws dynamodb put-item \
    --table-name ProductCatalog \
    --item '{
      "Id": {"N":"500"},
        "Title": {"S":"Book 500 Title"}
    }' \
    --expected '{
        "Id": { "Exists": false } 
    }'
{% endhighlight %}

And finally to use this with DynamoDBMapper, sample code would look like-

{% highlight java %}
// Set expected false for an attribute
ExpectedAttributeValue expectedAttributeValue = new ExpectedAttributeValue();
expectedAttributeValue.setExists(Boolean.FALSE);

// Map the id field to the ExpectedAttributeValue
Map<String, ExpectedAttributeValue> expectedAttributes = new HashMap<>();
expectedAttributes.put("id", expectedAttributeValue);

// Use the attributes within a DynamoDBSaveExpression
DynamoDBSaveExpression saveExpression = new DynamoDBSaveExpression();
saveExpression.setExpected(expectedAttributes);

// Save to dynamoDBMapper using the saveExpression
dynamoDBMapper.save(item, saveExpression);
{% endhighlight %}

The code above will throw a `ConditionalCheckFailedException` if there is an existing item already.

