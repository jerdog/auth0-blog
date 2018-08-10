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

[Storybook](https://storybook.js.org/) is a development environment for UI components. It let us browse a component library, view the different states of its components, and interactively develop and test them. Storybook runs outside of our application; therefore, we can develop UI components in isolation without worrying about any project dependencies and requirements.

For the scope of this introductory blog post, we are going to demonstrate how to start a new project using Storybook to get familiar with its platform and mechanics. In a follow-up post, we are going to explore deeper into the "Storybook-Driven Development" practices that we have developed at Auth0 within the Customer Success Infrastructure team.

Throughout these series, we are going to create the components of an online banking app called Marvel Bank. Let's start by learning how we can setup Storybook in a brand-new `create-react-app` project.

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Thrilling news: the next chapter for <a href="https://twitter.com/storybookjs?ref_src=twsrc%5Etfw">@storybookjs</a> with full-time maintenance and a bigger team. I&#39;m over the moon!<a href="https://t.co/xWL3Gp5dzI">https://t.co/xWL3Gp5dzI</a> <a href="https://t.co/T1JJfzNGp6">pic.twitter.com/T1JJfzNGp6</a></p>&mdash; shilman (@mshilman) <a href="https://twitter.com/mshilman/status/1021411688749326338?ref_src=twsrc%5Etfw">July 23, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## Setting Up a React Project

We are going to use [`create-react-app`](create-react-app) to scaffold easily a new React application.

We can run `create-react-app` using `npx` which comes bundled with `npm 5.2+` and higher:

```shell
npx create-react-app marvel-bank
cd marvel-bank
```

If you use `npm 5.1` or earlier, you can't use `npx`. Instead, install `create-react-app` globally:

```shell
npm install -g create-react-app
```

And then run:

```shell
create-react-app marvel-bank
cd marvel-bank
```

> Notice that we are not running `npm start`. As mentioned earlier, we don't need to have a running React application to use Storybook.

Open the newly created `marvel-app` folder on your preferred code editor or IDE.

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

## Using a Style Guide for Consistent Branding

Using a style guide can lead to better application design and development. As explain by the [Marvel Design team](https://blog.marvelapp.com/style-guides-better-design-development/), "during the design phase style guides encourage consistency in the visual identity and help keep the interface system as logical as possible, which makes for a better UX."

What is a style guide anyway? Marvel has a solid answer: "A style guide clearly demonstrates how all interface elements and components are to be visually represented. It’s essentially a master reference for the user interface (UI)."

In this sense, a component library is part of a style guide as it presents our components visually and it is the single source of truth for each component (the master reference). To create a comprehensive style guide, the component library can be accompanied the by design documents that define font styles and sizes as well as primary and secondary user interface colors. A Design Team usually creates a "UI Kit" that is handed to developers for implementation. _Shopify_ explains that a [UI kit is an assortment of graphic files](https://www.shopify.com/partners/blog/104547526-the-benefits-of-using-a-ui-kit) that include UI components (buttons, checkboxes, progress bars, etc.) for the purpose of defining and designing the user interface.

{% include tweet_quote.html quote_text="A component library helps you achieve consistent branding as it becomes the single source of truth for every component not only in the application but for all projects in the organization" %}

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

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Oh man and a <a href="https://twitter.com/storybookjs?ref_src=twsrc%5Etfw">@storybookjs</a> shoutout as well! Storybook has been indispensable for our component library.</p>&mdash; Maja Wichrowska (@majapw) <a href="https://twitter.com/majapw/status/997143616865828865?ref_src=twsrc%5Etfw">May 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

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

These scripts allow us to compile `.scss` files into `.css` and to keep watching the `src` folder for changes to the content of existing `.scss` files or the addition of new ones. To complete this task, we need to change the file extension of `App.css` and `index.css` to `.scss`. Depending on our development environment, this can be done in different ways such as renaming the files or refactoring their name or file type.

Storybook will only understand changes made or applied in `.css` files. If we were to make a change in a `.scss` file, we would need to compile it into `.css` for Storybook to be notified of the styling changes.

Normally, such compilation is a build step in our development workflow triggered by running `npm start`. However, I made the promise that we wouldn't have to run our React app in order to use Storybook in this blog post. I am keeping that promise and `npm start` is off-limits. What can we use instead? `watch-css`.

As stated earlier, `watch-css` compiles our SCSS into CSS files using the `build-css` script and it keeps monitoring all the SCSS files present under the `src` directory to detect any changes. Anytime an SCSS file is changed, that file is compiled and Storybook is able to see and apply the styling changes into the component preview (as we'll see soon!).

With this in mind, on the shell run the following command:

```shell
npm run watch-css
```

This is a good point to make our first `git` commit.

## Using `git` Version Control

Our project is in a good place for its first `git` commit. But before we do so, let's ensure that we have everything we need to ignore specified in the `.gitignore` file. If you are using an IDE like WebStorm, for example, there are extra files that the IDE creates that is better to keep out of the `git` repo.

I personally like to use [`gitignore.io`](https://www.gitignore.io/) to create my `.gitignore` file automatically. The categories that I choose to create the file are WebStorm, IntelliJ, OSX, Windows, and Visual Studio Code, since they are the tools and platforms that are most commonly used. You may head to that site, enter those categories, and copy/paste the output into the `.gitignore` file present in our project.

Since our `.css` files are built automatically under our project configuration, we do not need to include them in version control. In the `.gitignore` file add the following rule at the end:

```text
// .gitignore

//...

src/**/*.css
```

This rule ensures that none of the `.css` files under the `src` folder are tracked by `git`.

With a comprehensive `.gitignore` file in place, let's initialize a `git` repository (make sure that `marvel-app` is the current working directory):

```shell
git init
```

Let's run a quick `git status` check to see what files are being untracked:

```shell
git status
```

Let's go ahead and add these files shown to `git` in order to include them in our commit:

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

This all looks good to me ([LGTM™](https://knowyourmeme.com/memes/lgtm)), but before we include any styling or components in our project, we need to create a sound file structure.

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

{% include tweet_quote.html quote_text="Instead of duplicating the component code across features in your project, implement the component from a central source like a component library." %}

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

## Setting Up Storybook

There are two options available to us for adding Storybook: we can install the platform globally or we can start the platform locally. Which installation path should we take?

Our colleague, Matt Machuga, an Engineering Lead in our R&D Team, suggests that "CLI tools that are not project specific are good candidates for global installation." In practice, Matt tries to "scope everything else into local. So the version being used is kept recorded and installed via `npm install`."

We have different options for installing Storybook: a global install, an install using `npx`, and a local manual install. I'll explore those options with you and let you decide which one is right for you. However, I do recommend that you walk with me through the local manual install as it let us learn a great deal about how Storybook works.

### Install `@storybook/react` Globally

To install and run Storybook from your shell follow these quick steps:

- In the shell, make your project directory your current working directory:

```shell
cd my-project-directory
```

- Next, install `@storybook/cli` globally:

```shell
npm i -g @storybook/cli
```

> Above, we used a few `npm` argument shortcuts. `i` stands for `install` and `-g` stands for the flag `--global`.

- Restart your shell. You can do that by closing the existing window or tab and opening a new one.

- Finally, run the `getstorybook` command to have the Storybook CLI scaffold all the necessary files and folders to work with Storybook in your project. These include the `.storybook` folder under the root project and the `stories` folder under the `src` folder.

Let's see how this can be done without a global install using `npx`.

### Install `@storybook/react` Using `npx`

We can emulate the same behavior of the global installation of Storybook but without the actual global installation. If you have `npm >= 5.2` installed in your system, you have `npx` available!

- In the shell, make your project directory your current working directory:

```shell
cd my-project-directory
```

- Run the `getstorybook` command using `npx`:

```shell
npx @storybook/cli getstorybook
```

The Storybook CLI will run and create all the necessary files and folder in your project just as in the previous section. This method is the fastest way to get up and running!

Now, let's explore what the Storybook creators call the "slow start", the local manual installation of Storybook. Through that process, we'll learn how the folders and files installed by the Storybook CLI work.

{% include tweet_quote.html quote_text="npx lets us run one-off invocations of CLI tools available in the npm registry without local installation." %}

### Install @storybook/react manually

In the shell, let's run the following command:

npm:

```shell
npm i -D @storybook/react
```

> Another `npm` argument shortcut. `-D` stands for `--save-dev`.

### Storybook NPM Script

To run Storybook, we need to add the following `script` to `package.json`:

```json
"storybook": "start-storybook -p 9001 -c .storybook"
```

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
    "storybook": "start-storybook -p 9001 -c .storybook",
    "start-react": "react-scripts start",
    "start": "npm-run-all -p watch-css start-react",
    "build-react": "react-scripts build",
    "build": "npm-run-all -s build-css build-react",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  },
  "devDependencies": {
    "@storybook/react": "^3.4.8"
  }
}
```

### Create A Storybook Configuration File

[As explained in the Storybook docs](https://storybook.js.org/basics/guide-react/), the platform is flexible and can be configured in different ways. We can control that configuration through a config directory. In our `storybook` `npm` script, we added a `-c` option followed by `.storybook` which tells Storybook to look for configuration options in the `.storybook` hidden folder.

Having our project root folder as the current working directory, let's go ahead and create that folder:

macOS/Linux/Windows:

```shell
mkdir .storybook
```

Next, we need to create a `config.js` file inside `.storybook` to hold the configuration. Let's start it with the following code:

```javascript
import { configure } from "@storybook/react";

function loadStories() {
  require("../stories/index.js");
  // You can require as many stories as you need.
}

configure(loadStories, module);
```

Storybook works in a similar way to testing tools. The `config.js` loads the `configure` method, which takes as argument a function called `loadStories` and a `module`. `loadStories` will have **stories** defined on its body. A **story** is a single state of a component. We want to write a story for each state a component will have.

We are going to write and load the stories from `stories/index.js` which will be under the root folder. Let's create such folder and file to write our first story.

> `.storybook` is used solely for configuration files. Do not put your `stories` folder inside this hidden folder.

### Writing a Story in Storybook

Open the recently created `index.js` under `.storybook/stories` and populate it with the following code:

```javascript
// .storybook/stories/index.js

import React from "react";
import { storiesOf } from "@storybook/react";
```

So far, there's not much going on. We import `React` and a `storiesOf` that will help us create stories of a component of our choice. We need that component. Let's import `UIButton` here:

```javascript
// .storybook/stories/index.js

import React from "react";
import { storiesOf } from "@storybook/react";
import UIButton from "../../src/features/common/UIButton";
```

Storybook has a declarative language, what we are going to do next is to tell it that we want stories of `UIButton`:

```javascript
// .storybook/stories/index.js

import React from "react";
import { storiesOf } from "@storybook/react";
import UIButton from "../../src/features/common/UIButton";

storiesOf("UIButton", module);
```

If we were thinking in terms of an actual book, this is the book's binding and cover. We need to fill it with pages full of stories. We do that declarative too using the `add` method:

```javascript
// .storybook/stories/index.js

import React from "react";
import { storiesOf } from "@storybook/react";
import UIButton from "../../src/features/common/UIButton";

storiesOf("UIButton", module).add("with text", () => (
  <UIButton label={`Continue`} />
));
```

`add` acts like adding a chapter to a book that has a story. We want to give each chapter a title. In this case, we are creating a story titled `with text`. `add` takes as argument the story title and a function that renders the component being staged.

We have the foundation of writing a story. It's time to see if everything is working by running Storybook.

{% include tweet_quote.html quote_text="Similar to testing, Storybook uses declarative language to let us write stories that define the expected presentation and behavior of our components." %}

### Running Storybook

In the shell, run the following command:

npm:

```shell
npm run storybook
```

yarn:

```shell
yarn storybook
```

If everything runs successfully, you will see this message in the shell:

```shell
info Storybook started on => http://localhost:9001/
```

Let's open that URL, [`http://localhost:9001/`](http://localhost:9001/) in the browser. Let is load... and there we have it in its full glory: our own Storybook!

Right now it's pretty basic but this is a great start!

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/storybook-intro/first-storybook.png" alt="A working Storybook">
</p>

This is a good time to make another commit. We are going to create two commits to address the integration of Storybook and the creation of the `UIButton` component:

```shell
git status
git add src/features/
git commit -m "Create UIButton component"
```

```shell
git status
git add package-lock.json package.json .storybook/ stories/
git commit -m "Integrate Storybook"
```

## Integrating CSS with Storybook

To communicate the presentation and state of the `UIButton` component, we need to add styling to it. Under `src/features/common`, let's create `UIButton.scss` and populate it with the following code:

```scss
// src/features/common/UIButton.scss

@import "../../styles/theme";

.UIButton {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;

  height: 40px;
  width: 160px;
  border: 2px solid $blue;
  border-radius: 60px;

  font-family: $primary-font;
  font-size: 16px;
  color: $blue;
  letter-spacing: 1.27px;
  text-align: center;

  text-transform: uppercase;
}
```

Next, let's import that stylesheet into `UIButton.js` and add `UIButton` as a `className` for the `UIButton` component:

```javascript
import React from "react";

import "./UIButton.css";

const UIButton = props => <div className="UIButton">{props.label}</div>;

export default UIButton;
```

Let's save our work. We should see now how Storybook has refreshed the board and shows the updated component now styled:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/storybook-intro/storybook-with-component-with-style.png" alt="Storybook staged a component with styling applied.">
</p>

Just like with `create-react-app`, we can make changes to the structure, content, or style of the component and they will be updated in Storybook. Let's change the background, font color and border of `UIButton` to see this in action:

```scss
// src/features/common/UIButton.scss

@import "../../styles/theme";

.UIButton {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;

  background: $green;

  height: 40px;
  width: 160px;

  border-radius: 60px;

  font-family: $primary-font;
  font-size: 16px;
  color: white;
  letter-spacing: 1.27px;
  text-align: center;

  text-transform: uppercase;
}
```

Let's save our work again and observe the changes:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/storybook-intro/updated-component-in-storybook.png" alt="Update component in Storybook">
</p>

Style changes are well integrated into our workflow. It's truly amazing that we can preview our components this way without having to touch our actual application code. We can develop our component in isolation and then use them in the app whenever we want.

{% include tweet_quote.html quote_text="Just like with `create-react-app`, we can make changes to the structure, content, or style of a React component and it will be updated in Storybook! We can visualize our components without running our app." %}

As discussed before, `UIButton` has three different presentations. The best way to organize that would be through CSS classes and props.

We want the `UIButton` component to know:

- Its active state: `active` / `disables`
- Its style state: `fill` / `no-fill`

Let's update `UIButton.scss` with classes that represnet these states:

```scss
// src/features/common/UIButton.scss

@import "../../styles/theme";

.UIButton {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;

  height: 40px;
  width: 160px;

  border-radius: 60px;

  font-family: $primary-font;
  font-size: 16px;

  letter-spacing: 1.27px;
  text-align: center;

  text-transform: uppercase;
}

.active {
}

.disabled {
  border: 2px solid $color-text-lighter;

  color: $color-text-lighter;
  letter-spacing: 1.64px;
}

.fill {
  background: $green;
  box-shadow: 0 6px 8px 0 rgba(103, 194, 172, 0.5);

  color: $white;
  letter-spacing: 1.61px;
}

.no-fill {
  border: 2px solid $blue;

  color: $blue;
  letter-spacing: 1.27px;
}
```

Let's briefly review what we are doing here since it's important how this impacts component staging in Storybook:

- `.UIButton` has all the style properties share by all instances of `UIButton`.
- `.active` is not on use right now but it could be used to provide unique properties to an active button.
- `.disabled` has the style properties for any instance of `UIButton` that becomes disabled.
- `fill` and `no-fill` apply distinct styling to an active button.

We need to integrate this style logic into our component logic as follows:

```javascript
// src/features/common/UIButton.js

import React from "react";

import "./UIButton.css";

const UIButton = props => (
  <div
    className={`UIButton ${
      props.active
        ? props.fill
          ? `active fill`
          : `active no-fill`
        : `disabled`
    }`}
  >
    {props.label}
  </div>
);

export default UIButton;
```

Using a combination of ternary operators we process the state of the button. If `props.active` is true, we check if `props.fill` is true or not and apply the corresponding class to the component. If `props.active` is false, there are no extra decisions to make, we apply the default `disabled` class.

To see this in action, let's create new stories in our Storybook!

## Creating Multiple Stories for a Component in Storybook

Let's head to `stories/index.js` and remove the current story that we have defined there, `with text` as it isn't telling too much of a story:

```
// stories/index.js

import React from "react";
import { storiesOf } from "@storybook/react";
import UIButton from "../src/features/common/UIButton";

storiesOf("UIButton", module);
```

Next, let's add three new stories that clearly define the three presentations that we want `UIButton` to have depending on different state flags:

```javascript
// stories/index.js

import React from "react";
import { storiesOf } from "@storybook/react";
import UIButton from "../src/features/common/UIButton";

storiesOf("UIButton", module)
  .add("active with fill", () => (
    <UIButton label={`continue`} fill={true} active={true} />
  ))
  .add("active with no fill", () => (
    <UIButton label={`sign up`} fill={false} active={true} />
  ))
  .add("disabled", () => <UIButton label={`continue`} active={false} />);
```

Let's save our work and we'll see Storybook update to show the three new stories under `UIButton`:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/storybook-intro/storybook-component-with-three-stories.png" alt="Storybook showing three stories for a component">
</p>

Each story renders an isolated instance of `UIButton` whose presentation depends on the `props` values that are passed to it. This is where we can see the value proposition of Storybook at play.

Without Storybook, we would need to add `UIButton` in our app somewhere and test its rendering by passing it different props. We would then need to remove the component. What if we need to change the structure or style of the component? We'd need to start the process all over again.

With Storybook, a component and all of its different states can be staged in a centralized location. What we see in Storybook will be seen in all the areas of the application that use the component, giving us confidence that we'll preserve presentational consistency throughout our application.

Let's click around the different stories and see how the component changes on each. The UI Kit graphic definition of the user interface button is not alive in code.

UI Button active with fill:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/storybook-intro/ui-button-active-fill.png" alt="UI Button active with fill">
</p>

UI Button active with no fill:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/storybook-intro/ui-button-active-no-fill.png" alt="UI Button active with no fill">
</p>

UI Button disabled:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/storybook-intro/ui-button-disabled.png" alt="UI Button disabled">
</p>

We have a solid `UIButton` component that communicates its state clearly through its presentation. Let's go ahead and commit these changes:

```shell
git status
git add .
git commit -m "Add three states to UIButton component"
```

### Next Steps

We have just barely scratched the surface of what can be done with Storybook! There's a lot more we can do with this amazing tool to make the creation, testing, and documentation of React components much faster. Storybook is also a highly configurable tool that relies on add-ons to boost its offerings.

In the next posts of these series, we are going to create more components for the Marvel Bank app as we explore how to integrate Storybook into our development workflow and team culture. Afterward, we are going to be learning how we can use a cool feature called **Storyshots** to make the testing of React components easier.

At Auth0, different teams are making extensive use of Storybook as a development and design tool. In the final chapter of this series, we are going to explore how we are converging designers and developers into a Design Systems team that is letting us iterate faster and developer better products for our cherished customers.

Feel free to grab the source code of the project we have created so far from the [Marvel Bank App GitHub repo](https://github.com/auth0/marvel-bank-app).

{% include asides/about-auth0.markdown %}
