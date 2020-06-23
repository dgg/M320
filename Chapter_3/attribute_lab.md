# Attribute Lab

## Problem
A museum exchanges pieces of art with our museum and want to track whether they are on display and where they are.

For loans, there is an `events` array that tracks the museum and the date of loan.

```json
{
  "_id": ObjectId("5c5348f5be09bedd4f196f18"),
  "title": "Cookies in the sky",
  "artist": "Michelle Vinci",
  "date_acquisition": ISODate("2017-12-25T00:00:00.000Z"),
  "location": "Blue Room, 20A",
  "on_display": false,
  "in_house": false,
  "events": [{
    "moma": ISODate("2019-01-31T00:00:00.000Z"),
    "louvres": ISODate("2020-01-01T00:00:00.000Z")
  }]
}
```

For each new museum that we a piece is loan, a new index needs to be added. e.g. dealing with El Prado means creating a `{"events.prado": 1} index to query effectively which pieces are on loan there.

## Task

Change the schema:
* single index on all events
* include `date_aquisition` with the rest of dates

```json
{
  "_id": "<objectId>",
  "title": "<string>",
  "artist": "<string>",
  "date_acquisition": "<date>",
  "location": "<string>",
  "on_display": "<bool>",
  "in_house": "<bool>",
  "events": [{
    "moma": "<date>",
    "louvres": "<date>"
  }]
}
````
