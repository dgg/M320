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
