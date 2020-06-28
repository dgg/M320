## Problem

Organic/recycled clothing store.

Inventory increased and there are way more makers and suppliers.

Shop is becoming slower as frequently used docs cannot fit into RAM, because product docs contain all review, questions and specs.

```json
{
  "_id": ObjectId("5c9be463f752ec6b191c3c7e"),
  "item_code": "AS45OPD",
  "name": "Recycled Kicks",
  "maker_brand": "Shoes From The Gutter",
  "price": 100.00,
  "description": "These amazing Kicks are made from recycled plastics and
  fabrics.They come in a variety of sizes and are completely unisex in design.
  If your feet don't like them within the first 30 days, we'll return your
  money no questions asked.
  ",
  "materials": [
    "recycled cotton",
    "recycled plastic",
    "recycled food waste",
  ],
  "country": "Russia",
  "image": "https:///www.shoesfromthegutter.com/kicks/AS45OPD.img",
  "available_sizes": {
      "mens": ["5", "6", "8", "8W", "10", "10W", "11", "11W", "12", "12W"],
      "womens": ["5", "6", "7", "8", "9", "10", "11", "12"]
  },
  "package_weight_kg": 2.00,
  "average_rating": 4.8,
  "reviews": [{
      "author": "i_love_kicks",
      "text": "best shoes ever! comfortable, awesome colors and design!",
      "rating": 5
    },
    {
      "author": "i_know_everything",
      "text": "These shoes are no good because I ordered the wrong size.",
      "rating": 1
    },
    "..."
  ],
  "questions": [{
      "author": "i_love_kicks",
      "text": "Do you guys make baby shoes?",
      "likes": 1223
    },
    {
      "author": "i_know_everything",
      "text": "Why do you make shoes out of garbage?",
      "likes": 0
    },
    "..."
  ],
  "stock_amount": 10000,
  "maker_address": {
    "building_number": 7,
    "street_name": "Turku",
    "city": "Saint-Petersburg",
    "country": "RU",
    "postal_code": 172091
  },
  "makers": ["Ilya Muromets", "Alyosha Popovich", "Ivan Grozniy", "Chelovek Molekula"],
}

```

## Task
Modify the schema so that the working set can it in RAM:

```json
{
  "_id": "<objectId>",
  "item_code": "<string>",
  "name": "<string>",
  "maker_brand": "<string>",
  "price": "<decimal>",
  "description": "<string>",
  "materials": ["<string>"],
  "country": "<string>",
  "image": "<string>",
  "available_sizes": {
    "mens": ["<string>"],
    "womens": ["<string>"]
  },
  "package_weight_kg": "<decimal>",
  "average_rating": "<decimal>",
  "reviews": [{
    "author": "<string>",
    "text": "<string>",
    "rating": "<int>"
  }],
  "questions": [{
    "author": "<string>",
    "text": "<string>",
    "likes": "<int>"
  }],
  "stock_amount": "<int>",
  "maker_address": {
    "building_number": "<string>",
    "street_name": "<string>",
    "city": "<string>",
    "country": "<string>",
    "postal_code": "<string>"
  },
  "makers": ["<string>"]
}
```
* Remove the maker address of the list of makers (move to a separate collection)
* Replate `reviews` and `questions` with `top_five_review` and `top_five_questions`
