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
