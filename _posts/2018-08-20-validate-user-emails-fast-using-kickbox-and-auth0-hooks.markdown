---
layout: post
title: "Validate User Emails Fast using Kickbox and Auth0 Hooks"
description: "Learn how to ensure that only real users are registering to your applications using Kickbox and Auth0 Hooks."
date: 2018-08-30 8:30
category: Auth0-based Tutorial, Authentication, Cross-Marketing
design: 
  bg_color: "#1A1A1A"
  image: https://cdn.auth0.com/blog/auth0-webhooks-announcements/hooks_logo.png
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
  - 2017-02-15-introducing-auth0-hooks
  - 2018-07-19-how-auth0-makes-your-apps-more-secure
  - 2018-02-16-the-6-billion-reason-your-business-needs-advanced-fraud-protection
---

When building an application, it is critical to ensure that the users who are signing up for services or products are real users. By using Auth0, email verification services are provided out of the box; however, wouldn't it be better if verification emails didn't have to be sent at all? By using a combination of [Kickbox](https://kickbox.com/) and [Auth0 Hooks](https://auth0.com/docs/hooks), user email addresses can be validated without sending any emails.

## How does Kickbox work?

Kickbox is an [email technology company](https://kickbox.com/about) with products that help verify email addresses and prevent fake sign-ups applications.

To create a free account, we can visit the [Kickbox sign-up page](https://app.kickbox.com/signup).

{% include tweet_quote.html quote_text="Kickbox ensures we only send email to real users and helps us separate the low-quality addresses from high-value contacts." %}

## Preparing an Auth0 Tenant to Integrate with Kickbox

To demonstrate the integration of Kickbox with Auth0, we are going to use an existing [Auth0 Tenant](https://auth0.com/docs/getting-started/the-basics#account-and-tenants).

> If you haven't already done so, <a href="https://auth0.com/signup" data-amp-replace="CLIENT_ID" data-amp-addparams="anonId=CLIENT_ID(cid-scope-cookie-fallback-name)">sign up for a free Auth0 account here</a>.

Since we don’t want an email verification to be sent any more, we need to deactivate that option in the tenant. On the left sidebar of the Auth0 Dashboard, let's click on **Emails** first and then on **Templates** to get to the [Email Templates section](https://manage.auth0.com/#/emails). In this section, the **Verification Email** template should be selected by default. Find the **Status** toggle and turn it off.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/kickbox-auth0/auth0-email-templates.png" alt="Auth0 Email Templates">
</p>

We are now ready to add an Auth0 hook and integrate Kickbox.

## Kickbox Sendex™ Email Quality

As stated in the Kickbox documentation, Kickbox provides us with a [Sendex value](https://docs.kickbox.com/docs/the-sendex) which is an indicator of the quality of an email address. The existence or syntactical correctness of an email address does not indicate its quality. For example, `john.smith@example.com` can generally be seen as a higher quality email address than `sdfsdfsdf@example.com`.

Kickbox uses a number of algorithms, derived from the millions of email transactions it performs each hour, to determine the overall quality of an email address. These are some of the characteristics Kickbox considers when classifying the quality of an email address:

- Is the email address similar to a known, high-quality, valid email address? The Kickbox platform is always evaluating and learning what patterns good email addresses employ. Its evolving data is used to provide insight into the overall quality of a given address.

- Is the email address domain a commercial domain (example: `acme.com`) or a personal domain (`example: yahoo.com`)?

- Does the email address appear to be associated with a role (example: `postmaster@example.com`) instead of a person (example: `bob.smith@example.com`)?

The Sendex value ranges from a `0` (no quality) to `1` (excellent quality). The value of a good Sendex score depends on the specific purpose of the application. For a transactional email the following criteria may be applied:

- 1.00 - 0.55 = Good

- 0.54 - 0.20 = Fair

- 0.19 - 0.00 = Poor

We are going to integrate the Sendex value with Auth0 through a pre-registration hook. Before a user registration is complete, we are going to obtain the Sendex score of the email address and we'll determine if we either create the user account or send an error at the moment of the sign-up.

## Building an Auth Hook

[Auth0 Hooks](https://auth0.com/docs/hooks) allow us to customize the behavior of Auth0 using [Node.js](https://nodejs.org/en/) code that is executed against extensibility points (which are comparable to webhooks that come with a server). Hooks give us modularity when configuring our Auth0 implementation and extend the functionality of base Auth0 features.

On the left sidebar in the Auth0 Dashboard, let's click on [Hooks](https://manage.auth0.com/#/hooks). In the Hooks page, we will find different places where we can add a hook in the user login or sign-up life cycle. The one we are interested in is the **Pre User Registration** hook. We want to verify a user email before they register into our system.

To create a new hook, let's do the following:

- Click on the **Create New Hook** button.

- Give the hook a name.

- Select "Pre User Registration" from the "Hook" drop-down field.

- Click on the "Create" button.

Our newly created Auth0 hook will be shown under the "Pre User Registration" section with a green dot before its name. To edit it, let's click on the pencil button in the row below it.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/kickbox-auth0/newly-created-auth0-hook.png" alt="Newly created Auth0 Hook with an active indicator">
</p>

We are taken to the [Webtask editor](https://webtask.io/docs/editor) where we can easily edit our hook logic within the Auth0 Dashboard. In here, we will be able to edit the code that gets executed every time a new user registers. We can add new metadata to our users and return this or we can throw an error which will prevent the user registration.

We can remove all the default code that is there to start with an empty hook. The first thing that we will need is the function that will be executed upon a new user registration:

```javascript
module.exports = function(user, context, callback) {};
```

When our code execution is done, it will need to return the `callback` function with either an error or a valid response. So let’s add this:

```javascript
module.exports = function(user, context, callback) {
  const response = {};
  response.user = user;

  callback(null, response);
};
```

In order to connect to the Kickbox service, we will need to do a `GET` request using the Node `request` module. We can add that at the top of our code just before the `module.exports`:

```javascript
const request = require("request");

module.exports = function(user, context, callback) {
  // ...
};
```

The [Kickbox email verification API](https://docs.kickbox.com/v2.0/reference) is fairly simple to use. We simply make a `GET` call to the following endpoint:

```shell
https://api.kickbox.com/v2/verify?email=<email>&apikey=<API_KEY>
```

In the URL above, `<email>` is the address that we want to verify and `<API_KEY>` is our [Kickbox API key](https://docs.kickbox.com/docs/using-the-api).

Let’s build the `GET` request:

```javascript
const request = require("request");

module.exports = function(user, context, callback) {
  const response = {};
  response.user = user;
  const API_KEY = YOUR_KICKBOX_API_KEY;
  const email = user.email;
  const url = `https://api.kickbox.com/v2/verify?timeout=6000&email=${email}&apikey=${API_KEY}`;

  request.get({ url }, function(err, resp, body) {
    // Process the response and send back the callback
    callback(null, response);
  });
};
```

Now we only need to check the Sendex code to decide whether we accept the user registration or not:

```javascript
const request = require("request");

module.exports = function(user, context, callback) {
  const response = {};
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
      console.log("This email is safe, run the callback");
      callback(null, response);
    } else {
      console.log("Probably not a valid email address");
      callback(`Access Denied by Kickbox server. (${body.sendex})`);
    }
  });
};
```

In this case, we are looking at the Sendex score only. If the Sendex score is over `0.75`, we assume that the email address is safe and good to go. There are various other checks that we could make to decide if we want to accept the user or not.

Using this code will send back a generic error message to the end-user on the [Auth0 Universal Login page](https://auth0.com/docs/hosted-pages/login) when the user is trying to sign up with an invalid email.

{% include tweet_quote.html quote_text="Auth0 Hooks are a new extensibility mechanism in Auth0 that allows us to customize the behavior of the Auth0 platform using Node.js." %}

## Taking the User Experience One Step Further

This workflow may not create the most user-friendly experience. In order to show a more meaningful message, we could use the [Auth0 Hosted Pages editor](https://auth0.com/docs/hosted-pages#customize-your-hosted-page). Now that we have access to the Kickbox email data, we could still let our users login but we could perform email verification if we are feeling unsure. There are many other options that we can add to this workflow from here on. There is no limit at what we can do using the Auth0 hooks! I encourage everyone to give them a try.

{% include asides/about-auth0.markdown %}
