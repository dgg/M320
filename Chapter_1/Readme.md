# Introduction to Data Modeling

## The Document Model

MongoDb stores data hierarchically: a server can contain multiple databases, each one containing multiple collections with each collection containing multiple documents.

The collection of fields and embedded documents represent the schema of the document.

MongoDb offers flexible schema in the sense that the documents in a collection can have different shapes or 
type of property.

## Data Modeling Methodology

![methodology](methodology.png)

Three phases that can be ierative.

### Describe Workload

Gather what is needed to know about your data (size, operations, frequency, importance, performance requirements...)

### Identify Relationships

Identify the different entities and their quantities for your problem domain and how to relate to each other 

### Apply Design Patterns

Transforms the current model to address performance, scalability,... constraints.

## Model for Simplicity vs. Performance

For a given problem we can evaluate the trade-offs and settle for simplicity (development, staff constraints, size, sensible resource usage) or performance (larger team, lots of operations, resource usage is high,...).

When designing for performance, more ofthen than not, complexity of the solution increases, and more iterations of the methodology need to be used.

A middle ground can be achieved by exploring less design possibilities while iterating and pushing the envelope.

![flexible methodology](flexible_methodology.png)
