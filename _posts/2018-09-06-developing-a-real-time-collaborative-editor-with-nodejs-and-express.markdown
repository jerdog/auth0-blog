---
layout: post
title: "Developing a Real-Time, Collaborative Editor with Node.js and Express"
metatitle: "Developing a Real-Time, Collaborative Editor with Node.js"
description: "Learn how to develop a real-time web app that enables multiple users to work on the same document with ease."
metadescription: "Learn how to use Node.js and Express to develop a real-time web app that enables multiple users to work on the same document with ease."
date: 2018-09-06 07:48
category: Technical Guide, First Application, Pusher
post_length: 2
author:
  name: Godson
  url: https://twitter.com/devGson
  mail: "godsonobielum@yahoo.com"
  avatar: https://pbs.twimg.com/profile_images/1022409389272129536/YObVPEqr.jpg
design:
  bg_color: "#333333"
  image: https://cdn.auth0.com/blog/logos/node.png
tags:
- node.js
- express
- pusher
- auth0
- real-time
- collaborative
- editor
- web
- app
- web-app
- web-application
related:
- developing-real-time-web-applications-with-server-sent-events
- real-time-charts-using-angular-d3-and-socket-io
---

**TL;DR:** In this article, you will learn how to build a real-time, collaborative note editor application that enables multiple users to work on the same document simultaneously. To create this app, you will use tools like Node.js, Express, Pusher, and Auth0. If needed, [you can find the code of the final app in this GitHub repository](https://github.com/auth0-blog/collaborative-real-time-editor).

## What Will you Build?

As you can guess by the title of the article, you are going to build a real-time, web editor that enables different users to work on the same document from different places collaboratively. That is, you and one or more friends (for example) will be able to type and edit the same document at the same time. Just like if you were editing a Google Docs word but built by yourself. How cool is that?

Anyway, to build this web application, you will use tools like:

- [Node.js](https://nodejs.org/en/): a runtime engine based on Chrome's V8 processor that enables you to use the JavaScript programming language on your computer and on servers (i.e., outside web browsers).
- [Express](https://expressjs.com/): a fast and minimalist web framework for Node.js.
- [Pusher](https://pusher.com/): a hosted service that helps you build real-time apps with less code.
- [Auth0](https://auth0.com/): a universal authentication & authorization platform for web, mobile, and legacy applications.

Will you have to pay for these services and tools? No, you won't. Node.js and Express are open-source projects that you can use for free to build whatever you want to build. Pusher and Auth0 do have paid tiers where you can use advanced features to build better apps, but they also include some cool free tiers. You will be able to build amazing apps in no time with these services without paying a dime for quite a while. Unless your app becomes too popular, but that's hardly a problem, right?

## Pre-Requisites

To follow this article along, you will need some basic knowledge of JavaScript and related technologies like HTML and CSS. Also, you will need to have installed in your machine Node.js and NPM. If you don't have, you can [go to the official download page of Node.js and follow the instructions](https://nodejs.org/en/download/) there. NPM comes bundled into Node.js.

After installing Node.js and NPM, open a terminal and issue the following commands to guarantee you have everything set up:

```bash
node -v
# output will be similar to v9.11.1

npm -v
# output will be similar to 5.6.0
```

## What is Pusher?

Pusher is a hosted service that makes it very easy to add real-time functionality to a web or mobile application. This service provides libraries that help to seamlessly build chat applications, real-time charts, collaborative editors, etc. You can find more information about Pusher and the features it provides [on their website](https://www.pusher.com).

In this article, you will use [a feature provided by Pusher called TextSync](https://docs.pusher.com/textsync) to build your application. Basically, TextSync provides the functionality that enables users work on a document in real-time with others, it will also handle authorization and a few other things you will see as you build your application.

## Scaffolding your Project

First, you will have to create a directory for your application. So, on a terminal, move to the directory where you like to save your work and run the following commands:

```bash
# create a directory for your project
mkdir collaborative-real-time-editor

# move into it
cd collaborative-real-time-editor
```

The app that you will build won't be complex. As such, the directory structure of this app will be fairly simple as well:

```bash
+ assets
 - css
 - main.css
+ js
 - app.js
 - views
 - editor.pug
 - index.pug
 - layout.pug
+ package.json
+ server.js
+ variables.env
```

The `assets` directory will contain all your stylesheets (CSS rules) and front-end JavaScript code. The `views` directory will contain all your front-end views (HTML code). The `server.js` file will contain the server-side code to run your app. The `variables.env` file will hold some global environment variables (some keys that you will need to run your app). And the `.pug` files are your HTML files. As you go on, you will see how these files fit into your application.

> **Note:** If you don't know why you are going to use `.pug` files, don't worry. Pug is a template engine for Node.js that facilitates the process of writing HTML files. Using Pug is really easy, as you will see.

Next, you can use NPM to initialize your directory as a Node.js project by running:

```bash
# from your project's directory
npm init -y
```

### Installing Dependencies

With the directory properly initiated, you will have to install the necessary packages that will help you build certain parts of your application. To do so, run the commands below in your terminal:

```bash
npm install body-parser dotenv express express-session passport passport-auth0 pug textsync-server-node
```

The following is a run down of what some of the packages will help you achieve:

1. You will use the `passport` and `passport-auth0` packages to implement user authentication with Auth0.
2. You will use `textsync-server-node` to handle the Pusher TextSync authorization.
3. You will use `dotenv` to load environment variables from the `variables.env` file into Node.js's `process.env` object.
4. You will use `pug` for your server-side templates.

Don't worry if all these don't make that much sense now. As you go through the article, you will learn how to use them.

## Setting up a Pusher TextSync Account
Previously, i stated we'll be using a library called TextSync to build our application, so let's go ahead and create an account to enable us make use of TextSync. Go to this [link](https://pusher.com/textsync) and sign up.

Now we've signed up, let's create a new TextSync instance, this will enable us to get a `secret_key` and an `instance_locator` which we'll make use of later on. Click on `Create new instance` in the TextSync tab and provide a name for the instance, give it anything you want.

With that covered, copy the `instance_locator` and `secret_key`. These are important as
they'll enable us to communicate with the TextSync instance we created previously.

****** Image of the instance_locator and secret_key key ******

## Setting up Views with Pug
Next up, we'll create our server-side templates with Pug, Pug provides a lot of features 
we won't be exploring in this article, more information about these features can be found [here](https://pugjs.org/). We'll create three Pug files :

```html
views/layout.pug

doctype html
html(lang="en")
  head
    meta(charset="UTF-8")
    meta(name="viewport" content="width=device-width, initial-scale=1.0")
    meta(http-equiv="X-UA-Compatible" content="ie=edge")
    link(rel="stylesheet" href="/css/main.css")
    title Real Time Editor
  body.main
    block content
```

### Creating Unique URL's

```html
views/index.pug

extends layout

block content
  nav.login_nav
    button.login_button= user.displayName
  form(action="/note" method="POST")
    input(type="text" name="title" placeholder="TITLE OF NEW NOTE")
    input(type="hidden" name="slug")
    button(class="create_note") CREATE
  
  script.
    var slug = Math.random().toString(36).slice(2);
    document.querySelector('input[name="slug"]').value = slug
```

The file above contains a form which will be used to create a new document and an editing session. Importantly, it contains a variable `slug` which holds a random, unique alphanumeric string. When the form is submitted, the `slug` will be sent over to the server-side, and used to create a unique link/URL, other users will be able to join the editing session through that link only. This may not be clear now but later on we'll see exactly how this is done.

Finally,

```html
views/editor.pug

extends layout

block content
  .sidebar
    h3 Collaborators
    .active_users
      ul
  .main_content
    #text_editor
  script(type="text/javascript").
    var user = "#{user.displayName}";
  script(type="text/javascript" src="https://static.pusherplatform.com/textsync/us1/textsync.js")
  script(type="text/javascript" src="/js/app.js")
```

Lastly, let's create a file `main.css` which will contain all our styles.

```css
assets/css/main.css
  
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  font-family: sans-serif;
  font-weight: 300;
  font-size: .95rem
}

h3 {
  font-size: 1.5rem;
  color:white;
  text-align: center;
  margin-bottom: 3px;
}

nav {
  height:8vh;
  flex-basis:100%;
  background: linear-gradient(325deg, rgb(39, 107, 130) 0px, rgb(49, 84, 129) 100%);
}

button {
  cursor: pointer;
  border-radius:5px;
  color: white;
}

form {
  display:flex;
  margin:20px;
  padding:20px;
  justify-content: center;
  flex-basis:100%;
}

form input {
  width: 50%;
  border-width: 0 0 1px 0;
  padding: 10px;
  border-color: rgb(39, 107, 130);
  margin-right:20px;
}

.main {
  display:flex;
  flex-wrap:wrap;
}

.sidebar {
  display:flex;
  flex-direction: column;
  padding:10px;
  flex-basis:17%;
  height: 100vh;
  align-content: center;
  background: linear-gradient(325deg, rgb(39, 107, 130) 0px, rgb(49, 84, 129) 100%);
}

.active_users li {
  list-style-type: none;
  background: white;
  border-radius: 5px;
  padding: 10px;
  margin: 10px;
}

.main_content {
  padding: 30px;
  flex-basis: 83%;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}

#text_editor {
  margin: 10px 0 40px 0;
  height: 80vh;
  flex-basis: 100%;
}

.login_nav {
  display:flex;
  justify-content:center;
}

.login_button {
  width: 6rem;
  height:2rem;
  border: 1px;
  background: red;
  align-self: center;
}

.create_note {
  width: 6rem;
  border: 1px solid white;
  background: linear-gradient(325deg, rgb(39, 107, 130) 0px, rgb(49, 84, 129) 100%);
}

```

### Pusher TextSync Editor Configuration
Let's set up our TextSync editor, create a file `app.js` and paste in the following code,
We'll go through the code in detail right after.

```js
assets/js/app.js

const textSyncInstance = new TextSync({
  instanceLocator: "YOUR PUSHER INSTANCE_LOCATOR"
});

//Creates an instance of the TextSync editor
const editor = textSyncInstance.createEditor({
  docId: document.URL.slice(document.URL.lastIndexOf("/") + 1),
  element: "#text_editor",
  authEndpoint: "http://localhost:3000/textsync/tokens",
  userName: user,
  cursorLabelsAlwaysOn: true,

  onCollaboratorsJoined: users => {
    const activeUsers = document.querySelector(".active_users ul");
    users.forEach(value => {
      activeUsers.insertAdjacentHTML(
        "beforeend",
        `<li id='${value.siteId}'>${value.name}</li>`
      );
    });
  },

  onCollaboratorsLeft: users => {
    const activeUsers = document.querySelectorAll(".active_users ul li");
    users.forEach(value => {
      activeUsers.forEach(element => {
        if (element.id === value.siteId) element.remove();
      });
    });
  }
});
```

In the code above, replace the `instanceLocator` property with the one gotten after creating the TextSync instance. We'll use that to connect and interact with the TextSync instance we created previously. So, the next step is to create the TextSync editor, a few Configuration arguments are required to set this up. Specifically, the TextSync editor requires 3 arguments :

* docId : This is the ID of the document that will be loaded into the editor on creation. Previously, we created a slug which we use to create a unique URL. The function `document.URL.slice(document.URL.lastIndexOf("/") + 1)` gets the part of the URL after the last slash, e.g from the URL `localhost:3000/note/yajdzfr2w78`, it extracts `yajdzfr2w78` and that gets loaded as the `docId` into the editor on creation. This is important because the `docId` enables other users join the same editing session. Essentially, all TextSync editors with the same `docId` belong to the same editing session.

* element :  The DOM element that will contain the text editor. This may be either a CSS selector or a reference to the element object.

* authEndpoint : The URL of the endpoint on our server where the TextSync library can obtain authorization tokens. We'll see exactly how this works later on.

There are also optional configuration arguments we use in our application :

* userName : A name to be displayed to other collaborators working in the same editing session.

* onCollaboratorsJoined : This function is called whenever a user joins this editing session,we'll add this user to the list of active users and update the UI accordingly.

* onCollaboratorsLeft : This function is called whenever a user leaves the editing session,we'll remove the user from the list of active users and update the UI accordingly.

For a broader list of the TextSync configuration properties, visit the [docs](https://docs.pusher.com/textsync/reference/js)

## Setting up Routes with Express
In this section, we'll be focusing on the server-side of our application, let's dive in.

Let's create a file `variables.env` to hold our environmental variables. Copy both the `instance_locator` and `secret_key` gotten from the TextSync instance and paste it there.

```js
variables.env

INSTANCE_LOCATOR={YOUR TEXTSYNC INSTANCE_LOCATOR}
KEY={YOUR TEXTSYNC SECRET_KEY}
```

Create another file `server.js`, this will contain all our server-side code. The file will be a bit long, so for easier comprehension we'll break it down and explain it bit by bit. First let's import the packages we previously installed and also add some basic configuration.

```js
server.js

const path = require('path');

const bodyParser = require('body-parser');
const express = require('express');
const session = require('express-session');
const passport = require('passport');
const Auth0Strategy = require('passport-auth0');
const TextSync = require('textsync-server-node');

//Load the enviromental variables into process.env
require("dotenv").config({ path: "variables.env" });

const app = express();
app.set('view engine', 'pug');

//Allow access from a different origin
app.use((req, res, next) => {
  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "Content-Type, Authorization");
  next();
});
app.use(express.static(path.join(__dirname, 'assets')));
app.use(bodyParser.urlencoded({extended: false}));
app.use(bodyParser.json());

//We'll make use of sessions to keep track of logged in Users
app.use(session({
  secret: "--ENTER CUSTOM SESSION SECRET--",
  resave: false,
  saveUninitialized: false
}));

const textSync = new TextSync({
  instanceLocator: process.env.INSTANCE_LOCATOR,
  key: process.env.KEY
});

  ...
```

In the code above, the `key` config property is the secret key gotten from the previously
created TextSync instance. 

Now, let's set up some routes.

```js
server.js
  
  ...

function loggedIn(req, res, next) {
  req.session.user ? next() : res.redirect("/login");
}

app.get('/', loggedIn, (req, res) => {
  res.render('index', {user: req.session.user})
});

app.post('/note', loggedIn, (req, res) => {
  const slug = req.body.slug;
  res.redirect(`/note/${slug}`);
});

app.get('/note/:slug', loggedIn, (req, res) => {
  res.render('editor', {user: req.session.user})
});

  ...
```

In the file above, we set up three routes, let's go through them;

1. The `/` route : This displays a form that enables the user to create a new document and editing session.

2. The `/note` route : When the form in `index.pug` is submitted, a POST request is made to this route, which sends along the `slug`, we retrieve that with `req.body.slug` and use the slug to create a new, unique route/URL. The user is redirected to this new route.

3. The `/note/:slug` : This is the new route created and also where the collaboration takes place. The `:slug` in this route represents the unique slug we sent over previously. This route is unique to each editing session and anyone invited to join a particular session does so through this route.

We also have a function `loggedIn`, all that the function does is : 
When a user tries to access any of the routes it checks if the user is already logged in, if the user isn't logged in they are redirected to the `/login` route. That route hasn't been created just yet, we'll do that when we're hooking up our authentication middleware.

### TextSync User Authorization
Remember, when we configured our TextSync editor, we added a property `authEndpoint` that supposedly handles TextSync authorization, what this means is that any user that intends to join an editing session must be authorized through this endpoint, this is done to ensure maximum flexibility while also keeping the document secure, but how exactly is this done?

When a user tries to join an editing session, a POST request is made to this endpoint. The server(ours) then responds with a secure token describing the rights and permissions the user has to the document they are trying to access. The token is signed with a shared secret and so if it get's modified in any way, the token becomes invalid. The TextSync library then sends this token along when making a request to the TextSync server and if all goes well, the user is allowed to access the document with the appropriate rights and permissions exclusive to that user.

Let's implement the route that handles this authorization next;

```js
server.js
  
  ...
  
app.post('/textsync/tokens', (req, res) => {
  //Certain Users can be restricted to either READ or WRITE access on the document
  //to keep this demo simple, all users are granted READ and WRITE access to the document
  const permissionsFn = () => {
    return Promise.resolve([
      TextSync.Permissions.READ,
      TextSync.Permissions.WRITE
    ]);
  };

  //Set authentication token to expire in 20 minutes
  const options = { tokenExpiry: 60 * 20 };

  textSync.authorizeDocument(req.body, permissionsFn, options)
    .then(token => {
      res.json(token);
    });
});

  ...
```
That's all for the TextSync authorization.

> ***Note*** : The TextSync authorization we implemented above was basic, but if you plan on implementing something more complex, visit the [TextSync docs](https://docs.pusher.com/textsync/authorization)

Lastly, we'll set up user authentication, Auth0 handles this seamlessly.

## Authentication with Auth0
### What is Auth0
Auth0 provides authentication and authorization as a service. This means we could easily add authentication to our application without being concerned about the nitty-gritty of security, Auth0 handles all of this for us. Auth0 offers a variety of identity providers eg Social connections(Twitter,facebook,etc..), passwordless and a lot more. These identity providers are simply strategies in which users leverage to log in to our application and become authenticated. We can then hook this up to our application through one of the Auth0 SDK's(or our API). More information about Auth0 can be found [here](www.auth0.com).

### Setting up an Auth0 account
We'll need an Auth0 account to manage authentication. Sign up for a free Auth0 account [here](https://auth0.com/signup). After that, we'll create an Auth0 application, think of this as 
similar to an instance, This will interface with our local application and enable us track and manage users amongst other things. This application also provides a client ID, secret key and a few other required values that'll be useful in setting up authentication.

### Creating an Auth0 application
1. After Signing up, go to the Auth0 Dashboard and click the *New application* button.
2. Name the new app, don't choose any *Application Type*, then click the *Create* button.
3. Go to the *Settings* tab of the newly created application, add `http://localhost:3000/callback` to the `Allowed Callback URLs` field. Finally, copy the `Domain`, `Client ID`, `Client Secret` values and add those to the `variables.env` file.

```js
variables.env

AUTH0_CLIENT_ID={YOUR CLIENT_ID}
AUTH0_DOMAIN={YOUR DOMAIN}
AUTH0_CLIENT_SECRET={YOUR CLIENT_SECRET}
```

4. Click the "Save Changes" button.

Let's set up some social connections. Go to the Auth0 Dashboard, then click on the *Connections* tab and enable some connections. In our application we'll use the Github, Twitter and Google social connections.

That's all, let's go ahead and hook this up to our application.

### Authentication with Passport
Passport is an authentication middleware for Node, it makes use of strategies eg Local strategy for local username and password or social strategies e.g Facebook or Twitter,etc.. In this article we'll make use of the Auth0 strategy using the `passport-auth0` package.

Paste the code below and we'll go into the details after;

```js
server.js
  ...

// This middleware is required to initialize passport
app.use(passport.initialize());

//We use sessions in our application so this middleware is required
app.use(passport.session());

//Serialize the User into the session
passport.serializeUser( (user, done) => {
  done(null, user);
});

passport.deserializeUser( (user, done) => {
  done(null, user);
});

// Passport middleware that initializes the Auth0 strategy
passport.use(new Auth0Strategy({
domain: process.env.AUTH0_DOMAIN,
clientID: process.env.AUTH0_CLIENT_ID,
clientSecret: process.env.AUTH0_CLIENT_SECRET,
callbackURL: 'http://localhost:3000/callback'
}, (accessToken, refreshToken, extraParams, profile, done) => {
// accessToken is useful if we're calling the Auth0 API(not needed in this article)
// profile contains the information of the user
return done(null, profile);
}));

app.get('/login', passport.authenticate('auth0',{
  //The scope parameter determines the user information the server sends
  scope: "openid profile"
}) );

app.get('/callback', passport.authenticate('auth0'), (req, res) => {
  req.session.user = req.user;
  res.redirect('/')
});

//Listen to connections on port 3000
app.listen(process.env.PORT || 3000, () => {
  console.log("Server listening on port 3000.");
});

```

In order to use an authentication strategy, we must must first configure it. So let's configure the Auth0 strategy, we do that using the `passport.use()` function. The Auth0 strategy takes in a several required arguments and a verify callback function we'll use later.

All users are authenticated through the `/login` route, when a user tries to log in they're immediately redirected to an Auth0 page to log in through one of the identity providers. 
If that is successful, Auth0 will redirect the user to the callback route/URL(`http://localhost:3000/callback`) we provided when setting up the Auth0 strategy. This route contains middleware that triggers Passport to authenticate the request.

When Passport authenticates a request, it parses the credentials contained in the request and invokes the verify callback function we configured in our Auth0 strategy with these credentials as arguments--five arguments to be precise. In this article we're only interested in the `profile` argument as that contains the user profile. So we call the `done` function passing along the authenticated user's profile as the second argument, the user profile is attached to the request object and sent to the next middleware. 

We then retrieve the user profile from the request object, store these details in the session and then redirect the user to the `/` route where they can create new editing sessions.

For more information about Passport, how it works and other available strategies, visit the [docs](http://www.passportjs.org/docs/authenticate/).

And that's all for the user authentication, the last thing we'll do is run our app and see how it works. To keep this article short and fairly simple, we'll test our application locally
and in the second part of this article we'll deploy to a hosted service such as heroku.

## Testing our app
In the project directory run :

```bashmdir
$ node server.js
```

With the server up and running, visit `localhost:3000/` in the browser and then log in.

When you're done with that, create a new note, a new editing session will be created and you'll be redirected to a new URL. We're running this locally so in order to test this, copy the URL of the already created editing session, open up a different browser, paste the link, sign in and you can start contributing!.

****** GIF of the application ******

## Conclusion
We've been able to build an application with TextSync and uncover the powerful feature it provides. We went further and added user authentication to our application. In the second part of this article we'll deploy our application to a service such as Heroku and truly collaborate from anywhere in the world!.