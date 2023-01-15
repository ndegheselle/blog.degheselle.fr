---
layout: post
tags: Node.js
---

Linked [github](https://github.com/ndegheselle/lightcloud).

## Installation

Install [node.js](https://nodejs.org/en/download/). Create a folder for your project and initialize node :
```
mkdir project-name
cd project-name
npm init
```

Once the `package.json` file has been created by node you can install express.js, a library used to create api and web servers :
```
npm install express
npm install dotenv
```

`dotenv` will be used to load configuration from a `.env` file to javascript.

> Having a .env file to store your configuration is a good thing :
> - Everything is conveniently stocked in one file
> - You can ignore the .env file on your git and don't have to worry about leaking credentials
> - It is easy to switch config between development and production environment

## Create base server

Create a `./server.js`, it will be used to configure and start the server.

```js
import express from 'express'
import dotenv from 'dotenv'

dotenv.config();

const app = express();

// Get port from .env
const PORT = process.env.PORT || 80;
// Start server
app.listen(PORT, console.log(`Running on http://localhost:${PORT}`));
```

Create the `./.env` file with the following key :
```
PORT=8000
```

You can already start your server with the cmd `node server.js` and check on [http://localhost:8000](http://localhost:8000), a page will show up with the message : `Cannot GET /`. It's normal since no content has been setup right now.

> You may have the error : `Cannot use import statement outside a module`
> If that's the case you can simply modify the `./package.json` file to add this :
> ```json
> {
>    ...
>    "type": "module"
>    ...
> }
> ```
> You don't have to use the ES module, I personnaly prefer them to the require() syntax.

## Return content

We want two different routes, one for the app that returns HTML and one for the api that returns JSON data. To do so in `server.js` add :

```js
import express from 'express'
import dotenv from 'dotenv'

+ import path from 'node:path';
+ import { fileURLToPath } from 'url';
+ const __dirname = path.dirname(fileURLToPath(import.meta.url));

dotenv.config();

const app = express();

+ // Return an HTML file
+ app.get('/', (req, res) => {
+     res.sendFile(path.join(__dirname, '/index.html'));
+ });
+ 
+ // Return JSON data
+ app.get('/api', (req, res) => {
+     res.status(200).json({msg: "Some API data."});
+ });

// Get port from .env and start server
const PORT = process.env.PORT || 80;
app.listen(PORT, console.log(`Running on http://localhost:${PORT}`));
```

Add an html file `./index.html` :
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Test</title>
  </head>
  <body>
    Hello world
  </body>
</html>
```

Stop the server with CTRL+C and restart it with `node server.js`, after visiting [http://localhost:8000](http://localhost:8000) you should see 'Hello world'. You can also visit [http://localhost:8000/api](http://localhost:8000/api) and see some json.

## *Optional* : Setup for convenience

Convenient (ease of development and debug) but optional steps.

### Auto reload server and use npm scripts

First install `nodemon ` as a dev dependency :
```
npm install nodemon --save-dev
```

You can then use `nodemon server.js` to start the server with an auto reload. For convenience, you can define scripts in the `./package.json` file :
```json
{
  ...
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js"
  }
  ...
}
```

You can then use `npm run start` to start the normal server or `npm run dev` to start the auto reload dev server.
It's a good idea to use the npm scripts since :
- If you leave the project you may forget which command are used to start the server
- If you are using more complex commands to start the server

### Better logging system

It will make debugging easier. Install the package with `npm install morgan`. Then add in `./server.js` :

```js
...
+ import morgan from 'morgan';

const __dirname = path.dirname(fileURLToPath(import.meta.url));

dotenv.config();

const app = express();
+ app.use(morgan('tiny'));
...
```

## Links and sources

- [Base express js setup](https://expressjs.com/en/starter/hello-world.html)
- [Fix : Cannot use import statement outside a module](https://stackoverflow.com/a/59399717/10404482)
- [Return JSON with express](https://stackoverflow.com/a/19696261/10404482)
- [How To Deliver HTML Files with Express](https://www.digitalocean.com/community/tutorials/use-expressjs-to-deliver-html-files)

Optionals :
- [Node.js auto reload](https://stackoverflow.com/a/14406029/10404482)
- [Logging : morgan as an express middleware](https://expressjs.com/en/resources/middleware/morgan.html)