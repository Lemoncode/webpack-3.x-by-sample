# 05 JQuery

So far we have made good progress on our journey... but we are lacking one of the
basic pillars of web development, consuming third party libraries.

In this demo we will install a legacy library (jquery) via npm, define it as global, and use it. Finally we will end up creating a separate bundle for libraries.

We will start from sample _00 intro/04 output_.

Summary steps:

- Prerequisites:
  - nodejs packages must be installed
  - (Optional) starting point _00 intro/05 jquery_
- Install @babel/plugin-syntax-dynamic-import package via npm
- Configure the .babelrc configuration file
- Change the synchronous module import on students.js and load the averageService module lazily.
- Add the container div on index.html

# Steps to build it

## Prerequisites

Prerequisites, you will need to have nodejs installed (at least v 8.9.2) on your computer. If you want to follow this guide steps you will need to take as starting point sample _00 intro/05 jquery_.

## Steps

- `npm install` to install previous sample packages:

```bash
npm install
```

- Let's start by downloading the @babel/plugin-syntax-dynamic-import library via npm. In this case we will execute the following command on the command prompt: `npm install --save-dev @babel/plugin-syntax-dynamic-import`.

```
npm install --save-dev @babel/plugin-syntax-dynamic-import
```

It automatically adds that entry to our _package.json_.

_package.json_

```diff
  "devDependencies": {
    "@babel/core": "^7.1.0",
+    "@babel/plugin-syntax-dynamic-import": "^7.0.0",
    "@babel/preset-env": "^7.1.0",
    "babel-loader": "^8.0.2",
    "html-webpack-plugin": "^3.2.0",
    "webpack": "^4.19.1",
    "webpack-cli": "^3.1.1",
    "webpack-dev-server": "^3.1.8"
  },
```

- First we will create the .babelrc config file on the root with the next content:

```diff
+ {
+   "presets": [
+     [
+       "@babel/preset-env",
+       {
+         "useBuiltIns": "entry"
+       }
+     ]
+   ]
+ }
```

- Now we will configure the babel plugin syntax dynamic import at the plugin section of the .babelrc file:

```diff
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "useBuiltIns": "entry"
      }
    ]
  ],
+  "plugins": ["@babel/plugin-syntax-dynamic-import"]
}
```

- Now it's ready to be used. Just to test it, let's change the synchronous import on the students.js file. Let's import the averageService module lazily when button clicked:

_./students.js_

```diff

- import {getAvg} from "./averageService";

$('body').css('background-color', 'lightSkyBlue');

const scores = [90, 75, 60, 99, 94, 30];

- const averageScore = getAvg(scores);
- const messageToDisplay = `average score ${averageScore}`;
- document.write(messageToDisplay);


+ const handleOnClick = () => {
+   const averageServicePromise = import(/* webpackChunkName averageService */ "./averageService");
+
+   averageServicePromise.then(averageServiceModule => {
+     const averageScore = averageServiceModule.getAvg(scores);
+     const messageToDisplay = `average score ${averageScore}`;
+
+     const element = document.createElement("div");
+     element.innerText = messageToDisplay;
+
+     document.getElementById("container").append(element);
+   });
+ };

+ const button = document.createElement("button");
+ button.innerText = "Lazy loading sample";
+ button.onclick = handleOnClick;

+ document.getElementById("container").append(button);

```

- Finally we need add the container div into index.html:

_./index.html_

```diff

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Webpack 4.x by sample</title>
  </head>
  <body>
    Hello Webpack 4!
+    <div id="container"></div>
  </body>
</html>

```

## Running and Testing it

- Now we can just execute the app (`npm start`) and check how the averageService bundle load on handle button click event (checking the browser network dev tool).

```bash
npm start
```