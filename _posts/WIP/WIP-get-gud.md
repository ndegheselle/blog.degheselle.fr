# How to write nice code

Making sure that other peoples can understand the code and it's flow easily. Allow for :
- Colaborate with other peoples
In the future make it easier to :
- Maintain
- Debug

Readability :
- Naming things so that it's standard
    - Make sense syntaxically in it's context (you can read the code as a somewhat correct sentence)
    - No excuses to make names as little as possible with autocomplete
- Add comments then needed

Exemples :

```C#
// Number of time I have seen things like that ...
Popup.ShowPopup(); -> Popup.Show();
// If object not check valid ? VS If object is invalid.
// If yourself can't make sense of it while reading it why somone else would
if(!Object.CheckValid()) -> if(Object.IsInvalid())
```

Flexibility :
- Don't try to make everything generic but try to anticipate what the futurs hold
Communication
- Documenting (how to update && how to use)
- What (are this project resources), where (are theses resources)
- Why (these design choices have been made)
- The How aspect should be covered by the code in itself

## Organisation code

MVC is good. Then you start to think about separation of concern you end up with something like :

- UI / views : visuals parts of the app, use an exemple here to show why its different of the components
- buisness / logic / controler : control part of the app, buisness is more clear to me (it contain the main code of what the app do)
- data / model : all the objects, classes and enumerations used in the app.
- repositories : depending on the orm and how you are loading data this can be replaced directly by the data folder

That's MVC with extra steps.

In addition, there's things that should be build as if it should be usable outside of the project :
- components : every visual elements that are used in more than one view
- base : every element of logic that is used in more than one place

And heck, why not making it a separate project at some point ?

## Spread the data, Kouhei !

Dilema : share data between parts of your app. Solutions :
- Interfaces
- Stores or State management
- Singletons
- Don't make one giant static object that old everything