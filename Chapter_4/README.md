# Patterns (Part 2)

## Computed Patttern

Some computations are complex and if we store the operands of the computation separately, we can end up with poor peformance.

* Math operations: sum, average, median,... usually calling a built-in function on the server.<br/>
E.g. Keeping track of ticket sales. Instead of summing up the screenings docs every time a movie is displayed, we save the sum and update it whenever a new screening document comes in.
* Fan-out operations: do many tasks to represent a logical task. Two schemes: fan-out reads and read from different locations when reading, of fan-out writes that involves multiple write operations but not fanning-out when reading.
* Roll-up operations: is a drill-down operation, data is meged together. Grouping time-series data from small intervals into larger ones is a good example of rilling-up.<br/>
E.g. reporting hourly, daily, monthly or yearly summaries.

All math operations are roll-ups, but we can think of roll-ups as a grouping operation.

Doing the roll-up at the right time makes more sense than always do it when reading the aggregation.

### Problem
* Costly computational manipulation of data
* Executed frequently on the same data with same result

### Solution
* Perform the operation and store the result in the appropiate document or collection
* If need to redo calculation keep source data

### Use Cases
IoT, event sourcing, time-series data, frequent aggregations

### Benefits/Trade-offs
* ✔ Faster read queries
* ✔ Saving resources (CPU/disk)
* ✘ Difficult to indentify the need
* ✘ Do not overuse as complexity increases

## Bucket Patterns

In the IoT world, having 10 million temperature sensors, each sending temp data every minute means 36 billion pieces of information oer hour. Storing each readout as a document, means too many docs to manage.

Keeping one document per device means the 16 MB limit per document will be reached sooner or later. Or the big documents are not manageable.

A suggestion is one doc per, device per day. Every new day a new document will be created. The doc contains an array per hour.<br/>
This easy-to-understand-for-human structure makes it difficult to average the temp for a given period => get the array or section for each hour of the period and do the calculation. Maybe a single array for a day makes more sense.<br/>
Besides, if data is not coming, the position needs to be inserted with missing (null) entry.
```json
{
    _id: "device_id",
    date: ISODate("2018-03-01"),
    temp: [
        [20, 20, 20.1, ...],
        [21.1, 20.9,...],
        ...
    ]
}
```


Another suggestion is have a document per device, per hour. There are 24 times more documents, but they are smaller. Beside, the temperatures are in an object with a minute key.<br/>
For missing data, the value for the minute key will simply not be there.
```json
{
    _id: "device_id",
    date: ISODate("2018-03-01T13"),
    temp: 
        {1: 20, 2: 20, 3: 20.1, ...}
    ]
}
{
    _id: "device_id",
    date: ISODate("2018-03-01T14"),
    temp: 
        {1: 20, 2: 20, 3: 20.1, ...}
    ]
}
```

There are multiple ways of a bucketing and each one is fit for a set of operations.

Another example when bucketing comes handy is storing message in a chat app with many channels.<br/>
We can create a documetn per channel per day, for instance. It makes easy to archive or shard to a cheaper storage based on date.

Another usage is mimicking column-oriented databases to bring only the data that is needed for each document, instead of the whole document. In the IoT world, imagine that a device pushes multiple fields. We can create a bucket with the values for each field and a different document contains only one field. It becomes very efficient to calculate on a single field.

### Gotchas

* Random insertion and deletion in buckets.
* Difficult sorting across buckets
* Ad-hoc queries are more complex across buckets
* Works best when complexity is hidden behind application.

### Problem
Avoiding too many documents or too big documents
1-* that can't be embedded.

### Solution
Define optimal amount of info and store it in an array in the main object.

It is a embedded 1-* with N docs, each having an average sub-docs

### Use Cases
IoT, data-warehousing, lots of information associated to a document

### Benefits/Trade-offs
* ✔ Good-balance between data access and size of data returned
* ✔ Makes data more manageable
* ✔ Easy to prune data
* ✘ Can lead to poor query results if not designed correctly
* ✘ Less friendly to BI tools as bucketing needs to be understood by queries

## Schema Versioning Pattern

Schema is going to change, but mongo allows those changes without downtime because the schema is flexible.

We can have documents that have a property and other documents with another array property. Each will be annotated with the version of the schema that it conforms to.<br/>
In a relational database there is only one schema per table.

The application will have to handle all different versions of the document. Additionally, the document can be brought forward to the latest version whenever is retrieved. When all docs are migrated to the latest version, it is up to us to remove the previous version handlers.

