# Webpack

We use webpack to bundle all our files into one source like what happens in ReactJS.
all other files like image, html, etc, just get copied to `dist`.  
It gets multiple transpilers like babel.js, and ts-loader or other modules like prettier or esLint.

Example below sets up a full working webpack configurationsthat all js files and transpiles by and put them in ./dist/bundle.js and hot-loads a server for easy development.

Installing dependencies:

```json
// package.json

 "devDependencies": {
    "@babel/core": "^7.19.3",
    "@babel/preset-env": "^7.19.3",
    "babel-loader": "^8.2.5",
    "copy-webpack-plugin": "^11.0.0",
    "webpack": "^5.74.0",
    "webpack-cli": "^4.10.0",
    "webpack-dev-server": "^4.11.1"
  }
```

then we set babel configs file:

```json
// .babelrc

{
  "presets": [
    [
      "@babel/preset-env",
      {
        "modules": false
      }
    ]
  ]
}
```

then we config our webpack:

```javascript
// webpack.config.js

const webpack = require("webpack");
const path = require("path");
// to copy our html file into dist
const CopyPlugin = require("copy-webpack-plugin");

module.exports = {
  entry: "./src/index.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "bundle.js",
  },
  mode: "development",
  watch: true,
  module: {
    // adding babel to our webpack process
    rules: [
      {
        test: /\.js$/,
        use: "babel-loader",
        exclude: /node_modules/,
      },
    ],
  },
  plugins: [
    // copy our static files
    new CopyPlugin({ patterns: [{ from: "public" }] }),
  ],

  // config an hot-load server to refresh compilation and browser on any changes.
  devServer: {
    static: path.join(__dirname, "dist"),
    port: 9002,
  },

  // we should specify extensions in order for our devServer to work.
  resolve: { extensions: [".js", ".ts"] },
};
```

---

### Babel.js

Older browsers still can't support new javascript features (ES6), so we need to compile them into older version of js by babel.

to use babel solely we need to install it's dependencies:

```json
// package.json

"devDependencies": {
    "@babel/core": "^7.19.3",
    "@babel/preset-env": "^7.19.3",
},
```

now we can transpile our code:

```shell
$ npx babel index.js
```
