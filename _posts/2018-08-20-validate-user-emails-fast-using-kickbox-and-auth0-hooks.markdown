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
