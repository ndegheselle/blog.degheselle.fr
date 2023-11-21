---
layout: post
tags: software
---

# How to write nice code

All the importants points to write good code
- That hopefull will trigger a "Oh, that's neat"
- That will last
- That peoples wants to work with

In other words it should be easy to :
1. Improve or fix the existing
2. Add new things
3. Take a part of the app to reuse it in another project

## It's no good if nobody can read it

Making sure that other peoples can understand the code and its flow (how and when the code is going from A to B) easily.
How you can achieve that  :
- Use standards, from you team or global standards, and stick with it
- Naming so that code make sense syntaxically in its context (you can read the code as a somewhat correct sentence)
- Add comments then needed, to explain why these choices, how that may be fixed, why you haven't done
- Separation of concern
- But try to not nest too much since it's hurt flow readability
In addition to the code readability, communication about the project is also very important :
- Documenting (how to run, update and use)
- What (are this project resources), where (are theses resources)
- Why (these design choices have been made)
- The How aspect should be covered by the code in itself (thats readability)

Exemples :

```Csharp
// Number of time I have seen things like that ...
Popup.ShowPopup(); -> Popup.Show();
// If object not check valid ? VS If object is invalid.
// If yourself can't make sense of it while reading it why somone else would
if(!Object.CheckValid()) -> if(Object.IsInvalid())

// If the code is from a stackoverflow question you can add the link in a comment
public void Show();
```

Readability aslo improve :
- Flexibility (adapt the code without redesiging everything)
- Maintenability (easy to update over time)
- Debugging (fix issues in the code)

Aside from that some other points to keep in mind :
- Don't try to make everything generic but try to anticipate what the futurs hold
- It's ok to rewrite / refractor existing code multiple times

## More about separation of concern

A well organised code allow for readability on the project level.

Then you start to think about separation of concern you end up with something like :
- UI / views : visuals parts of the app, use an exemple here to show why its different of the components
- Buisness / logic / controler : control part of the app, buisness is more clear to me (it contain the main code of what the app do)
- Data / model : all the objects, classes and enumerations used in the app.
- Repositories : depending on the orm and how you are loading data this can be replaced directly by the data folder

In addition, you may want to separate everything that is reusable in the project. And heck, why not making it a separate project at some point ?
- Components : every visual elements that are used in more than one view
- Base : every element of logic that is used in more than one place

