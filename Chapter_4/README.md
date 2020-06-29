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

### Problem

### Solution

### Use Cases

### Benefits/Trade-offs
* ✔ 
* ✘ 
## Tree Patterns

### Problem

### Solution

### Use Cases

### Benefits/Trade-offs
* ✔ 
* ✘ 
## Polymorphic Pattern

### Problem

### Solution

### Use Cases

### Benefits/Trade-offs
* ✔ 
* ✘ 
## Others
