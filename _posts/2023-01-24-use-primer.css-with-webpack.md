---
layout: post
tags: node.js
---

[Primer.css](https://primer.style/css/) is the CSS library (and more) created by github. It is complete, with a lot of differents components and I really like how it looks :)

It also has the advantage of being used and maintained by a large company, which mean it will most likely not be abandoned easily.

## Create the base

Create a base node project with `npm init project`. You can then create the following folders and files :

```
./dist
    index.html
./src
    ./scss
        styles.scss
    main.js
```

Inside the `./dist/index.html` file you can set an HTML base :

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Title</title>
    <link rel="stylesheet" href="styles.css">
  </head>
  <body>
    <main></main>
    <script src="scripts.js"></script>
  </body>
</html>
```

Inside the `./src/main.js` file add an import to the style file (in order for webpack to find the style file) :

```js
import "../scss/styles.scss"
```

> You may need to enable [ECMAScript modules](https://nodejs.org/api/esm.html) to use the import syntax.

You can then create this base file `./webpack.config.js` :
```js
import path from 'node:path'
import { fileURLToPath } from 'url'

import MiniCssExtractPlugin  from 'mini-css-extract-plugin'

const __dirname = path.dirname(fileURLToPath(import.meta.url));

export default {
  entry: './src/main.js',
  output: {
    filename: 'scripts.js',
    path: path.resolve(__dirname, 'dist'),
  },
  module: {
    rules: [
      {
        test: /\.(css|sass|scss)$/,
        use: [  { loader: MiniCssExtractPlugin.loader }, 'css-loader', "sass-loader"]
      }
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
        filename: "styles.css",
    })
  ]
}
```

For webpack you have to install the following packages :

```
npm i css-loader mini-css-extract-plugin sass sass-loader webpack webpack-cli
```

`MiniCssExtractPlugin` will be used to load the `scss` files, it will generate a `styles.css` file in the output folder.

You can simply run the command `webpack` to generate the files.

## Import Primer.css

You can now install Primer.css via npm :

```
npm i @primer/css
```

To import the syles you can simply add an import in `./src/scss/styles.scss` :

```scss
@import "@primer/css/index.scss";
```

Or import modules by modules, with the minimum being theses ones :

```scss
@import '@primer/css/color-modes/index.scss';
@import '@primer/css/support/index.scss';
@import '@primer/css/base/index.scss';
```

If you use the `webpack` command right now you will probably run into errors linked to [this github issue](https://github.com/primer/css/issues/722).

To fix this you can download the differents files like in [this answer](https://github.com/primer/css/issues/722#issuecomment-636271979), you will have to places them in the `./dist` folder :

```
https://github.com/images/spinners/octocat-spinner-16px.gif
https://github.com/images/spinners/octocat-spinner-32.gif
https://github.com/images/spinners/octocat-spinner-32-EAF2F5.gif
https://github.com/images/modules/ajax/success@2x.png
https://github.com/images/modules/ajax/error@2x.png
https://github.com/images/modules/ajax/success.png
https://github.com/images/modules/ajax/error.png
```

You will have the following folders and files :

```
./images
    ./spinners
        octocat-spinner-16px.gif
        octocat-spinner-32.gif
        octocat-spinner-32-EAF2F5.gif
    ./modules/ajax
        success@2x.png
        error@2x.png
        success.png
        error.png
```

Then you have to add some config inside the `./webpack.config.js` file in order for 'css-loader' to ignore the url that poses problems inside Primer.css :

```js
...
    rules: [
      {
        test: /\.(css|sass|scss)$/,
        use: [  { loader: MiniCssExtractPlugin.loader },
+        { 
+            loader: 'css-loader',
+            options: {
+              url: false
+            }
+        },
        "sass-loader"]
      }
    ]
...
```

This should do the trick, now then you run `webpack` you will build without errors. For example you can try to add a Box in the `./dist/index.html` file :

```html
<div class="Box">
  <div class="Box-header">
    <h3 class="Box-title">
      Box title
    </h3>
  </div>
  <div class="Box-body">
    Box body
  </div>
  <div class="Box-footer">
    Box footer
  </div>
