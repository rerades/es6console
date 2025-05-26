# ES6Console.com Project

Welcome to the ever-evolving repository for es6console.com. This project is intended to be a playground for those interested in ECMAScript 6 to 5 transformers.

## Table of Contents
1. [Exciting Features](#exciting-features)
1. [System Requirements](#system-requirements)
1. [Project Structure](#project-structure)
1. [Development Guidelines](#development-guidelines)
  1. [Developer Tools](#developer-tools)
  1. [App Routing Strategy](#app-routing-strategy)
1. [Quality Assurance Testing](#quality-assurance-testing)
1. [Deployment Process](#deployment-process)
1. [Build System](#build-system)
  1. [Configuring Your Environment](#configuring-your-environment)
  1. [Understanding Globals](#understanding-globals)
  1. [Styling Guidelines](#styling-guidelines)
  1. [Optimizations for Production](#optimizations-for-production)

## Exciting Features
Our toolkit of choice includes popular packages such as: 
* [Serverless](http://serverless.com)
* [React](https://github.com/facebook/react)
* [Redux](https://github.com/rackt/redux)
* [React Router](https://github.com/rackt/react-router)
* [Webpack](https://github.com/webpack/webpack)
* [Babel](https://github.com/babel/babel)
* [ESLint](http://eslint.org)

## System Requirements
To get started, ensure you have:
* Node `^4.5.0`
* Either Yarn `^0.17.0` or npm `^3.0.0`

### Installation from Source Code

This step involves cloning the project and installing dependencies. Begin with:

```bash
$ git clone https://github.com/matthisk/es6console.git <my-project-name>
$ cd <my-project-name>
```

Next, install your project dependencies. We recommend [Yarn](https://yarnpkg.com/) as it ensures deterministic installs, but `npm install` works as well.

```bash
$ npm install    # Install project dependencies
$ npm run dev    # Compile and launch (same as `npm start`)
```

Though you will likely rely on `npm dev` more often, additional scripts are available:

|`npm run <script>`|Description|
|------------------|-----------|
|`start`|Serves your app at `localhost:8000`. Hot Module Replacement (HMR) will be enabled in development.|
|`compile`|Compiles the application to disk (`~/dist`.|
|`dev`|Same as `npm start`, but also enables nodemon for the server.|
|`test`|Runs unit tests with Karma and generates a coverage report.|
|`test:dev`|Runs Karma and watches for changes to re-run tests, but does not generate coverage reports.|
|`deploy`|Runs linter, tests, and then compiles your application to disk, if tests are successful.|
|`lint`|Lints all `.js` files.|
|`lint:fix`|Lints and fixes all `.js` files. [Read more on this](http://eslint.org/docs/user-guide/command-line-interface.html#fix).|

(Note: `deploy`, `deploy:staging`, `deploy:prod`, `deploy_lambda`, `deploy_lambda:staging`, and `deploy_lambda:prod` are identical and override `NODE_ENV` to "production".)

## Project Structure


```
.
├── bin                      # Scripts for building/starting
├── config                   # Project and build configurations
├── public                   # Public static assets (not directly imported in source code)
├── server                   # Express application that enables webpack middleware
│   └── main.js              # Development Server entry point
├── src                      # Source code for the application
│   ├── index.html           # Main HTML page container for the app
│   ├── main.js              # Bootstrap and rendering for the application
│   ├── components           # Reusable global presentational components
│   ├── containers           # Reusable global container components
│   ├── layouts              # Defines the major structures of a page
│   │   └── CoreLayout.js    # CoreLayout receives children for each route
│   │   └── CoreLayout.scss  # Styles related to CoreLayout
│   │   └── index.js         # Main file for the layout
│   ├── routes               # Main route definitions and async split points
│   │   ├── index.js         # Bootstrap main application routes with the store
│   │   ├── Home             # Fractal route
│   │   │   ├── index.js     # Route definitions and async split points
│   │   │   ├── assets       # Assets needed to render components
│   │   │   ├── components   # Presentational React components
│   │   │   └── routes **    # Fractal sub-routes (** optional)
│   ├── store                # Redux-specific pieces
│   │   ├── createStore.js   # Create and modify redux store
│   │   └── reducers.js      # Reducer registration and injection
│   └── styles               # Styles applicable to the entire application
├── lambda                   # AWS Lambdas
└── tests                    # Unit tests
```

## Development Guidelines

#### Developer Tools

## Quality Assurance Testing
Creating a `.spec.js` file within `~/tests` allows us to add a unit test. Karma automatically recognizes and responds to these files. Mocha and Chai are available within the test, with no import required. Coverage reports are compiled to `~/coverage` by default. For changing the type of reports and their compilation location, modify `coverage_reporters` in `~/config/project.config.js`.

## Deployment Process
ES6Console is hosted on Amazon S3. Running `npm run deploy` syncs the `~/dist` folder to the preconfigured S3 bucket in `config/environment.config.js`. The website uses several backend scripts hosted on AWS Lambda, deployed automatically with serverless framework by running `npm run deploy_lambda`.

### Configuring Your Environment

Default configuration for the project can be found under `~/config/project.config.js`. You can redefine `src` and `dist` directories, modify compilation settings, edit your vendor dependencies etc, without the need to interact with the actual webpack build configuration.

Environment-specific overrides timing can be modified under `~/config/environments.config.js` where overrides can be defined on a per-NODE_ENV basis. Examples for `development` and `production` are given, let these be your guide. Here are some common configuration options:

|Key|Description|
|---|-----------|
|`dir_src`|base path for the application source code|
|`dir_dist`|path to compile the application|
|`server_host`|hostname for the Express server|
|`server_port`|port for the Express server|
|`compiler_devtool`|type of source-maps to generate (set to `false`/`null` to disable)|
|`compiler_vendor`|packages to split into the vendor bundle|

Webpack is configured to utilize [resolve.root](http://webpack.github.io/docs/configuration.html#resolve-root), allowing for local package imports as if traversing from `~/src` root. For example:

```js
// current file: ~/src/views/some/nested/View.js
// This:
import SomeComponent from '../../../components/SomeComponent'

// Can convert to this:
import SomeComponent from 'components/SomeComponent' // Simplified!
```

### Understanding Globals

These are globally available variables across your source code. To modify them, locate the `globals` key in `~/config/project.config.js`. When adding new globals, remember to add them to `~/.eslintrc`.

|Variable|Description|
|---|---|
|`process.env.NODE_ENV`|The `NODE_ENV` active when the build started|
|`__DEV__`|True when `process.env.NODE_ENV` is `development`|
|`__PROD__`|True when `process.env.NODE_ENV` is `production`|
|`__TEST__`|True when `process.env.NODE_ENV` is `test`|

### Styling Guidelines

Our set up supports both `.scss` and `.css` file extensions. Imported styles are processed with [PostCSS](https://github.com/postcss/postcss) for minification and autoprefixing, and they are extracted to a `.css` file during production builds.

### Optimizing for Production

[Babel-plugin-transform-runtime](https://www.npmjs.com/package/babel-plugin-transform-runtime) is used on Babel for transforming in a non-inlined manner. In production, webpack extracts styles to a `.css` file, reduces your JavaScript size, and performs other optimizations like module deduplication.
