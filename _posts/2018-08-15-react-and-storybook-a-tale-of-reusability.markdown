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
