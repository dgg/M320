# Polymorphic Lab

## Problem
Recruiting agency that got a new client (Techsy). The new customer has several community outreach and diversity initiative programs that need to be included.

Before the new client a recruiting document would look like:
```json
{
  "_id": ObjectId("5caa9e799c0aa5e39686f803"),
  "first_name": "Arya",
  "last_name": "Stark",
  "engineer_level": 3,
  "education": [{
    "level": "BS",
    "subject": "Computer Science"
  }],
  "years_experience": 3,
  "previous_employer": "Hooli",
  "technical": ["C++", "Python", "Java", "Golang", "MongoDB", "MySQL", "Bash/Shell"],
  "non-technical": {
    "languages": ["English"],
    "other": ["fencing", "dance", "horseback riding", "management"]
  },
  "candidate_notes": "A fierce warrior and survivor. Will do amazingly on any team."
}
```

These two docs come from the new customer:
```json
{
  "_id": ObjectId("5caa9e819c0aa5e39686f804"),
  "first_name": "Ginevra",
  "last_name": "Weasley",
  "program_affiliation": "Veteran Outreach Apprenticeship",
  "team_placement": "Server",
  "start_date": ISODate("2017-01-31T00:00:00.000Z"),
  "end_date": ISODate("2018-01-31T00:00:00.000Z"),
  "education": "App Bootcamp",
  "extend_offer": "yes",
  "technical_skills": ["JS", "ReactJS", "NodeJS", "bash/shell", "HTML5", "CSS", "Ruby on Rails", "Python"],
  "non-technical_skills": {
    "languages": ["English"],
    "other": ["Magic"]
  },
  "notes": "Ginny has been an amazing part of the team during the apprenticeship and we would love to extend her an offer to join us more permanently."
}
```
```json
{
  "_id": ObjectId("5caa9e899c0aa5e39686f805"),
  "name": {
    "first": "Rincewind",
    "last": "TheWizzard"
  },
  "program_affiliation": "Techsy Summer Internship ",
  "team_placement": "Data Optimization",
  "start_date": ISODate("2019-06-01T00:00:00.000Z"),
  "end_date": ISODate("2019-08-31T00:00:00.000Z"),
  "education": [{
      "level": "BA Major",
      "subject": "Philosophy"
    },
    {
      "level": "BS Minor",
      "subject": "Artificial Intelligence"
    }
  ],
  "extend_offer": "yes",
  "skills": {
    "technical": ["R", "Django", "JavaScript", "Matlab", "HTML5", "CSS", "Mathematica", "LaTeX", "Java", "JIRA"],
    "languages": ["English", "Chimeran", "Vanglemesht", "Sumtri", "Black Oroogu", "High Borogravian", "beTrobi"],
    "other": []
  },
  "notes": "Rinecewind has great ideas, but he is a bit of a mess. We still like him a lot and would love to keep him on board."
}
```

We need to be able to seach all candidates (whether they come from the new customer or not)

## Task

* Across all documents, we will use two fields for name: `first_name` and `last_name`.
* Across all documents, we will use a field called `recruiting_source` as the indicative field for the document shape.
* In documents that already have an existing `program_affiliation` field, we will use the value of this field as the `recruiting_source`.
* In documents that don't have `program_affiliation`, we will add a new field called `recruiting_source`.
* All documents should indicate whether an offer was extended via the `extend_offer` field.

Modify this schema to incorporate the previous changes:

```json
[{
  "_id": "<objectId>",
  "first_name": "<string>",
  "last_name": "<string>",
  "engineer_level": "<int>",
  "education": [{
    "level": "<string>",
    "subject": "<string>"
  }],
  "years_experience": "<int>",
  "previous_employer": "<string>",
  "technical": ["<string>"],
  "non-technical": {
    "languages": ["<string>"],
    "other": ["<string>"]
  },
  "candidate_notes": "<string>"
}, {
  "_id": "<objectId>",
  "first_name": "<string>",
  "last_name": "<string>",
  "program_affiliation": "<string>",
  "team_placement": "<string>",
  "start_date": "<date>",
  "end_date": "<date>",
  "education": "<string>",
  "extend_offer": "<string>",
  "technical_skills": ["<string>"],
  "non-technical_skills": {
    "languages": ["<string>"],
    "other": ["<string>"]
  },
  "notes": "<string>"
}, {
  "_id": "<objectId>",
  "name": {
    "first": "<string>",
    "last": "<string>"
  },
  "program_affiliation": "<string>",
  "team_placement": "<string>",
  "start_date": "<date>",
  "end_date": "<date>",
  "education": [{
      "level": "<string>",
      "subject": "<string>"
    },
    {
      "level": "<string>",
      "subject": "<string>"
    }
  ],
  "extend_offer": "<string>",
  "skills": {
    "technical": ["<string>"],
    "languages": ["<string>"],
    "other": ["<string>"]
  },
  "notes": "<string>"
}]
```
