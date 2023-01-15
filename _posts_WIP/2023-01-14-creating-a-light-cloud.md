---
layout: post
tags: Node.js
---

Create a lightweight alternative for nextcloud, main objectives are :
- Use mature and documented libraries
- Easier database ORM/ODM
- Simpler component system based on a CSS library

## Libraries used

- **Database** : Mongodb
- **Backend** : Mongoose / Expresse.js
- **Frontend** : Petite-vue.js / Spectre.css

## Installation

Install [node.js](https://nodejs.org/en/download/) and [mongodb](https://www.mongodb.com/docs/manual/installation/).

Install the node.js packages :
```
npm install express
npm install mongoose
```

## Backend

- Create base application with express, separating app and API
- Create authentification for app and API 