</div>
```

> If you are importing module by module, you have to import box to use it : 
> ```scss
> @import "@primer/css/box/index.scss";
> ```

## *Optionnal* : purge CSS

Primer.css is quite big (webpack is even warning you : `WARNING in entrypoint size limit`), its weight around 800KiB. In production you will have to gzip this (that will make it down to 70KiB), you can also use other solution like PurgeCSS that will remove all the part of the library that you don't use.

First install the necessary packages :

```
npm i glob-all purgecss-webpack-plugin
```

You simply have to add a little bit of configuration inside the `./webpack.config.js` file :

```js 
import glob from 'glob-all'
import { PurgeCSSPlugin }  from 'purgecss-webpack-plugin'
...
    plugins: [
        new MiniCssExtractPlugin({
            filename: "styles.css",
        }),
+        new PurgeCSSPlugin({
+          paths: glob.sync(`./dist/*.html`, { nodir: true }),
+        })
    ]
...
```

It's as simple as that, for the small HTML example we have the CSS file generated is 30KiB instead of the 800 for the same visual result !

### With Vue.js

In case you are running vue.js you have a little bit more of config to add so that PurgeCSS will check you .vue files. First you probably already have the 'vue-loader' package installed :
```
npm i vue-loader
```

And your config will look like this :

```js
import {VueLoaderPlugin} from 'vue-loader'
...
  module: {
    rules: [
      {
        test: /\.(css|sass|scss)$/,
        use: [  { loader: MiniCssExtractPlugin.loader }, { 
          loader: 'css-loader',
          options: {
            url: false
          }
        }, "sass-loader"]
      },
+      {
+        test: /\.vue$/,
+        loader: 'vue-loader'
+      }
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
        filename: "styles.css",
    }),
    new PurgeCSSPlugin({
      paths: glob.sync(`./dist/*.html`, { nodir: true }),
    })
+    new VueLoaderPlugin()
  ],
...
```

You can then update the config of PurgeCSS to accept vue file :

```js
...
new PurgeCSSPlugin({
  paths: glob.sync([`./src/**/*.vue`, `./dist/*.html`], { nodir: true }),
  safelist: [ /-(leave|enter|appear)(|-(to|from|active))$/, /^(?!(|.*?:)cursor-move).+-move$/, /^router-link(|-exact)-active$/, /data-v-.*/ ],
})
...
```

## *Optionnal* : separate dev and prod

PurgeCSS can be quite long to process, so you may want to deactivate it in development and keep it only in production. To do that you can use multiple `webpack` files. Create theses files :

webpack.common.js
```js
import path from 'node:path'
import { fileURLToPath } from 'url'
import MiniCssExtractPlugin  from 'mini-css-extract-plugin'
import {VueLoaderPlugin} from 'vue-loader'

const __dirname = path.dirname(fileURLToPath(import.meta.url));

export default {
  entry: './src/main.js',
  output: {
    filename: 'scripts.js',
    path: path.resolve(__dirname, 'dist'),
  },
  module: {
    rules: [
      {
        test: /\.(css|sass|scss)$/,
        use: [  { loader: MiniCssExtractPlugin.loader }, { 
          loader: 'css-loader',
          options: {
            url: false
          }
        }, "sass-loader"]
      },
      {
        test: /\.vue$/,
        loader: 'vue-loader'
      }
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
        filename: "styles.css",
    }),
    new VueLoaderPlugin()
  ]
};
```

You can also use a development server in development with the package `npm i webpack-dev-server`.
webpack.dev.js
```js
import { merge } from 'webpack-merge'
import common from'./webpack.common.js'

export default merge(common, {
    mode: 'development',
    devServer: {
        static: {
          directory: path.join(__dirname, 'dist'),
        },
        port: 9000,
    },
});
```

And webpack.prod.js
```js
import { merge } from 'webpack-merge'
import common from'./webpack.common.js'

import glob from 'glob-all'
import { PurgeCSSPlugin }  from 'purgecss-webpack-plugin'

export default merge(common, {
    mode: 'production',
    plugins: [
        new PurgeCSSPlugin({
          paths: glob.sync([`./src/**/*.vue`, `./dist/*.html`], { nodir: true }),
          safelist: [ /-(leave|enter|appear)(|-(to|from|active))$/, /^(?!(|.*?:)cursor-move).+-move$/, /^router-link(|-exact)-active$/, /data-v-.*/ ],
        })
    ]
});
```

You can now use theses npm scripts :
```
"scripts": {
    "build": "webpack --config webpack.prod.js",
    "start": "webpack serve --open --config webpack.dev.js"
}
```

## Links and sources

Optionals : 
- [PurgeCSS](https://purgecss.com/plugins/webpack.html)
- [Webpack : Separate production and dev](https://webpack.js.org/guides/production/)
- [Glob path use multiple path for PurgeCSS config](https://github.com/FullHuman/purgecss-webpack-plugin/issues/42#issuecomment-411675824)