# Tutorial - Getting started with web 3D using ArcGIS API for JavaScript

In this tutorial you'll learn how to create a globe visualization of places you've been to. Something like this pin globe (the style is all up to you, you can get creative and style it totally different):

![screenshot](./images/screenshot.png)

For this tutorial you'll need to have the data in [geojson](http://geojson.org/) format and like all things web, make sure to have `node` and `git` installed. It's useful if you have a [GitHub account](https://github.com/) so that we can host the code there and deploy it at the end on [GitHub Pages](https://pages.github.com/).

## Step 1: Set up the projet and development environment

### Set up your project

Create the folder for your project and inside it in the console run `npm init`. Fill up project name, keywords, author, etc.
This will generate a `package.json` file.

### Set up a git repo

In the terminal run `git init` in the root folder. Create a `.gitignore` file and add
```
node_modules/*
.DS_Store
```
to exclude them from `git`.

### Set up app structure

Create an `index.html` file and an `app` folder that will store the JS/TS code and a style folder that will store the CSS.

- index.html
- app -> main.js
- style -> main.css

### Set up TypeScript

```
npm install --save-dev typescript
npm install --save @types/arcgis-js-api
```

Add `tsconfig.json` file with the following configuration:

```js
{
  "compilerOptions": {
    "module": "amd",
    "noImplicitAny": true,
    "sourceMap": true,
    "jsx": "react",
    "jsxFactory": "tsx",
    "target": "es5",
    "esModuleInterop": true,
    "experimentalDecorators": true,
    "preserveConstEnums": true,
    "suppressImplicitAnyIndexErrors": true
  },
  "include": [
    "./app/*"
  ],
  "exclude": [
    "node_modules"
  ]
}
```

Add a script in `package.json` to watch for changes and compile everytime a `.ts` file changes:

```js
  ...,
  "scripts": {
    "tsc": "tsc -w" // compiles and watches for changes
  },
```

You can find a detailed description of this step [here](https://developers.arcgis.com/javascript/latest/guide/typescript-setup/index.html).

To test this, change `main.js` file to `main.ts` and run `npm run tsc`. This will compile the files and watch for changes.

### Set up a linter for TypeScript

Install the tool needed for linting:

`npm install --save-dev tslint`

You can read more about the tool [here](https://palantir.github.io/tslint/).
Set up the linting rules in a `tslint.json` file. The rules that I use in this project are the ones that we also use when developing the API:
https://github.com/Esri/jsapi-resources/blob/master/4.x/typescript/demo/tslint.json.

Copy the `tslint.json` file in the root of the project and create a script to run the command from the command lint:

```js
  "scripts": {
    ...,
    "lint": "tslint app/**/*.ts"
  },
```

You can run this command if you want to check for syntax errors in your code.

### Set up a local web server

Install `browser-sync` to set up a web server. This will also listen for changes and reload the page:

`npm install --save-dev browser-sync`

Install `npm-run-all` to have the web server watch for changes in parallel to the typescript compiler.

`npm install --save-dev npm-run-all`

Change the `scripts` property to the following:

```js
"scripts": {
  "watch:build": "tsc -w",
  "watch:server": "browser-sync start --server -w",
  "lint": "tslint app/**/*.ts",
  "start": "npm-run-all --parallel watch:build watch:server"
}
```

### Add repository to GitHub (optional step)

This is optional, but it's useful to have a backup of your code and also to deploy the app at the end using GitHub Pages.

Go to [GitHub](https://github.com/) and create an account if you don't have one yet.
Create a new repository (don't create a Readme file) and then push the local repository to the remote one:

```
git remote add origin https://github.com/your-username/your-app-name.git
git push -u origin master
```

## Step 2: Create a globe

In this step we'll add a 3D map to our project. So let's create a `Map` and render it in a `SceneView`:

```ts
import Map from "esri/Map";
import SceneView from "esri/views/SceneView";

const map = new Map({
  basemap: "topo"
});

const view = new SceneView({
  map,
  container: "viewDiv"
});
```


To see the div element that contains the map, we should change the `height` to `100%`:
```css
html,
body,
#viewDiv {
  margin: 0;
  padding: 0;
  height: 100%;
}
```
