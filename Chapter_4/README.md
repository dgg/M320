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
* ✔ 
* ✘ 

## Bucket Patterns

### Problem

### Solution

### Use Cases

### Benefits/Trade-offs
* ✔ Faster read queries
* ✔ Saving resources (CPU/disk)
* ✘ Difficult to indentify the need
* ✘ Do not overuse as complexity increases

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
