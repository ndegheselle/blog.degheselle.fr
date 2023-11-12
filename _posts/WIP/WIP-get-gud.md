# How to write nice code

Making sure that other peoples can understand the code and it's flow easily.
How you can achieve that  :
- Use standard, from you team or global standards
- Naming so that code make sense syntaxically in it's context (you can read the code as a somewhat correct sentence)
- Add comments then needed, to explain why and sometime how (rarely since naming should answer how, but sometime that's not that easy)
- Separation of concern
- But try to not nest too much since it's hurt flow readability
In addition to the code readability, communication about the project is also a very important :
- Documenting (how to update && how to use)
- What (are this project resources), where (are theses resources)
- Why (these design choices have been made)
- The How aspect should be covered by the code in itself

Aside from that some other points to keep in mind :
- Don't try to make everything generic but try to anticipate what the futurs hold
- It's ok to rewrite / refractor existing code multiple times

Exemples :
```C#
// Number of time I have seen things like that ...
Popup.ShowPopup(); -> Popup.Show();
// If object not check valid ? VS If object is invalid.
// If yourself can't make sense of it while reading it why somone else would
if(!Object.CheckValid()) -> if(Object.IsInvalid())

// If the code come from a stackoverflow question you can add the link in a comment
public void Show();
```

Readability make it easier for :
- Flexibility (adapt the code without redesiging everything)
- Maintenability (easy to update over time)
- Debugging (fix issues in the code)

# Organisation code

A well organised code allow for readability on a project level.

Then you start to think about separation of concern you end up with something like :
- UI / views : visuals parts of the app, use an exemple here to show why its different of the components
- Buisness / logic / controler : control part of the app, buisness is more clear to me (it contain the main code of what the app do)
- Data / model : all the objects, classes and enumerations used in the app.
- Repositories : depending on the orm and how you are loading data this can be replaced directly by the data folder

In addition, you may want to separate everything that is reusable in the project. And heck, why not making it a separate project at some point ?
- Components : every visual elements that are used in more than one view
- Base : every element of logic that is used in more than one place

That's MVC with extra steps. But why is it so important ? Separation improve readability and maintenability

# Spread the data, Kouhei !

Dilema : share data between parts of your app. Solutions :
- Interfaces
- Stores or State management
- Singletons
- Don't make one giant static object that old everything