That's MVC with extra steps. But why is it so important ? Separation improve readability and maintenability,
- If one bit of code is doing several things at once it will be harder to understand
- Seems easy to do but, separation can be pretty hard to achieve with complexe logic that interact a lot with the UI (and that's why binding exsist and is such a huge thing)

## Spread the data, Kouhei !

Tips on how to share data between differents parts of the app. Then you have differents components with parent passing data to childs, sometimes you want two distants childs (with different parents) to share data. What you want to avoid is having one global place with a bunch of different properties that are not linked together, which is bad for maintenability and readability.

Most of the time the solution is setting up a store / state management system. That mean creating singleton classes (so with an unique instance accross the project) which implement interfaces, with that components accessing the store will have only access to the data they need (reducing coupling). The stores can also be used along with events in order to keep the whole app up to date.

In general components should only have access to the data they need, nothing more (which help to create generic components), that way you can pass the data from the parent and only the parent have to know how to get this data.


# Let have a simple example

Imagine you have an existing application that imports a CSV file, read its content and then save it to a json file.

We have this interface used to get the parameters of file to import :
```html
<div>
    <input value={filePath} />
    <input value={csvSeparator}/>
    <checkbox value={hasHeaders} />
</div>
```

And the following code to treat it :
```ts
function businessLogic()
{
    // Check if file exist
    if (!file.exist(this.UI.filePath)) {
        this.window.alert("Error, the file doesn't exist !");
        return;
    }

    // Update UI
    this.UI.setLoading(0.1, "Importing the file ...");

    // Doing some logic
    loadFile(this.UI.filePath);
    getHeaders(this.UI.hasHeaders);
    var values = separate(this.UI.csvSeparator);
    
    // Update UI
    this.UI.setLoading(0.9, "Saving the file ...");

    // Finished ! Save the file
    if (file.exist(this.outputPath))
    {
        // Ask a user confirmation
        if (!this.window.confirm("The output file already exist, do you want to overwrite it ?"))
            return;
    }
    saveFile(this.outputPath);
}
```

All of this logic is very standard and nothing complicated, but what happen if you need to automatically import the file ? The two problems that you will have is that without the view context and data you can't run this business logic and that the business logic is very dependent on the view since you need user inputs during the process.

The first thing you can do is to separate the logic from the view context by passing parameters to the function (or objects in more complex cases) :
```ts
function businessLogic(filePath: string, options: CsvImportOptions)
{
    // Check if file exist
    if (!file.exist(filePath)) {
        this.window.alert("Error, the file doesn't exist !");
        return;
    }

    // Update UI
    this.UI.setLoading(0.1, "Importing the file ...");

    // Doing some logic
    loadFile(filePath);
    getHeaders(options.hasHeaders);
    var values = separate(options.csvSeparator);
    
    // Update UI
    this.UI.setLoading(0.9, "Saving the file ...");

    // Finished ! Save the file
    if (file.exist(this.outputPath))
    {
        // Ask a user confirmation
        if (!this.window.confirm("The output file already exist, do you want to overwrite it ?"))
            return;
    }
    saveFile(this.outputPath);
}
```
This already makes the function easier to read, but there are still some references to the actual UI which mean that you can't use it in other components. So let's fix this :
```ts
function businessLogic(filePath: string, options: CsvImportOptions)
{
    // Check if file exist
    if (!file.exist(filePath)) {
        this.window.alert("Error, the file doesn't exist !");
        return;
    }

    // Tell anyone who is listening to update UI
    onProgressEvent.emit(0.1, "Importing the file ...");

    // Doing some logic
    loadFile(filePath);
    getHeaders(options.hasHeaders);
    var values = separate(options.csvSeparator);
    
    // Tell anyone who is listening to update UI
    onProgressEvent.emit(0.9, "Saving the file ...");

    // Finished ! Save the file
    if (file.exist(this.outputPath))
    {
        // Ask a user confirmation
        if (!this.window.confirm("The output file already exist, do you want to overwrite it ?"))
            return;
    }
    saveFile(this.outputPath);
}
```
Here you go ! By using events, your function doesn't have to know the specific UI and can be used in any component. Still, this business logic needs to exist in a context where it can display alerts and confirm messages, while it may be fine in most case what will happen if you need to run it totally programmatically without any interface ?

The alert part can easily be fixed with an `exception` :

```ts
...
// Check if file exist
if (!file.exist(filePath)) {
    throw new Exception("Error, the file doesn't exist !");
}
...
```

> If you already wondered : When should I use an `exception` over simply returning a null value or a custom object with an error ? Stop wondering ! An exception should be used then something unexpected happen, a state where your application should not continue working, aside from that you should either use events or returns.

Handling, confirm messages is more tricky since it's highly coupled to how UI work. There are several ways to solve this : 
Separate the function in several smaller functions :

```ts
function loadFile(filePath: string, options: CsvImportOptions)
{
    // Check if file exist
    if (!file.exist(filePath)) {
        throw new Exception("Error, the file doesn't exist !");
    }

    // Tell anyone who is listening to update UI
    onProgressEvent.emit(0.1, "Importing the file ...");

    // Doing some logic
    loadFile(filePath);
    getHeaders(options.hasHeaders);
    var values = separate(options.csvSeparator);
    
    // Tell anyone who is listening to update UI
    onProgressEvent.emit(0.9, "Saving the file ...");

    // Finished ! Save the file
    if (file.exist(this.outputPath))
    {
        return false;
    }
    return true;
}

function saveFile(filePath: string) { ... }
```
And in the UI :
```ts
var result = business.loadFile(this.filePath, {hasHeaders: this.hasHeaders, csvSeparator: this.csvSeparator});
if (!result &&
    !this.window.confirm("The output file already exist, do you want to overwrite it ?"))
{
    return;
}
business.saveFile(this.filePath);
```

The other solution is to pass a callback :
```ts
function loadFile(filePath: string, options: CsvImportOptions, callbackConfirm: function)
{
    // Check if file exist
    if (!file.exist(filePath)) {
        throw new Exception("Error, the file doesn't exist !");
    }

    // Tell anyone who is listening to update UI
    onProgressEvent.emit(0.1, "Importing the file ...");

    // Doing some logic
    loadFile(filePath);
    getHeaders(options.hasHeaders);
    var values = separate(options.csvSeparator);
    
    // Tell anyone who is listening to update UI
    onProgressEvent.emit(0.9, "Saving the file ...");

// Finished ! Save the file
    if (file.exist(this.outputPath))
    {
        if (!callbackConfirm("The output file already exist, do you want to overwrite it ?"))
            return;
    }
    saveFile(filePath: string) { ... }
}

function saveFile(filePath: string) { ... }
```

If the callback becomes more complex and need more parameters, the best is to create a generic interface to handle that (for example `IDialog`).

What we finish with is looking a lot like a Controller part of an MVC and the MVC is there to solve most of those problems. Still, I think it's easier to understand why it's a good solution by trying to solve these problems yourself and hand up with something similar to the MVC.

## Links and sources

Optionals : 
- [How principled coders outperform the competition](https://www.youtube.com/watch?v=q1qKv5TBaOA)
- [SOLID](https://en.wikipedia.org/wiki/SOLID)