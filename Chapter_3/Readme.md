# Patterns (Part 1)

Patterns enable designing solution that both escalable and performant.

Patters are not complete solutions. They are reusable units of knowledge taht can be used to provide a complete solution.

## Handling Duplication, Staleness and Integrity

When optimizing an schema, we try to increase performance or optimize for certain use case. Sometimes this optimization comes with a trade-off in the shape of: data duplication across docs, staleness of some piece of data or writing extra app logic.

If trace-offs are more concerning than the reason to change the schema, we should not use such pattern.

## Handling Duplication

Why? Usually because of embedding in a given doc for faster access.

The concern comes from the fact that duplicated information challenges correctness and consistency.

Duplication **is allowed to exist**. But duplication affects data in different ways.

Sometimes, duplication is a feature: e.g. shipping addresses in orders should not be references, as changing the shipping address, would change fulfilled orders.

Sometimes, duplication does not matter as related data does not change.: e.g. movies-actors could be modeled as *-*. We could model it with two collections and have references from movies to actors or from actors to movies. If we embedded the actors into the movies we would be duplicating, but since the cast of a movie does not change once released we can accept such duplication if it suits our case.

Sometimes duplication affects data that changes over time: e.g. movies and revenues. We could have a pre-computed `gross_revenues` property in the movie document and we have a collection of screenings. If the existence of the field and the need to keep it in sync surpases the benefit it may bring, we should not have such duplication.<br/>
But if we go for it, the application might need to update multiple documents or have a background job do that. But how often? To answer that, we need consider also staleness.

## Handling Staleness

Staless is displaying a piece of data that might be out-of-date.

Globalization and scalability of our systems makes up-to-second display of information more of a challenge.

The question is not how trustful is our data, but how tolerating to stale data is the information. It is more important to have more up-to-date informatino about the availability of a product that it is to know how many people viewed it.

Analytics queries are tolerated to be stale as they usually take place in secondary nodes.

A commong way to refresh stale data is to use a change stream to derive which dependent data needs to be udpated.

## Referential Integrity

Integrity shared some traits with staleness, for example, we could allow missing some pieces of related data for some time, given they are corrected in the future.

Broken integrity is usually the result of deleting a pieces of data without deleting the references to it.<br/>
It can also be a fact of live of duplicated information of a distributed system.

MongoDB does not support foreign keys or cascading operations to maintain integrity. If that is needed, it needs to be implemented by the application.

We can rely on change streams for deferred referential integrity. Or we can avoid embedding. Or we can use transactions

## Attribute Pattern

Polymorphism allows storing documents in the same collection with different schemas.<br/>
The documents will have some common fields with the same meaning, there might be some others that appear in a subset of the documents and mean different things for different types and have different formats. There might be properties unique or unknonwn.

If we have a lot of fields that we want to query, we might need indexes. Many indexes.

We might want to use the attribute pattern.

1. indentify the fields to transpose
1. for each field and its value a named tuple is created: `{ k: "name_of_the_field", v: "value_of_the_field" }` and place them all inside a new array property (`props`, for instance). There could be a third field in the tuple with the unit, for example.
1. Create an index on `{props.k: 1, props.v: 1 }`

Another example: a movie has different fields for different dates of release in different countries:
```js
{
    title: "Dunkirk",
    release_USA: "2017/07/23",
    release_Mexico: "2017/08/01",
    release_France: "2017/08/01",
    ...
}
```

What if we wanted to find all the movies released between two dates across all countries?

Moving to 
```js
{
    title: "Dunkirk",
    releases: [
        {k: "USA", v: "2017/07/23" },
        {k: "Mexico", v: "2017/08/01"},
        {k: "France":, v: "2017/08/01" }
    ]
    ...
}
```
makes the query so much simple.

### Problem
* Lots of similar fields with similar types and need to search accross those fields at once.

* Another case is when only a subset of documents have many similar fields.

### Solution
Transform fields into a new array property of key, value pairs with the key being the name of the field and the value, its value. The create an index containing both key and value.

### Use Cases
* Searcheable characteristics of a product
* Set of fields all having the same value type (list of dates)

## Benefits/Tradeoffs

* ✔ Easier to index
* ✔ Allow variety of field names
* ✔ Allows qualifying the relationship between key and value with a third tuple field.

## Extended Reference Pattern

If you find yourself "joining" data between collections, event if the query is not so horrid, with a high volume of data, performance is a liability.

Before `$lookup`, all joining had to be done in the application with multiple queries involved.

`$graphLookup` allows recursive queries on the same collection, like on graph databases.

Another way would be embedding in the one-side of the 1-* relationship. But... what if the joins come from the other side?<br/>
Imagine a 1-* between a customer and its orders. But we usually query orders, not customers.

Embedding the most used information (duplication) in the many-side while maintaining the reference field in the many, allows us to not having to join most of the time, but joining if we must at the expense of duplication.

Duplication management:
* minimize it: duplicate fields that change rarely and only the fields needed to avoif the join
* After change in "master" information: identify what needs to be chanaged and do it straight away if we must or wait for a batch update to do it at a later stage

In the example, duplicating is the right thing to do.

### Problem
* too many repetitive joins

### Solution
* identify the field on the lookup side
* bring in those fields into the main object

### Use Cases
Catalog, mobile applications, real-time analytics.

That is: optimize read operations by avoiding round-trips or touching too many pieces of data.

### Benefits/Trade-offs
* ✔ Faster reads
* ✔ Reduced number of joins and lookups
* ✘ Duplication if the extended reference contains data that changes a lot

## Subset Pattern
MongoDB tries to put in memory the working set (docs and portion of indexes that are accessed).

If the working set does not fit in memory, MongoDB will start trying to occupy RAM and there will be a constant thrashing of data trying to make it to RAM just to be evicted shortly after for other data to get there.

Solutions:
* add more RAM (or more nodes to the cluster)
* scale with sharding (or are more shards)
* reduce the size of the working set

Will focus on third. Get rid of part of huge documents that is not used so often.

For example, for a movie, people might want to access only the main actors, top reviews or quotes. The rest can go into a separate collection.

### Problem
Working set does not fit in memory

Or only some of the data of the working set is frequently used.

Lots of pages evicted from memory

### Solution
Divide the document in two: fields that are often required and fields that are rarely required.

The resulting relationship will be a 1-1 or 1-*.<br/>
Part of the information is duplicated in the most used side.

### Use Cases
* List of review of a product.
* List of comments on an article
* List of actors in a movie

### Benefits/Trade-offs
* ✔ Smaller working set as most used docs are smaller
* ✔ Short disk access from most used collection
* ✘ More round trips to the server when accessing less frequent information
* ✘ Mor disk usage as information is duplicated
