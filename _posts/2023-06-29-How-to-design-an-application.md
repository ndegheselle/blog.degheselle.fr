---
layout: post
tags: software
---

# How to design an application

Or anything that has an interface. I am writing this after noticing on multiple occasions that creating an application is easy, creating one that work is harder and one that simply work is a challenge. The goal there is to explain the mindset you have to get to create an application that can easily be understood, something that will be be worthy of a "Huh, neat".

Someone, which can also be the creator itself after a pause, checking an existing application is there either to :
1. Improve or fix the existing
2. Add new things
3. Take a part of this app to reuse it in another project

All of these require a certain understanding of the existing code and workflow. This can be eased with clear separation of concern, naming convention and so on, a lot of documentation on how to write good code is around there (I particurlarly like [CodeAesthetic](https://www.youtube.com/@CodeAesthetic) that have really good points).

How can we make it as easy as possible ?

## Make the code generic but not too much, most importantly don't overthink it

Generic is good and you should do it then you can. Then you add a functionality always think "Does it make sense outside of this project" and if yes, just put it either in a completely different project or at least a specific folder / namespace (for example `base`, which convey the idea that it's a 'base' brick that can be used to build a lot of things).

But keep in mind that specialized code is simpler to write and understand. Don't get me wrong, well done generic code can be really easy to understand but it's way easier to create accessible specialized code. It's all come down to how much time you have and how you should use it, do you prefer perfecting a simpler specific piece of code or producing a more complex generic one that *may* be used.

## Being consistent is easier that being perfect and much more beneficial.

From what I have seen the more complex it goes, the more opinion related it will become. In other word the more complex you go the more choices you have to take since there is rarely one way to do something, and even more rarely one good way. Those choices will be related to your experience and finds, and sometime it will not be easilly understable by someone else whatever you do, to make the life of everyone easier you should be as consistent as possible.

For example, working with asynchronous programming you have the choice to either go with events or promises (or something similar depending on your programming language). None of this solution is better than the other, you just have to choose and stick with one.

> Always write comments about what you were thinking then codding, where you wanted to go, how you think this can be fixed, why you made the choice to not make this generic or otherwise, and so on. Explaining why something hacky has been put in place can save a lot of time for someone reading your code while thinking "Well that can easily be fixed, why didn't he ?". To separate these kind of comments from simple logic description you can use tags like NOTE, OPTIMIZE, TODO, HACK, XXX, FIXME and BUG.

## Let have a simple example

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