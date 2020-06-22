# Relationships

Despite embracing the document model, pieces pof information stored still have dependencies and relationships between them.

Embedding large arrays, having too many or too few collections are mistakes that can be made if relationships are not studided.<br/>
And performance may drop because of it.

## Relationship Types and Cardinality

Most relationships can be classified as:
* one-to-one
* one-to-many
* many-to-many

1-1 relationships are usually modeled as sibling properties on a document (grouped data in the entity).

1-* (e.g. customer has many invoices, but the invoice is only related to a single customer)

*-* (e.g. an invoice contains multiple products and a single product can be part of many invoices)

These three are not enough, because the number of related entities matter and we can model the relationship differently depending of that number.

1-∞ are represented with a tuple **[min, most_likely, max]**

## One-to-Many

This is the most interesting one, because 1-1 can be modeled in a tabular way and *-* can be modeled as two 1-* (with an intermediate relationship entity).

Examples:
* a person with many credit cards
* a blog entry with many comments
*...

Possible models for 1-* in mongo:

### Embed 
* on the one-side and have a array property for the many related entities
* on the many-side and have a duplicated embedded document

Rule of thumb: embed in the entity most queried

### Reference

* on the one-side and have an array of references to the ids of the related entities
* on the many-side and have a property with the id of the "one" entities

Mongo does not support cascade so delations/updates need to be performed by the application.

## Many-to-Many Relationships

It can be msitaken by a 1-* relationship if only looked from one side of the relationship, so make sure to look at both ends to correctly identify it.

In the relational world, you cannot have two tables related as *-*. An intermediate relationship table ("jump table")needs to be created.

Example. Phone numbers and persons that use them. Some phone numbers are exclusive for one person, but family phones are used by multiple people.<br/>
We can duplicate the phone number for each member of the household.<br/>
Duplication is not always the evil. It is always a trade-off.

Possible models for *-* in mongo:

## Embed
* array of subdocuments in the many-side
* array of subdocuments in the otehr many-side

## Reference
* Array of references in one many-side
* Array of reference in the other many-side

Rule of thumb: embed documents of the least queried collection into the documents of the most queried collection.

## Example
### Carts and items. (or orders and addresses)

The cart document can have an array of embeded items (quantified with an extra property).<br/>
We usually query carts, instead of items. And there is duplication of items for different carts. While we keep a separate collection of items (with no relation to carts whatsoever) to represent items that are not added to any cart.

### Items and stores

Another model is keep a list of store references for each item.

Another option is having a list of item ids in the store document.

Which one to use depends on the queries adn the frequency of when items are started to be sold at a given store. Update the item when a new store sells it, or change the stores whenever when a new product is released. It depends.

## One-to-One
Usually 1-1 are properties of the same document.

Related properties can also be embedded in a sub-document or referenced in another collection.

## One to Zillions

1-∞ is better defined by specifying the cardinalty of the relationship [min, likely, max].

Maximum nuber is what we care the most of the relationship.

Talking about thos big numbers, they need to be watched out as we would not want to retrieve such a high number of related entities in our app.