Optionally, docs can be migrated via background jobs.


### Problem
* Avoid downtime doing schema updates
* Upgrade docs can take way too long
* Not all documents need to be updated

### Solution
Each doc has a schema version.

The app can handle all version

Pick a strategy to migrate the docs.

### Use Cases
* Every app the has a production-deployed db
* Systems with a lot of legacy data

### Benefits/Trade-offs
* ✔ No downtime
* ✔ Full control of migrations process
* ✔ Less technical debt
* ✘ May need multiple indexes fot the same field during migration period

## Tree Patterns
Model hierarchical information.

There are several use cases for hirarchies between objects/nodes: organization charts, books in bookstore or product categorization.

There are a set of common operations that are useful for these hierarchical data:
* find ancestors of node X (faX)
* find reports to Y (frY)
* Find all nodes under Z (fuZ)
* move children from parent N to parent P (mNP)
* ...

Documents are hierarchical by nature.

### Structures

There are several structures that can be combined. For example, using ancestors array and parent reference:

```javascript
{
    _id: 8,
    name: "Umbrellas",
    parent: "Fashion",
    ancestors: ["Swag", "Fashion"]
}
```

to get the benefits of optimal operations from each model: frY, fuZ


#### Parent references
Doc has a ref to parent.
```javascript
{
    name: "<string>",
    parent: "name_of_parent"
}
```

* faX is an aggregation with `$graphlookup` of the inmediate parents => !
* frY `.find({parent: 'Y'})` => y 
* fuZ is straighforward, but iteration over several docs is needed => ! 
* mNP is single update where parent = N => y

#### Child references
Doc has an array property with inmediate children
```javascript
{
    name: "<string>",
    children: ["child_1", "child_2", ...]
}
```

* faX => !
* frY => !
* fuZ is really simple and a single query `.find(name: Z, {children: 1})` => y
* mNP => !

#### Array of ancestors
Doc has a ancestors array that represent the ancestors path.

```javascript
{
    name: "<string>",
    ancestors: ["parent", "grandparent", "great-grandparent"...]
}
```
* faX `.find({name: X}, {ancestors: 1})` => y
* frY => y
* fuZ => y
* mNP => !

#### Materialized paths

Doc as a ancestors string that represents ancestors path with a delimiter.

```javascript
{
    name: "<string>",
    ancestors: "parent.grandparent.great-grandparent"
}
```
* faX `.find({ancestors: /\.Y$/} )` => y
* frY => !
* fuZ => !
* mNP => !

### Problem
* Represent hierarchical structured data
* different access patterns to navigate the tree
* Optimize for common operations

### Solution
Use one or more:
* child reference
* parent reference
* array of ancestors
* materialized paths

### Use Cases
* Org charts
* Categorization

### Benefits/Trade-offs
* ✔ child reference: easy to navigate to children or tree descending access
* ✔ parent reference: immediate parent discovery and tree updates
* ✔ array of ancestor: navigate upward the ancestor path
* ✔ materialized path: use regex to find nodes in tree

* ✘ 
## Polymorphic Pattern
Organizing documents by either what is common or by what is different: same or different collection.

Different collections involves more difficulties to find query results that come from both collections.<br/>
=> Group things together because we want to query them together.

E.g. vehicles collection that have cars (owner, taxes, #wheels), trucks (owner, taxes, #wheels, #axels) and boats (owner, taxes). Some doc types have properties that others have not. Having a vehicle discriminator (vehicle_type) is the canonic implementation of a polymorphic collection, because determines the expected shape of the document.

Another case of polymorphism occurs in embedded sub-documents. For example a customer with several addresses in different countries. Addresses are different by country, containing different properties.<br/>
Based on the country we can infer the shape of the sub-document.

This pattern is commonly applied when we need a single (unified) view of the different types of documents. The relational solution to this problem is sub-optimal. When using document databases, the solution is much simpler.

The schema versioning pattern is a case of polymorphic pattern. With the version being the discriminator.

### Problem
Store in a single view (collection) objects that are more similar than different. 

### Solution
Use a discriminator field that tracks the type of document or sub-document.

The application is responsible to treat each type accordingly

### Use Cases
* single-view systems
* product catalogs
* content management systems

### Benefits/Trade-offs
* ✔ Easy to implement
* ✔ Allows queries across a single collection
* ✘ 
## Others
