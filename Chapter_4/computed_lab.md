# Computed Lab

## Problem

Lots of residents are installing solar panels and power plant needs to be flexible in electricity distribution.

Some residents produce more energy and sell the excess.

Our database tracks energy production (kWh), energy consumption and how much enery needs to be supplemented daily.

The algorithsm analyze the consumption by city zones one day at a time.

```json
{
  "_id": ObjectId("5c9414f25e6aff2b8870a2d0"),
  "address": {
    "building number": "742",
    "street name": "Evergreen Terrace",
    "city": "Springfield",
    "state": "MA",
    "zip code": "01111"
  },
  "owner": "Homer J. Simpson",
  "zone": 13,
  "date": ISODate("2019-03-21T23:03:31.197Z"),
  "kW per day": {
    "consumption": 10,
    "self-produced": 7,
    "city-supplemented": 3
  }
}
```
The problem with this design is that the zone totals need to be calculated every day for the report.

Every zone has 100 to 900 buildings and a zone collection will be creted when consumption data comes from each unit in each zone, daily.

## Task

Use _Computed Pattern_ to create separate collection to store zone-based summaries on zone consumption, prodcution and city-supplemented energy which will be calculated and over-written when new data comes.

Mosify this schema to represent the schema that will be store in the computed collection. Remove fields that are not needed:

```json
{
  "_id": "<objectId>",
  "address": {
    "building number": "<string>",
    "street name": "<string>",
    "city": "<string>",
    "state": "<string>",
    "zip code": "<string>"
  },
  "owner": "<string>",
  "zone": "<int>",
  "date": "<date>",
  "kW per day": {
    "consumption": "<int>",
    "self-produced": "<int>",
    "city-supplemented": "<int>"
  }
}
```
