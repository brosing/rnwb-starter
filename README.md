# Steps to reproduce this starter kit...

## Step 1: React & React Native Initialize

- Init package.json
- Create folder `packages`
- Run inside packages folder
  ```
  npx react-native init app --template react-native-template-typescript
  npx create-react-app web --template typescript
  ```
- Back to Root & add this to `package.json` to create Yarn Workspaces:
  ```
  "private": true,
  "workspaces": ["packages/*"]
  ```
- Move to packages app & web to delete `node_modules`
- Add `@rnwb/${packageName}` to each package.json name & dependencies
- Inside app package.json add this:
  ```
  "workspaces": {
    "nohoist": [
      "react-native",
      "react-native/**",
      "react",
      "react/**"
    ]
  }
  ```
  [Read this](https://yarnpkg.com/blog/2018/02/15/nohoist) for more.
- the last step in app package is add some properties in metro.config.js module exports:
  ```
  resolver: {
    extraNodeModules: new Proxy(
      {},
      {
        get: (target, name) => {
          return path.join(__dirname, `node_modules/${name}`);
        },
      },
    ),
  },
  watchFolders: [
    path.resolve(__dirname, '../common'),
    path.resolve(__dirname, '../common/node_modules')
  ],
  ```
- Don't forget to use `const path = require('path')`
- Configure React web to use react-native AppRegistry:
  ```
  import {AppRegistry} from 'react-native';
  import {App} from '@rnwb/common';
  import * as serviceWorker from './serviceWorker';

  // register the app
  AppRegistry.registerComponent('App', () => App);

  AppRegistry.runApplication('App', {
    initialProps: {},
    rootTag: document.getElementById('root')
  });

  // If you want your app to work offline and load faster, you can change
  // unregister() to register() below. Note this comes with some pitfalls.
  // Learn more about service workers: http://bit.ly/CRA-PWA
  serviceWorker.unregister();
  ```
- Go to package web & run `yarn add react-native-web` & `yarn add -D @types/react-native`

## Step 2: React Native Web Initialize
- Create folder named common inside packages
- Move to common folder & run `yarn init -y`
- Don't forget to change package name to `@rnwb/common`
- Run `yarn add react react-native`
- Add devDependencies `yarn add -D @types/react-native typescript`
- Add build script:
  ```
  "scripts": {
    "build": "rm -rf dist && tsc",
    "watch": "tsc --watch"
  }
  ```
- Create folder named `src` and file named `index.tsx`
- Try to create a react-native component there
- Move to package app & web, delete app.js & edit their index.js to import App inside package common:
  ```
  import { App } from "@rnwb/common"
  ```
- Tips:
  - there will be some unused files in web, just delete it!
  - Create the file `packages/web/public/index.css` with the following contents:
  ```
  html,
  body,
  #root,
  #root > div {
    width: 100%;
    height: 100%;
  }

  body {
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }
  ```
  - Edit `packages/web/public/index.html` to include our CSS before closing the head tag:
  ```
  <link rel="stylesheet" href="%PUBLIC_URL%/index.css" />
  ```
  - Configure tsconfig.json: just copy it from web package & modify it.
    - Remove `noEmit`, `isolatedModules` & `allowJs`
    - Add `outDir` & `declaration`
    - Changes `module` to 'commonjs'
  - Changes package.json `main` to "dist/index.js"
  - Don't forget to build your common package before running the web or app
  - Go back to Root folder & run `yarn` to make sure all dependecies is installed

## Step 4: How to build
- Create component in package common
- Run `yarn build` in package common
- Then you can go to each package folder & use `yarn start` to run in each platform