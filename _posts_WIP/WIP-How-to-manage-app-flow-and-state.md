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

# What this problem is about

How should an app be organized
What are the mains problematics
- Handling updates
- Handling remove

Why is it relevant with the previous problems ? Because the solutions are really similars, creating defined bricks with limited functionnalities and how do you assemble these bricks.

Should extend this to "Basic problematics and solution to setup an application"

# Basic problematics and solution to setup an application

Show a basic exemple of what can go wrong :
- Everything in a file that end up with thousand of lines (going up and down in the file while trying to understand the flow of the app) : separate in different classes and files to force you to find relevant names and ease of navigation in the code base
- Check what is really usefull, most of the time old code contain bits that was making sense in a specific context

- Is it worth it creating a complexe setup, even for a simple application ?

How to avoid creating something that is hardly extensible and maintenable :
- If you have time thing about it twice
- Follow MVC and separation of concern
- Use clear names

- views : visuals parts of the app, use an exemple here to show why its different of the components
- buisness / logic / controler : control part of the app, buisness is more clear to me (it contain the main code of what the app do)
- data / model : all the objects, classes and enumerations used in the app.
- repositories : depending on the orm and how you are loading data this can be replaced directly by the data folder

Things that should be build as if it should be usable outside of the project :
- components : every visual elements that are used in more than one view
- base : every element of logic that is used in more than one place