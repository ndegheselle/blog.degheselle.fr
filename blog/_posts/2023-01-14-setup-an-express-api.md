---
layout: post
tags: node.js
---

* TOC
{:toc}

A short tutorial on how to install an express server with some optional configuration to make developement easier.

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

> You may have the error : `Cannot use import statement outside a module`. The solution can be found [here](https://stackoverflow.com/a/59399717/10404482).

## Return content

Now we need to add endpoint to the api that will return content. Add a route inside the `server.js` :

```js
import express from 'express'
import dotenv from 'dotenv'

+ import path from 'node:path';
+ import { fileURLToPath } from 'url';
+ const __dirname = path.dirname(fileURLToPath(import.meta.url));

dotenv.config();

const app = express();

+ // Return JSON data
+ app.get('/api', (req, res) => {
+     res.status(200).json({msg: "Some API data."});
+ });

// Get port from .env and start server
const PORT = process.env.PORT || 80;
app.listen(PORT, console.log(`Running on http://localhost:${PORT}`));
```

If you check on [http://localhost:8000/api](http://localhost:8000/api) you can see that JSON data is returned.

## Improve routing

In the future there will be a lot of different routes, in order to nicely separate everything we will put routes in specifics files.
Create a `./routes/apiRoutes.js` file :

```js
import express from 'express'

const router = express.Router();

router.route('/').get((req, res) => {
    res.status(200).json({msg: "Some API data."});
});

export default function(app) {
    app.use(`/api/v${process.env.API_VERSION}`, router);
};
```

Add the `API_VERSION=1` key in the `./.env`. 

Then modify the `./server.js` with the following content :

```js
import express from 'express'
import dotenv from 'dotenv'
import morgan from 'morgan'

import createApiRoutes from './routes/apiRoutes.js'

dotenv.config();

const app = express();
app.use(morgan('tiny'));

createApiRoutes(app);

// Get port from .env and start server
const PORT = process.env.PORT || 80;
app.listen(PORT, console.log(`Running on http://localhost:${PORT}`));
```

You can now go on [http://localhost:8000/api/v1](http://localhost:8000/api/v1) and get the json :

```json
{
  "msg": "Some API data."
}
```

## *Optional* : Setup for convenience

Here are a few handy but optional steps to help you develop and debug.

### Auto reload server and use npm scripts

Instead of stopping and restarting your node server at each file update, you can setup a file watcher that will automatically restart the server on updates.

First install `nodemon` as a dev dependency :
```
npm install nodemon --save-dev
```

You can then use `nodemon server.js` to start the server with  auto reload. For convenience, you can define scripts in the `./package.json` file :
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
-  You may need more complex commands to start the server in the future

### Better logging system

A logging system can list the routes that have been called, which can be useful to understand what went wrong during debugging. Also, in the future it may be used in production to store useful information (like wrong logging attempts for example).

Install the package with `npm install morgan`. Then add in `./server.js` :

```js
...
+ import morgan from 'morgan';

const __dirname = path.dirname(fileURLToPath(import.meta.url));

dotenv.config();

const app = express();
+ app.use(morgan('tiny'));
...
```

### Manage API errors and 404

In order to simplify debuging of the API you can add middlewares that will handles errors and 404. Create a `./middlewares` folder, with the following file :

```
./middlewares
    apiErrorMiddleware.js
```

Inside the file `./middlewares/apiErrorMiddleware.js` :

```js
const apiNotFound = (req, res, next) => {
    const error = new Error(`Not Found - ${req.originalUrl}`)
    res.status(404)
    next(error)
}

const apiErrorHandler = (err, req, res, next) => {
    const statusCode = res.statusCode === 200 ? 500 : res.statusCode
    res.status(statusCode)
    res.json({
        message: err.message,
        stack: process.env.NODE_ENV === 'production' ? null : err.stack,
    })
}

export { apiNotFound, apiErrorHandler }
```

Add the `NODE_ENV=dev` key in the `./.env`.

And then in `./routes/apiRoutes.js` add :

```js
export default function(app) {
    app.use(`/api/v${process.env.API_VERSION}`, router);
+    app.use('/api', apiNotFound);
+    app.use('/api', apiErrorHandler);
};
```

The server will now return JSON during errors and 404 instead of an HTML page.

## Links and sources

- [Base express js setup](https://expressjs.com/en/starter/hello-world.html)
- [Fix : Cannot use import statement outside a module](https://stackoverflow.com/a/59399717/10404482)
- [Return JSON with express](https://stackoverflow.com/a/19696261/10404482)

Optionals :
- [Express middlewares](https://expressjs.com/en/guide/using-middleware.html)
- [Node.js auto reload](https://stackoverflow.com/a/14406029/10404482)
- [Logging : morgan as an express middleware](https://expressjs.com/en/resources/middleware/morgan.html)