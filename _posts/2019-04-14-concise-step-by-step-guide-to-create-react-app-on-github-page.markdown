---
layout: post
title:  "A concise step-by-step guide to create React app on Github page"
date:   2019-04-14 00:00:00 +0700
tags: react github-page
---

Create React app

If you have it already, feel free to skip this step. I normally use the boilerplate create-react-app , which allows me to create a React app in one single command

    npx create-react-app my-app

This wil create a my-app folder, which is ready for the development with npm start

## Create Github page

If you looked at the tutorialhttps://pages.github.com, you would see that there are 2 types of Github pages: users/organization site and project site. One of the differences is that the URL User/organization will create your url in {username}.github.io while project site will be {username}.github.io/{project-name}. We will proceed with the second one, since the first one only allow us to use master branch. Create a new project on Github, and adding the remote to your created React app

    git remote add origin https://github.com/*{username}*/*{project-name}*.git

## Deployment

Add gh-pages package to your React app

    npm install gh-pages --save-dev

Then, in the package.json, you will have to make some modification

* Adding hompepage: {your-site-url}

* Adding deploy script into scripts section. If you are using different build script, just change the predeploy command with yout build script

    “predeploy”: “react-scripts build”,

    “deploy”: “gh-pages -d {your-created-dist-dir}”

* Finally, for those who using create-react-app , possibly we have to add the following "main": "index.js"

* Time to reap the result: npm run deploy . And that’s it :)

Here is my samplepackage.json

    {
      "name": "liemly",
      "version": "0.1.0",
      "private": true,
      "main": "index.js",
      "dependencies": {
        "react": "^16.8.6",
        "react-dom": "^16.8.6",
        "react-scripts": "2.1.8",
    },
      "scripts": {
        "start": "react-scripts start",
        "build": "react-scripts build",
        "test": "react-scripts test",
        "eject": "react-scripts eject",
        "predeploy": "react-scripts build",
        "deploy": "gh-pages -d build"
    },
      "eslintConfig": {
        "extends": "react-app"
      },
      "browserslist": [
        ">0.2%",
        "not dead",
        "not ie <= 11",
        "not op_mini all"
      ],
      "devDependencies": {
        "gh-pages": "^2.0.1"
      },
      "homepage": "https://liemlyquan.github.io/liemly/"
    }

And with such, I have mine up and running at [https://liemlyquan.github.io/liemly/](https://liemlyquan.github.io/liemly/)
