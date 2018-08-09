---
layout: post
title: "React and Storybook: A Tale of Reusability"
description: "Create modular and reusable components that can be shared across projects by building a component library with React and Storybook."
date: 2018-08-15 8:30
category: Technical Guide, Architecture, Frontend, React, Auth0 Engineering
design: 
  bg_color: "#222228"
  image: https://cdn.auth0.com/blog/logos/storybook-react.png
author:
  name: Dan Arias
  url: http://twitter.com/getDanArias
  mail: dan.arias@auth.com
  avatar: https://pbs.twimg.com/profile_images/1002301567490449408/1-tPrAG__400x400.jpg
tags: 
  - react
  - storybook
  - frontend
  - ui
  - ux
  - engineering
  - component-library
  - reusability
  - banking
related:
  - 
---

For the scope of this introductory blog post, we are going to demonstrate how to start a new project using Storybook to get familiar with its platform and mechanics. In a follow-up post, we are going to explore deeper into our "storybook-driven development" practices.

We are going to create the components of an online banking app called Marvel Bank. Let's start by learning how we can setup Storybook in a brand-new `create-react-app` project.

## Setting Up a React Project

We are going to use [`create-react-app`](create-react-app) to scaffold easily a new React application.

We can run `create-react-app` using `npx` which comes bundled with `npm 5.2+` and higher:

```shell
npx create-react-app marvel-bank
cd marvel-bank
npm start
```

If you use `npm 5.1` or earlier, you can't use `npx`. Instead, install `create-react-app` globally:

```shell
npm install -g create-react-app
```

And then run:

```shell
create-react-app marvel-bank
cd marvel-bank
npm start
```

If a browser window or tab doesn't open automatically, open [`http://localhost:3000/`](http://localhost:3000/) to see the app. Open the newly created `marvel-app` folder on your preferred code editor or IDE.

Within `marvel-bank`, the `src` folder contains the core business logic of our React application. Let's do some housekeeping and remove files and code that we don't need.

Open `src/App.js` and trim it like so:

```javascript
// src/App.js

import React, { Component } from "react";
import "./App.css";

class App extends Component {
  render() {
    return <div className="App" />;
  }
}

export default App;
```

Next, let's delete the following files:

- `App.test.js`
- `logo.svg`

Why are we removing testing files? Testing is super important! We agree. We have a blog series planned in which we are going to showcase how to perform UI Testing in React.

> **Spoiler**: Storybook is an essential part of our testing stack as it makes testing a painless experience.

We are ready for our first `git` commit. But before we do so, let's ensure that we have everything we need to ignore specified in the `.gitignore` file. If you are using an IDE like WebStorm, for example, there are extra files that this tool creates that is better to keep out of the `git` repo. I personally like to use [`gitignore.io`](https://www.gitignore.io/) to create my `.gitignore` file automatically. The categories that I choose to create the file are WebStorm, IntelliJ, OSX, Windows, and Visual Studio Code, since they are the tools and platforms that are most commonly used.

With a comprehensive `.gitignore` file in place, let's initialize a `git` repository (make sure that `marvel-app` is the current working directory):

```shell
git init
```

Let's run a quick `git status` check to see what files are being untracked:

```shell
git status
```

In my case, `git` replies with:

```shell
Untracked files:
  (use "git add <file>..." to include in what will be committed)

        .gitignore
        README.md
        package-lock.json
        package.json
        public/
        src/
```

