---
layout: post
title: "Validate User Emails Fast using Kickbox and Auth0 Hooks"
description: "Learn how to create Angular applications secured by Auth0 using StackBlitz, an online cloud IDE for Angular and React web applications powered by Visual Studio Code."
date: 2018-08-30 8:30
category: Auth0-based Tutorial, Authentication, Cross-Marketing
design: 
  bg_color: "#1A1A1A"
  image: https://cdn.auth0.com/blog/angular/logo3.png
author:
  name: Joel Lord
  url: https://twitter.com/joel__lord
  mail: joel.lord@auth0.com
  avatar: https://secure.gravatar.com/avatar/ec1016a9a63da65998b75f35b6ceb3bb
tags: 
  - authentication
  - hooks
  - email
  - verification
  - email-verification
  - webtask
  - nodejs
  - frontend
  - javascript
  - kickbox
  - customization
  - universal-login
  - registration
related:
  - 
---

When building an application, it is critical to ensure that the users who are signing up for services or products are real users. By using Auth0, email verification services are provided out of the box; however, wouldn't it be better if verification emails didn't have to be sent at all? By using a combination of [Kickbox](https://kickbox.com/) and [Auth0 Hooks](https://auth0.com/docs/hooks), user email address can be validated without sending any emails.

## How does Kickbox work?

Kickbox is an [email technology company](https://kickbox.com/about) with products that help verify email addresses and prevent fake sign-ups to your applications.

To create your free account, we can visit the [Kickbox sign-up page](https://app.kickbox.com/signup).

## Creating an Auth0 Application

First off, let's start by building a [regular application in Auth0](https://auth0.com/docs/applications). We can use any existing application or we can create a new one. To do so, let's go to the _Applications_ section and click on **Create Application**. For the purpose of this example, we can pick a Single Page Application as the application type. We can then pick the framework of our choice on the next screen as we will only use the Auth0 Management Dashboard to do all of our configurations.

Since we don’t want the email verification sent any more, we can deactivate easily. On the left sidebar, let's click on **Emails** first and then on **Templates** to get to the [Email Templates section](https://manage.auth0.com/#/emails). In this section, The **Verification Email** template should be selected by default. Find the **Status** toggle and turn it off.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/kickbox-auth0/auth0-email-templates.png" alt="Auth0 Email Templates">
</p>

We are now ready to add an Auth0 hook and integrate Kickbox.

## Building an Auth Hook

[Auth0 Hooks](https://auth0.com/docs/hooks) allow us to customize the behavior of Auth0 using [Node.js](https://nodejs.org/en/) code that is executed against extensibility points (which are comparable to webhooks that come with a server). Hooks give us modularity when configuring our Auth0 implementation and extend the functionality of base Auth0 features.

You can edit the hooks in the management dashboard using the [Webtask editor](https://webtask.io/docs/editor).

On the left sidebar in the Auth0 Dashboard, let's click on [Hooks](https://manage.auth0.com/#/hooks). In the Hooks page, we will find different places where we can add a hook in the user login or sign-up life cycle. The one we are interested in is the **Pre User Registration** hook. We want to verify a user email before they register into our system.

Kickbox can provide us with a [Sendex score](https://docs.kickbox.com/docs/the-sendex) when we verify an email address. Based on this score, we will either create the user or send them an error at the moment of the sign-up.

To create a new hook let's do the following:

- Click on the **Create New Hook** button.

- Give the hook a name.

- Select "Pre User Registration" from the "Hook" drop-down field.

- Click on the "Create" button.

Our newly created Auth0 hook will be shown under the "Pre User Registration" with a green dot before its name. To edit it, let's click on the pencil button in the row below it.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/kickbox-auth0/newly-created-auth0-hook.png" alt="Newly created Auth0 Hook with an active indicator">
</p>

We are taken to the Webtask editor. In here, we will be able to edit the code that gets executed every time a new user registers. We can add new metadata to our users and return this or we can throw an error which will prevent the user registration.

We can remove all the default code that is there to start with an empty hook. The first thing that we will need is the function that will be executed upon a new user registration:

```javascript
module.exports = function(user, context, cb) {};
```

When our code execution is done, it will need to return the `cb` function with either an error or a valid response. So let’s add this:

```javascript
module.exports = function(user, context, cb) {
  var response = {};
  response.user = user;

  cb(null, response);
};
```

In order to connect to the Kickbox service, we will need to do a `GET` request using the Node `request` module. We can add that at the top of our code just before the `module.exports`:

```javascript
var request = require("request");
module.exports = function(user, context, callback) {
  // ...
};
```

The [Kickbox email verification API](https://docs.kickbox.com/v2.0/reference) is fairly simple to use. We simply make a GET call to the following endpoint:

`https://api.kickbox.com/v2/verify?email={{email}}&apikey={{API_KEY}}`

In the URL above, `{{email}}` is the address that we want to verify and `{{API_KEY}}` is our [Kickbox API key](https://docs.kickbox.com/docs/using-the-api).

Let’s build the `GET` request:

```javascript
var request = require("request");

module.exports = function(user, context, callback) {
  var response = {};
  response.user = user;
  const API_KEY = YOUR_KICKBOX_API_KEY;
  const email = user.email;
  const url = `https://api.kickbox.com/v2/verify?timeout=6000&email=${email}&apikey=${API_KEY}`;

  request.get({ url }, function(err, resp, body) {
    // Process the response and send back the cb
    cb(null, response);
  });
};
```

Now we only need to check the Sendex code to decide whether we accept the user registration or not:

```javascript
var request = require("request");

module.exports = function(user, context, callback) {
  var response = {};
  response.user = user;
  const API_KEY = YOUR_KICKBOX_API_KEY;
  const email = user.email;
  const url = `https://api.kickbox.com/v2/verify?timeout=6000&email=${email}&apikey=${API_KEY}`;

  request.get({ url }, function(err, resp, body) {
    body = JSON.parse(body);

    // Add the user_metadata property if not present already
    if (!response.user.user_metadata) response.user.user_metadata = {};
    // Add the sendex code as part of the user metadata
    response.user.user_metadata.sendex = body.sendex;

    if (body.sendex > 0.75) {
      console.log("This email is save, run the callback");
      callback(null, response);
    } else {
      console.log("Probably not a valid email address");
      callback("Access Denied by Kickbox server. (" + body.sendex + ")");
    }
  });
};
```

In this case, we are looking at the Sendex score only. If the Sendex score is over `0.75`, we assume that the email address is safe and good to go. There are various other checks that we could make to decide if we want to accept the user not.

Using this code will send back a generic error message to the end-user on the [Auth0 Universal Login page](https://auth0.com/docs/hosted-pages/login) when the user is trying to sign up with an invalid email.
