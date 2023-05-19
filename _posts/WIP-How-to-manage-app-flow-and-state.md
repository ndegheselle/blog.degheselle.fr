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

Solution : 
- Pass a tree of data with a new node at each steps ? -> seems quite complexe and not worth it
- Use one object with differents interfaces / composition -> probably the easiest solution

# Using a single context

- Is shared between all the relevent steps (severals context can exists independently)
- The step only have access to the relevant parts of the context (this is where interfaces are usefull)
- In more complex case the context may have a tree of the different steps, the step can then navigate this tree if it need access to previous or next information