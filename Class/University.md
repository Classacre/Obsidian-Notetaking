---
limit: 20
mapWithTag: false
icon: document
tagNames: 
filesPaths: 
bookmarksGroups: 
excludes: 
extends: 
savedViews: []
favoriteView: 
fieldsOrder:
  - yH13Mm
  - ucoLXw
  - DFwf5K
  - c3eNAG
  - WUyBLS
  - bVjLeD
version: "2.43"
fields:
  - name: Status
    type: Select
    options:
      sourceType: ValuesList
      valuesList:
        "1": To do
        "2": Working
        "3": Done
      valuesFromDVQuery: |
        `= (this.Lecture && this.Flashcard) ? "Done" : ((this.Lecture || this.Flashcard) ? "Working" : "To do")`
    path: ""
    id: yH13Mm
    command:
      id: insert__yH13Mm
      icon: list-plus
      label: Insert Status field
  - name: Priority
    type: Select
    options:
      sourceType: ValuesList
      valuesList:
        "1": Low
        "2": Medium
        "3": High
    path: ""
    id: ucoLXw
  - name: Week
    type: Number
    options:
      min: .nan
      max: .nan
    path: ""
    id: DFwf5K
  - name: tags
    type: Multi
    options:
      sourceType: ValuesList
      valuesList: {}
    path: ""
    id: bVjLeD
  - name: Lecture
    type: Select
    options:
      sourceType: ValuesList
      valuesList:
        "1": 🟩
        "2": 🟥
    path: ""
    id: c3eNAG
  - name: Flashcards
    type: Select
    options:
      sourceType: ValuesList
      valuesList:
        "1": 🟩
        "2": 🟥
    path: ""
    id: WUyBLS
---
