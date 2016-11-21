#Package.json, Babel and Webpack
===========


##  Overview

This reading will consist of an introduction to the package.json. Followed by a deeper dive into the babel.rc. Finally wrapping everything up with a brief explanation of how Webpack automates and codifies our development processes. 

## Objectives

 1. Start, Run, Bundle from package.json.
 2. NPM --save, --save-dev.
 3. Locate and install babel plugins.
 4. Explain and understand a .babelrc file.
 5. Webpack setup with Babel.


## Exposition
### Package.json explained

To begin our development process we must have a configuration file that tells our application about our project depencies, the file is called package.json it holds various metadata relevant to the project. It is also used for various other things like sending meta data to npm or version and license information. However, we will be focusing on using this file to run scripts for us. Let's take a look at an example package.json file and how a script is used. 

***Example of underscore.js package.json***

```javascript
{
  "name" : "underscore",
  "description" : "JavaScript's functional programming helper library.",
  "homepage" : "http://documentcloud.github.com/underscore/",
  "keywords" : ["util", "functional", "server", "client", "browser"],
  "author" : "Jeremy Ashkenas <jeremy@documentcloud.org>",
  "contributors" : [],
  "dependencies" : {
  	"jquery": "^4.0.0"
  },
  "repository" : {"type": "git", "url": "git://github.com/documentcloud/underscore.git"},
  "main" : "underscore.js",
  "version" : "1.1.6",
   "scripts": {
    "start": "node underscore.js",
    "test": "mocha --reporter spec test"
  },
  "devDependencies": {
    "mocha": "^1.17.1"
  }
}
```
####Invoking commands
>Both start and test are special values and can be invoked directly.

`$ npm start` or `npm run start` |
`$ npm test`  or `npm run test`

###Installing dependencies using npm

Similar to ruby gems javascript has packages that are basically large js libraries with tons of objects and methods that help us build code  quicker than usual. Our package.json file helps us keep track of which packages we would like to use throughout our application, just like a gemfile holds our ruby gems. However, there are some packages we only want to use when developing for example our mocha tests. The command below show how to install dependencies for production or development. 

 
>Saving to development

`npm --save-dev mocha`

```javascript
{
  "devDependencies": {
    "mocha": "^1.17.1"
  }
}
```

>Saving to production

`npm --save react`

```javascript
{
  "dependencies": {
    "react": "^0.15.0"
  }
}
```

###Babel Transpiler 

JavaScript (or ECMAScript) as a language is being updated faster than ever before. With these updates comes a whole slew of new language features and sugar that really take the pain out of some of the previously most complex or convoluted areas. Browsers and other JavaScript engines (node.js) haven’t quite caught up with this evolving spec, however there exists a way to use (most) of these features today, and that is Babel.js

***We'll install the babel-cli package with the next command. This will install the latest stable version of babel-cli for the current project and list it as one of the devDependencies in package.json too:***
>Remember we need a package.json file _npm init -y_

>This setup is for on the fly.

`npm install --save-dev babel-cli`

>Pre-compiled

`npm install babel-loader babel-preset-es2015 babel-preset-react -S`

Babel will also look for a configuration file called .babelrc  in the path of your project. This will tell babel what options to use, including which presets to use, which is all we’ll need in the file for now.

```
{
    presets: ['es2015', "react"]
}
```

Now we can use it one of two ways, on the fly or pre-compiled. If you’re going to pre-compile your code, then you’ll likely want to use a build system like gulp or webpack. 


###Webpack Introduction

Webpack is a module bundler which takes modules with dependencies and generates static assets by bundling them together based on some configuration.

The support of loaders in Webpack makes it a perfect fit for using it along with React and we will discuss it later in this post with more details.

Let's start with installing webpack using npm

`npm install webpack --save`

Webpack requires some configuration settings to carry out its work and the best practice is doing it via a config file called webpack.config.js.

`touch webpack.config.js`

>update config with:

```javascript
var webpack = require('webpack');
var path = require('path');

var BUILD_DIR = path.resolve(__dirname, 'src/client/public');
var APP_DIR = path.resolve(__dirname, 'src/client/app');

var config = {
  entry: APP_DIR + '/index.jsx',
  output: {
    path: BUILD_DIR,
    filename: 'bundle.js'
  }
};

module.exports = config;
```

>Your file tree should look like this:

```
|_ .babelrc
|
|_ package.json
|
|_ src
|	|_ client
|		|_ app
|		|	|_ AwesomeComponent.jsx
|		|	|_ index.jsx
|		|_ index.html
|		|_ public
|			|_bundle.js
|_ webpack.config.js
```

The minimalist requirement of a Webpack config file is the presence of entry and output properties.

The ***APP_DIR*** holds the directory path of the React application's codebase and the ***BUILD_DIR*** represents the directory path of the bundle file output.

The output instructs Webpack what to do after the bundling process has been completed. Here, we are instructing it to use the ***src/client/***public directory to output the bundled file with the name bundle.js

We are now setup for on the fly babel with webpack by running this command:
`./node_modules/.bin/webpack -d`

To make it more interactive and use for pre-compiled, create an index.html file in the src/client directory and modify it to use this bundle.js file

```javascript
<html>
  <head>
    <meta charset="utf-8">
    <title>React.js using NPM, Babel6 and Webpack</title>
  </head>
  <body>
    <div id="app" />
    <script src="public/bundle.js" type="text/javascript"></script>
  </body>
</html>
```

>Update webpack.config with:

```javascript
// Existing Code ....
var config = {
  // Existing Code ....
  module : {
    loaders : [
      {
        test : /\.jsx?/,
        include : APP_DIR,
        loader : 'babel'
      }
    ]
  }
}
```

The last step is to automate the loader when you update any of the saved files. For that to happen lets modify our package.json.

```javasript
{
  // ...
  "scripts": {
    "dev": "webpack -d --watch",
    "build" : "webpack -p"
  },
  // ...
}
```

Now the command `npm run build` runs Webpack in production mode, which minimizes the bundle file automatically, and the command `npm run dev` runs the Webpack in the watch mode.




## Resources

https://blog.risingstack.com/the-react-way-getting-started-tutorial/
https://html5hive.org/es6-and-babel-tutorial/
https://babeljs.io/docs/setup/#installation
https://webpack.github.io/
