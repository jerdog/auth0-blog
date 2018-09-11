---
layout: post
title: 4 Vital Ways Enterprise SSO Saves Your Employees Time and Provides Peace of Mind
metatitle:
description:
metadescription:
date: 2018-09-11 14:58
category: Business, Identity, SSO
post_length: 1
is_non-tech: true
author:
  name: "Diego Poza"
  url: "https://twitter.com/diegopoza"
  mail: "diego.poza@auth0.com"
  avatar: "https://avatars3.githubusercontent.com/u/604869?v=3&s=200"
design:
  bg_color: "#297373"
  image:
tags:
- sso-login
- single-sign-on
- credentials
- lessons
- enterprise
- time-saving
- sso
related:
- 2017-07-31-2016-social-passwordless-and-sso-data-what-can-we-learn
- 2018-01-10-implementing-single-sign-on-in-b2c-applications
---

## Enterprise SSO ensures better productivity, user experience, and security for employees through single-click access.

Leading up to the 2016 U.S. presidential election, Russian hackers allegedly attacked the Hillary Clinton campaign and the Democratic National Committee with [spear-phishing](https://www.pcmag.com/article/354240/spear-phishing-attacks-what-you-need-to-know) efforts to trick users into providing their usernames and passwords to fraudulent websites.

As cybersecurity concerns continue to mount, user credentials demonstrate a universal weak point in corporate security, and the more accounts and logins employees and IT departments must manage, the more hackers can find valuable openings. 

So how do companies navigate these risks in a world constantly pushing for more SaaS application use as they grow in size?

One answer lies in SSO, a way to enable single-click credentials and secure identity verification while also improving user experience. 

## What is SSO?

SSO—Single Sign-on—acts as a holistic, single login point where users enter one set of authentication credentials that grant access to a suite of other applications, regardless of the platform, technology, or domain used. 

Picture an office building with unique offices for a variety of related but separate businesses, each with their own key. 

But if it were using an SSO-like system, authorized personnel would instead use one key to get through the front door, sparing them the hassle of using separate keys to get into the individual offices. 

This is the general idea behind how SSO systems work for services like G Suite — where your Google account login gives you access to a variety of individual applications — and Slack. 

### How does SSO work?

Users begin their SSO experience by logging into a gateway, which can be displayed in a variety of ways, depending on the application a company uses. 

With Auth0, users are redirected to a login page with username and password fields, and may include Social Identity Providers such as LinkedIn, GitHub, Google, etc.

![Example Auth0 login screen](https://cdn.auth0.com/blog/enterprise_sso:auth0_example.png)

From there, the users go through the following steps: 

1. They enter their master username and password
2. The credentials are run through the Identity Provider (IdP) for verification, where the authentication server checks the credentials against the directory that contains stored data
3. If the credentials check out, the authorizing server will set an SSO cookie 
4. Users are then redirected to the application they want to access

In the future, users won’t have to see a login screen when accessing any approved application integrated with the SSO setup. 

Instead, when users want to log in:

1. the IdP checks to see if there is an existing SSO cookie; 
2. if necessary, the provider updates the cookie; and 
3. the IdP returns an ID token containing the users' identity information and logs them in. 

Note: During this process, users don’t see a login screen and are seamlessly directed to the dashboard of the application they’re looking to use. 

## 1. Enterprise SSO Improves Employees' User Experience

By using enterprise SSO, companies save employees time and effort by asking them for only one login and one set of credentials. 

These credentials work for all cloud, on-premises, and mobile applications and provide a hub for all company applications, rather than requiring employees to remember usernames and passwords for each tool.

SSO also provides employees with a more personalized experience by bringing all of their tools and resources into one hub. 

## 2. SSO Provides Multiplatform Access

{% include tweet_quote.html quote_text="Universal access provides employees with the flexibility they expect in the modern workplace and ensures IT security against the threats inherent in modern digital life" %}

By giving employees a single point of login, companies seamlessly provide their teams with multiplatform access. 

With recent studies showing that small companies use a mean of [13.4 different applications](https://www.intermedia.net/assets/pdf/Osterman_-_Survey_on_Application_Use_Within_Organizations.pdf), and large companies boast an average of [more than 15 applications](https://www.intermedia.net/assets/pdf/Osterman_-_Survey_on_Application_Use_Within_Organizations.pdf), SSO allows companies to reduce the chance of employees losing, reusing, and sharing passwords across a multitude of accounts.

That means employees aren’t limited to accessing key tools and resources only at work: They can also securely log into these applications from their homes and mobile phones or access them while traveling.

![Google apps panel](https://cdn.auth0.com/blog/enterprise_sso:google_apps.png)

[Source: [Google](https://gsuite.google.com/)]

This universal access provides employees with the flexibility they expect in the modern workplace and ensures IT security against the [threats inherent in modern digital life](https://auth0.com/blog/four-cybersecurity-attacks-you-need-to-know/).

## 3. Enterprise SSO Ensures More Secure Passwords

While employees objectively know they should create hard-to-guess passwords, the impracticality of remembering complex credentials often results in the reuse of weak passwords over and over again. 

In fact, [nearly 9_0% of people ages 18–30 use the same password_](https://keepersecurity.com/assets/pdf/Keeper-Mobile-Survey-Infographic.pdf) across multiple accounts. 

When employees give in to the demand for increasingly complex passwords in order to thwart hackers, the frequency of password recoveries skyrockets. Because of these complex password requirements, [_60% of people need to reset their passwords every 60 days_](https://keepersecurity.com/assets/pdf/Keeper-Mobile-Survey-Infographic.pdf) — a practice that also [_costs companies an average $179 per employee_](https://www.pingidentity.com/en/company/blog/2015/11/13/passwords_will_die_a_slow_death.html) [_per employee_](https://www.pingidentity.com/en/company/blog/2015/11/13/passwords_will_die_a_slow_death.html) and is largely avoidable with SSO.

When companies embrace enterprise SSO, team members have to remember only one master set of credentials. This prevents them from creating unique, complex sets of passwords, reusing weak passwords, or [_keeping physical records of all passwords_](https://keepersecurity.com/assets/pdf/Keeper-Mobile-Survey-Infographic.pdf) — all of which are inconvenient, costly, and insecure. 

{% include tweet_quote.html quote_text="Companies looking to save their employees time and provide peace of mind while improving individual and network-wide security can set up SSO with the help of Auth0." %}

## 4. Enterprise SSO Boosts Employee Productivity

Utilizing SSO for employees boosts productivity by reducing the time teams spend locked out of key tools while waiting for password resets and getting set up on new applications. 

Employees are also much more likely to know about and take advantage of resources and tools that make their jobs easier and faster and answer common user questions when those applications are linked to one hub, as SSO allows. 

## How Does a Company Set Up Enterprise SSO?

Companies looking to save their employees time and provide peace of mind while improving individual and network-wide security can set up SSO with the help of Auth0.

Beyond the technical aspects of the switch, companies should keep in mind the following rollout tips to help their teams adjust to an SSO setup:

* Make sure to carry over login information so team members don’t have to reenter everything.
* Provide clear direction for users.
* Identify key applications that employees often use, including key software, cloud accounts, and social media. 