That looks good to me ([LGTM™](https://knowyourmeme.com/memes/lgtm)). Let's go ahead and add these files to `git` to include them in what will be committed:

```shell
git add .
```

We used a shortcut here to add all the files that were being untracked, the `.` after the `add` command.

> If you want a `git` refresher, I highly recommend this amazing [_Git & Version Control_](https://www.git-tower.com/learn/) tutorial by the folks at [Git Tower](https://www.git-tower.com/).

Running `git status` again would show us the new files that have been added that include changes to be committed. Let's create our first commit then:

```shell
git commit -m "Initial commit"
```

We'll get a reply by `git` showing all the files that were changed and the insertions that were made. Having a well-thought commit history in place will let us go back in time if we ever need to use an older version of our code. `CTRL/CMD + Z` can only go so far back!

## Using a Style Guide for Consistent Branding

Using a style guide can lead to better application design and development. As explain by the [Marvel Design team](https://blog.marvelapp.com/style-guides-better-design-development/), "during the design phase style guides encourage consistency in the visual identity and help keep the interface system as logical as possible, which makes for a better UX."

What is a style guide anyway? Marvel has a solid answer: "A style guide clearly demonstrates how all interface elements and components are to be visually represented. It’s essentially a master reference for the user interface (UI)."

In this sense, a component library is part of a style guide as it presents our components visually and it is the single source of truth for each component (the master reference). To create a comprehensive style guide, the component library can be accompanied the by design documents that define font styles and sizes as well as primary and secondary user interface colors. A Design Team usually creates a "UI Kit" that is handed to developers for implementation. _Shopify_ explains that a [UI kit is an assortment of graphic files](https://www.shopify.com/partners/blog/104547526-the-benefits-of-using-a-ui-kit) that include UI components (buttons, checkboxes, progress bars, etc.) for the purpose of defining and designing the user interface.

For the online banking app that we are going to start creating in this blog post, we are going to use a free Banking UI Kit that [Marvel created for Sketch](https://blog.marvelapp.com/free-banking-ui-kit-for-sketch/). To honor their effort, we named the app `marvel-bank`. The UI Kit looks as follows:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/storybook-intro/marvel-banking-ui-kit.png" alt="Marvel Banking UI Kit">
</p>

The UI Kit is designed for iOS, but we are going to adapt it for usage in a responsive web application. To make it users for developers to understand the UI Kit, we created a design document where we can get the font names, the sizing, the color hex codes, etc: [Marvel Bank Style Guide](https://auth0.github.io/marvel-bank-styleguide/).

In the following excerpt, notice how is much easier to understand the guidelines that a developer would need to follow when creating the components presented in the UI Kit:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/storybook-intro/design-document.png" alt="Design document excerpt">
</p>

If we need to implement a green button, we can consult the design document and copy the hex code of `$green`; however, it would be much easier if we could code the elements of the design document into our application in a modular way and reusable way. We have plenty of options to do that:

- We could use CSS variables, also known as [custom properties](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_variables).
- We could use [SCSS](https://sass-lang.com/) or [Stylus](http://stylus-lang.com/) to create style partials and variables.
- We could also opt for using [CSS-in-JS](https://reactjs.org/docs/faq-styling.html) through a library like [`styled-components`](https://www.styled-components.com/).

Our Team currently has been using a combination of Styles and [CSS Modules](https://github.com/css-modules/css-modules) to manage the styling of the projects. React has unified the three layers of front-end development (HTML, CSS, and JavaScript) under one layer powered by JavaScript, JSX, and CSS-in-JS. For this post, however, we are going to rely on SCSS to create the modular components to minimize the development overhead.

> **Feedback** Would you like to learn how we are using `styled-components` to create a modern component library? Let me know in the comments below and I could make it part of this React series.

## Adding SCSS to `create-react-app`

We [add `SCSS` support to `create-react-app` without having to `eject`](https://techcookbook.com/react/use-scss-with-create-react-app). We need to install a few packages to help us achieve this goal:

npm:

```shell
npm install --save node-sass-chokidar npm-run-all
```

yarn:

```shell
yarn add node-sass-chokidar npm-run-all
```

[`node-sass-chokidar`](https://www.npmjs.com/package/node-sass-chokidar) is a thin wrapper around node-sass executable to use chokidar instead of Gaze when watching files.

[`npm-run-all`](https://www.npmjs.com/package/npm-run-all) is a CLI tool to run multiple npm-scripts in parallel or sequential.

Next, we need to modify our `package.json` to watch and process `.scss` files into `.css` ones:

```json
{
  "name": "marvel-bank",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "node-sass-chokidar": "^1.3.3",
    "npm-run-all": "^4.1.3",
    "react": "^16.4.1",
    "react-dom": "^16.4.1",
    "react-scripts": "1.1.4"
  },
  "scripts": {
    "build-css": "node-sass-chokidar src/ -o src/",
    "watch-css":
      "npm run build-css && node-sass-chokidar src/ -o src/ --watch --recursive",
    "start-react": "react-scripts start",
    "start": "npm-run-all -p watch-css start-react",
    "build-react": "react-scripts build",
    "build": "npm-run-all -s build-css build-react",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

These scripts allow us to compile `.scss` files into `.css` and to keep watching the `src` folder for changes to the content of existing `.scss` files or the addition of new ones. To complete this task, we need to change the file extension of `App.css` and `index.css` to `.scss`. Depending on your development environment, this can be done in different ways such as renaming the files or refactoring their name or file type.

On the shell, stop the running `create-react-app` and re-issue the `npm start` command again to include these new scripts in the build process. With that, we are ready to start developing the app.

Before we include any styling or components, we need to create a sound file structure.

## Project Structure

An internal pattern that we use is to have specialized folders to hold the different elements of our React application. In one of our projects, we have the following structure:

```shell
- root
    |-- api/
    |-- features/
    |-- assets/
    |-- redux/
    |-- styles/
    |-- utils/
    |-- App.js
    |-- index.js
    |-- routes.js
```

For the initial phase of this project, we only need the `features` and the `styles` folder. The `features` holds our components in a categorized manner. We define features of our applications, such as `search` or `reports`, and create components that power and build that feature. What Storybook will help us solve is the event where feature share components. Instead of duplicating the code across features, the feature will implement the component from a central source. As the name suggests, these common components go into a `common` feature folder.

This is the project structure we want for this phase of the project:

```shell
- root
    |-- features/
    |---- common/
    |-- styles/
    |---- _theme.scss
    |-- App.js
    |-- App.scss
    |-- index.js
    |-- index.scss
    |-- registerServiceWorker.js
```

Let's go ahead and create these folders and files:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/storybook-intro/file-structure.png" alt="Project structure">
</p>

This is a good checkpoint. Let's go ahead and commit:

```shell
git status
git add .
git commit -m "Create project structure that integrates with SCSS"
```

## Creating Application Theme

`_theme.scss` is an SCSS partial that will be imported by any other component stylesheet that needs to apply the defined theme based on the [Marvel Banking Style Guide]. You can copy and paste the following definitions to your local `_theme.scss` file:

```scss
// src/styles/_theme.scss

// Fonts
$primary-font: "Lato", sans-serif;
$secondary-font: "Open Sans", sans-serif;

// Font Colors
$color-text: #4f6c8d;
$color-text-light: #a3adb4;
$color-text-lighter: #ccd6e1;

$color-text-contrast: #ffffff;
$color-text-contrast-light: #a7b5c6;
$color-text-contrast-lighter: #5f5f63;

// Primary Colors
$blue: #506e8d;
$white: #ffffff;
$green: #6bbfab;
$red: #f96b6b;

// Dark Primary Colors
$dark-blue: #33475c;
$dark-white: #f0f0f0;
$dark-green: #5ba392;
$dark-red: #de5e5e;

// Auxiliary Colors
$bright-blue: #5db8eb;
$opaque-blue: #4770d6;
$opaque-yellow: #f3c416;
$opaque-red: #dd765f;
```

Since this is a significant checkpoint, let's commit again:

```shell
git status
git add .
git commit -m "Create app theme variables"
```

It's time to start creating components!

## Creating A Reusable Button Using React

To take see the benefits that Storybook gives us, we need to have components to visualize. Right now, we have none.

Recall that the Marvel UI Kit offers us the definition of a button that has three presentations:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/storybook-intro/ui-button.png" alt="UI Button with three different presentations expressed through styling">
</p>

These presentations can be summarized as follows:

- Active State (Fill): The button is green, pops up, and has white text.
- Active State (No-Fill): The button shares the background of the parent container, and it has blue color for text and border.
- Disabled State: The button looks suppressed and has a light blue color for text and border.

The button itself has two status: active or disabled. The relationship between presentation and state is something that we can express easily with Storybook. First, we need to plan the component. How are we going to approach its engineering?

Let's start with creating the basic structure of the component: a rounded rectangle that contains a label. Create a file named `UIButton` under the `common` folder and populate it with this:

```javascript
// src/features/common/UIButton.js

import React from "react";

const UIButton = props => <div>{props.label}</div>;

export default UIButton;
```

Now that we have a component, we go back to one of the questions that we asked ourselves when creating components:

- How should a prototype of the component be created? Should a blank page with mock data be used?

Traditionally, we would compose the component into a visible element of the UI to preview it. This time, we are going to use Storybook.
