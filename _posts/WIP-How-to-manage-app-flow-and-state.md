---
layout: post
tags: software
---

How to handle an app state and workflow
- Tree
- Linear
- Going Back

Handling global state (parameters and others) as well as local parameters.

Concept of store / context :
- Share an object / state between several steps / pages of the app
- The store shoumd be unique between all objects
- concept of workflow

Main problem :
To share informations between differents pages most of the time you end up creating one object containing all the data that should be shared between all the pages.
- Pros : allow you to easily export and import it if needed
- Cons : with complexe workflows it can be quite heavy

Solution : ?