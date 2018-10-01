---
layout: post
title: "Setting Up a Component Library with React and Storybook"
metatitle: "Setting Up a Component Library with React and Storybook"
description: "Learn how to set up a component library with React and Storybook to create modular and reusable components that can be shared across projects."
metadescription: "Building the foundation of a component library with React and Storybook. Learn how to create reusable design components that can be shared across projects."
date: 2018-09-11 8:30
category: Technical Guide, Auth0 Engineering, React
auth0_aside: true
design: 
  bg_color: "#222228"
  image: https://cdn.auth0.com/blog/logos/storybook-react.png
author:
  name: Dan Arias
  url: http://twitter.com/getDanArias
  mail: dan.arias@auth0.com
  avatar: https://secure.gravatar.com/avatar/d786d3d179f6bd91ce63df956d433588
tags: 
  - react
  - storybook
  - frontend
  - component
  - ui
  - ux
  - engineering
  - component-library
  - reusability
  - banking
related:
  - 2018-09-04-our-engineering-experience-with-react-and-storybook
  - 2018-08-28-react-tutorial-building-and-securing-your-first-app
  - 2018-08-14-react-context-api-managing-state-with-ease
---

[Storybook](https://storybook.js.org/) is a development environment for UI components. It let us browse a component library, view the different states of its components, and interactively develop and test them. Storybook runs outside of our application; therefore, we can develop UI components in isolation without worrying about any project dependencies and requirements.

We are producing a blog post series on how some teams are using React at Auth0. We've shared about [our experience using React and Storybook to better manage components](https://auth0.com/blog/our-engineering-experience-with-react-and-storybook/). In this post, we are going to explore how to organize a basic project, how to implement a style guide to help us achieve consistent branding, and how to build the foundation of a component library to make our components reusable and easy to maintain.

Throughout these series, we are going to be building an online banking web application. In the next posts, we'll learn about using Storybook to easily create component libraries, how to better test React applications, and much more!

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">React is such a good idea that we will spend the rest of the decade continuing to explore its implications and applications.</p>&mdash; Guillermo Rauch (@rauchg) <a href="https://twitter.com/rauchg/status/801005961334943744?ref_src=twsrc%5Etfw">November 22, 2016</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

Let's start.

## Setting Up a React Project

We are going to use [`create-react-app`](https://github.com/facebook/create-react-app) to scaffold easily a new React application.

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

Using a style guide can lead to better application design and development. As explained by the [Marvel Design team](https://blog.marvelapp.com/style-guides-better-design-development/), "during the design phase style guides encourage consistency in the visual identity and help keep the interface system as logical as possible, which makes for a better UX."

What is a style guide anyway? Marvel has a solid answer: "A style guide clearly demonstrates how all interface elements and components are to be visually represented. It’s essentially a master reference for the user interface (UI)."

In this sense, a component library is part of a style guide as it presents our components visually and it is the single source of truth for each component (the master reference). To create a comprehensive style guide, the component library can be accompanied by design documents that define font styles and sizes as well as primary and secondary user interface colors. A Design Team usually creates a "UI Kit" that is handed to developers for implementation. _Shopify_ explains that a [UI kit is an assortment of graphic files](https://www.shopify.com/partners/blog/104547526-the-benefits-of-using-a-ui-kit) that include UI components (buttons, checkboxes, progress bars, etc.) for the purpose of defining and designing the user interface.

{% include tweet_quote.html quote_text="A component library helps you achieve consistent branding as it becomes the single source of truth for every component not only in the application but for all projects in the organization" %}

For the online banking app that we are going to start creating in this blog post, we are going to use a free Banking UI Kit that [Marvel created for Sketch](https://blog.marvelapp.com/free-banking-ui-kit-for-sketch/). To honor their effort, we named the app `marvel-bank`. The UI Kit looks as follows:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/storybook-intro/marvel-banking-ui-kit.png" alt="Marvel Banking UI Kit">
</p>

The UI Kit is designed for iOS, but we are going to adapt it for usage in a responsive web application. To make it easier for developers to understand the UI Kit, I created a design document where we can get the font names, the sizing, the color hex codes, etc: [Marvel Bank Style Guide](https://auth0.github.io/marvel-bank-styleguide/).

In the following excerpt, notice how is much easier to understand the guidelines that a developer would need to follow when creating the components presented in the UI Kit:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/storybook-intro/design-document.png" alt="Style guide design - typography and color palette">
</p>

If we need to implement a green button, we can consult the design document and copy the hex code of `$green`; however, it would be much easier if we could code the elements of the design document into our application in a modular way and reusable way. We have plenty of options to do that:

- We could use CSS variables, also known as [custom properties](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_variables).
- We could use [SCSS](https://sass-lang.com/), [LESS](http://lesscss.org/), or [Stylus](http://stylus-lang.com/) to create style partials and variables.
- We could also opt for using [CSS-in-JS](https://reactjs.org/docs/faq-styling.html) through a library like [`styled-components`](https://www.styled-components.com/).

Our Team currently has been using a combination of Stylus and [CSS Modules](https://github.com/css-modules/css-modules) to manage the styling of the projects. React has unified the three layers of front-end development (HTML, CSS, and JavaScript) under one layer powered by JavaScript, JSX, and CSS-in-JS. For this post, however, we are going to rely on SCSS to create the modular components to minimize the development overhead.

> **Feedback:** Would you like to learn how we are using `styled-components` to create a modern component library? Let us know in the comments below and we could make it part of this React series.

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Oh man and a <a href="https://twitter.com/storybookjs?ref_src=twsrc%5Etfw">@storybookjs</a> shoutout as well! Storybook has been indispensable for our component library.</p>&mdash; Maja Wichrowska (@majapw) <a href="https://twitter.com/majapw/status/997143616865828865?ref_src=twsrc%5Etfw">May 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## Adding SCSS to `create-react-app`

We can [add `SCSS` support to `create-react-app`](https://techcookbook.com/react/use-scss-with-create-react-app) without having to `eject` to a custom setup. We need to install a few packages to help us achieve that goal:

NPM:

```shell
npm install --save node-sass-chokidar npm-run-all
```

[`node-sass-chokidar`](https://www.npmjs.com/package/node-sass-chokidar) is a thin wrapper around the node-sass executable to use [chokidar](https://github.com/paulmillr/chokidar) instead of [Gaze](https://github.com/shama/gaze) when watching files.

[`npm-run-all`](https://www.npmjs.com/package/npm-run-all) is a CLI tool to run multiple npm-scripts in parallel or sequentially.

Next, we need to modify our `package.json` `"scripts"` property to watch and process `.scss` files into `.css` ones:

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

I personally like to use [`gitignore.io`](https://www.gitignore.io/) to generate the content of my `.gitignore` file automatically. The categories that I choose to create the file are WebStorm, IntelliJ, OSX, Windows, and Visual Studio Code, since they are the tools and platforms that are most commonly used. You may head to that site, enter those categories, and copy/paste the output at the end of the `.gitignore` file present in our project.

Since our `.css` files are built automatically under our project configuration, we do not need to include them in version control. In the `.gitignore` file add the following rule at the end:

```text
// .gitignore

// ...

src/**/*.css
```

This rule ensures that none of the `.css` files under the `src` folder are tracked by `git`.

With a comprehensive `.gitignore` file in place, let's initialize a `git` repository (make sure that `marvel-app` directory is the current working directory):

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

We used a shortcut here to add all the files that were being untracked using the `.` after the `add` command.

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
- src
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

For the initial phase of this project, we only need the `features` and the `styles` folder. The `features` folder holds our components in a categorized manner. We define features of our applications, such as `search` or `reports` in its subfolders, and create components that power and build that feature. What Storybook will help us solve is the event where features share components. Instead of duplicating the code across features, the feature will implement the component from a central source: our component library. As the name suggests, these common components go into a `common` feature folder.

This is the project structure we want for the `src` folder in this phase of the project:

```shell
- src
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

{% include tweet_quote.html quote_text="Instead of duplicating the component code across features in your project, implement the component from a central source, like a component library." %}

Let's go ahead and create these folders and files:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/storybook-intro/file-structure.png" alt="Demo banking app file and code structure">
</p>

We'll be using `_theme.scss` in a moment. This is a good checkpoint. Let's go ahead and commit:

```shell
git status
git add .
git commit -m "Create project structure that integrates with SCSS"
```

> Noticed something different this time? Since the `features` folder and its `common` subfolder are currently empty, they were not included as part of the commit bundle by git. We could place an `index.js` file inside `common` that exports all of the components within that folder using what is known as a [barrel export](https://medium.com/@klauskpm/do-a-barrel-export-aa5b79b76b05). For this blog post, we are not going to use that just yet.

## Creating the Application Theme

`_theme.scss` is an SCSS partial that will be imported by any other component stylesheet that needs to apply the defined theme based on the [Marvel Banking Style Guide](https://auth0.github.io/marvel-bank-styleguide/). You can copy and paste the following definitions to your local `_theme.scss` file:

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

// Background Colors
$gray: #bec9d1;

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

To see the benefits and value that Storybook provides us, we need to have components to visualize. Right now, we have none.

Recall that the Marvel UI Kit offers us the definition of a button that has three presentations:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/storybook-intro/ui-button.png" alt="Styling UI buttons - Primary, secondary, tertiary - Active and Disabled state buttons">
</p>

These presentations can be summarized as follows:

- Active State (Fill): The button is green, pops up, and has white text.
- Active State (No-Fill): The button shares the background of the parent container, and it has a blue color for its text and border.
- Disabled State: The button looks suppressed and has a light blue color for its text and border.

The button itself has two status: active or disabled. The relationship between presentation and state is something that we can express easily with Storybook. First, we need to plan the component. How are we going to approach its engineering?

Let's start with creating the basic structure of the component: a rounded rectangle that contains a label. Create a file named `Button.js` under the `common` folder and populate it with this:

```javascript
// src/features/common/Button.js

import React from "react";

const Button = props => <div>{props.label}</div>;

export default Button;
```

Let's quickly commit the creation of the `Button` component:

```shell
git status
git add .
git commit -m "Create Button component"
```

Now that we have a component, we go back to one of the questions that we have been asking ourselves in our team when creating components:

- How should a prototype of the component be presented? Should a blank page with mock data be used?

Traditionally, we would compose the component into a visible element of the UI to preview it. This time, we are going to use Storybook.

### Next Steps

We have built a foundation to help us create our online banking web app with consistent branding by implementing a style guide. In the next part of this series, we are going to learn how to use Storybook to create a sustainable component library that will act as the single source of truth for any component that we create.

At Auth0, different teams are making extensive use of Storybook as a development and design tool. In the final chapter of this series, we are going to explore how we are converging designers and developers into a Design Systems team that is letting us iterate faster and develop better products for our cherished customers.

Feel free to grab the source code of the project we have created so far from the [Marvel Bank App GitHub repo](https://github.com/auth0-blog/marvel-bank-app). The repo is being built gradually and contains commits that cover the work we have done in this blog post and on future posts. To start from scratch, be sure to [create a branch from the first commit](https://stackoverflow.com/questions/2816715/branch-from-a-previous-commit-using-git): `Initial commit`.

Stay tuned for the next part! Feel free to follow [@auth0 on Twitter](https://twitter.com/auth0) or subscribe to our newsletter.

{% include asides/about-auth0.markdown %}
