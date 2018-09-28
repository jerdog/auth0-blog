---
layout: post
title: "Early Look at Facebook Access Token Security Breach"
metatitle: "Early Look at Facebook Security Breach Involving 50 Million Accounts"
description: "Almost 90 millions Facebook users were affected by a breach that compromised access tokens to the platform."
metadescription: "About 90 millions Facebook users were impacted by a breach that compromised platform access tokens, making it possible for attackers to take over accounts."
date: 2018-09-28 16:38
category: Hot Topics Security Breaches
post_length: 
auth0_aside: true
author:
  name: Diego Poza
  url: https://twitter.com/diegopoza
  avatar: https://avatars3.githubusercontent.com/u/604869?v=3&s=200
  mail: diego.poza@auth0.com
design: 
  bg_color: "#203259"
  image: https://cdn.auth0.com/blog/cambridge-analytica-facebook/logo.png
tags: 
  - facebook
  - breach
  - privacy
  - security
  - login
  - tokens
  - authentication
  - social-media
  - policy
related:
  - 2018-07-02-breaking-down-master-data-management
  - 2018-08-02-four-cybersecurity-attacks-you-need-to-know
  - 2018-08-03-what-data-did-facebook-really-give-cambridge-analytica
---

On September 25th, 2018, Facebook learned from its engineering team that [a security vulnerability compromised around 50 million accounts](https://newsroom.fb.com/news/2018/09/security-update/). While the investigation is still in progress, Facebook already has defined the attack surface and vulnerabilities that led to the attack along with identity data that was compromised.

A feature that allows Facebook users to determine what their profile looks like to other people, known as ["View As"](https://www.facebook.com/help/288066747875915?helpref=faq_content), had a vulnerability that was exploited by attackers. Through this vulnerability, attackers were able to steal Facebook access tokens. An [access token](https://auth0.com/docs/tokens/access-token) is a credential that can be used by an application to access an API. Its main purpose is to inform the API that the bearer of this token has been authorized to access the API and perform specific actions. In this case, an attacker could have used the Facebook access tokens to take over accounts.

One of the ways that Facebook uses access tokens is to persist login. As Facebook mentions on their [Security Update](https://auth0.com/docs/tokens/access-token), these tokens are the "equivalent of digital keys that keep people logged in to Facebook so they don’t need to re-enter their password every time they use the app." Thus, for this type of data breach, changing the user's password doesn't provide risk mitigation. Instead, the digital keys need to be changed. Once Facebook understood the origin and nature of the attack, the engineering team carried out the following actions to mitigate any damage to the platform and to users:

- Facebook fixed the vulnerability in the "View As" feature.
- Facebook has informed law enforcement about the attack.
- Facebook has reset the access tokens of the almost 50 million accounts that were determined to be at risk in order to diminish any risk to the user data and to ensure their personal and digital security.
- As a precautionary step, Facebook has also reset the access tokens for another 40 million accounts. These accounts used the "View As" feature in the past year.

As a result of these security measures, about 90 million users will have to log back into the Facebook platform. Other applications that use Facebook login are also impacted and will require users to re-authenticate. Facebook wants to remain transparent and direct in the acknowledgment and communication of this security breach. Once affected users log back into their accounts, they will receive a notification at the top of their News Feed with an explanation of what has happened and what actions were taken.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/facebook-data-breach/facebook-data-breach-notification-access-tokens" alt="Updated on September 28, 2018 at 1:08PM PT to include image of notification.">
</p>

[Source](https://fbnewsroomus.files.wordpress.com/2018/09/42647794_340201783392972_4207828087510925312_n.png?w=960&h=551)

As a final precaution, Facebook has turned off the "View As" feature to run a security review as part of their investigation. As the security investigation is on its early stage, Facebook has yet to determine if any of the compromised accounts had their data accessed or misused. The identity or location of the attackers is also unknown at this time. Facebook vows to keep users updated on the evolution of the investigation. 

On a statement on his personal page, Mark Zuckerberg, Facebook CEO, issued a statement [summarizing the nature of the data breach](https://www.facebook.com/zuck/posts/10105274505136221). Part of his statement includes the assessment that new tools need to be developed to prevent attacks like this from being successful:

> "We face constant attacks from people who want to take over accounts or steal information around the world. While I'm glad we found this, fixed the vulnerability, and secured the accounts that may be at risk, the reality is we need to continue developing new tools to prevent this from happening in the first place. If you've forgotten your password or are having trouble logging in, you can access your account through the Help Center."

To stay tuned on this developing investigation, visit [Facebook Newsroom](https://newsroom.fb.com/) or follow [@facebook](https://twitter.com/facebook) on Twitter.


{% include asides/about-auth0.markdown %}