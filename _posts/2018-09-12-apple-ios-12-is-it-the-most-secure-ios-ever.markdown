---
layout: post
title: "Apple iOS 12: Is it the Most Secure iOS Ever?"
metatitle: "Apple iOS 12 Security - Most Secure iOS Ever? View New Features"
description: "The software’s latest features will help users bolster their data security."
metadescription: "Learn about Apple's latest iPhone iOS 12 release's security features. User's data security and privacy is more important than ever. Check out how Apple is addressing this."
date: 2018-09-12 11:27
category: Technical Guide, Whats New, iOS
auth0_aside: true
author:
  name: "Martin Gontovnikas"
  url: http://twitter.com/mgonto
  mail: gonto@auth0.com
  avatar: https://www.gravatar.com/avatar/df6c864847fba9687d962cb80b482764??s=60
design:
  bg_color: "#4A4A4A"
  image: https://cdn.auth0.com/blog/apple-ios-12-security/apple-ios-12-logo.png
tags:
- ios
- security
- sms
- usb
- data
- data-security
- privacy
- data-privacy
- security
- auditing
- password
- 2fa
post_length: 1
related:
- 2017-12-29-why-every-business-needs-two-factor-authentication-security
- 2017-11-22-using-centralized-login-to-add-authentication-to-your-ios-apps
- 2017-10-16-is-faceid-really-secure
---

If you happened to catch Apple’s initial June 4, 2018 announcement of iOS 12, you might have noticed that it was lacking the “big splash” that we’ve seen from other iOS announcements. That’s partially because Apple was so busy focusing on new iOS 12 data security features.

Though more was unveiled at the September 12, 2018 Apple Keynote event, many of the features of iOS 12 likely won’t catch the average consumer’s attention. That’s intentional. Good data security features are relatively unnoticeable to most users. If anything, those features in Apple’s newest mobile OS will make life a little bit easier for the average iPhone user.

But, if you happen to be an IT or security pro, the new iOS 12 probably piqued your interest.

This emphasis on personal data privacy and security from Apple prompts one question: is iOS 12 the most secure operating system Apple has ever released?

In a word, yes. Let’s dig into what makes it so secure.

## Better Anti-Hacking Technology for Data Privacy

One of the biggest changes Apple is making to the new iOS’s security is the introduction of USB Restricted Mode. 

On iOS 11 and earlier iOS versions, USB-based communications stay active for up to seven days after the phone is locked. This means that if an iPhone is stolen, the would-be hackers have seven days to plug a brute force device, such as GrayKey, into the phone and force it to unlock.

_[The New York Times](https://www.nytimes.com/2018/06/13/technology/apple-iphone-police.html)_ calls this seven-day window a massive security vulnerability in iOS 11.

The good news is that Apple is closing this window. With USB Restricted Mode on iOS 12, Apple is reducing the time for USB-based communications to just one hour after the phone is locked. 

That makes a brute force attack on iOS 12 nearly impossible. According to one app developer we talked to, “USB Restricted Mode essentially turns the phone into a battery-powered brick after one hour.” Unless the phone is unlocked by an authorized user in that hour timeframe, it’s useless to would-be hackers.

To illustrate this further, if an employee at your company has their iPhone stolen with iOS 11 on it, there’s a very good chance the thief will be able to gain access to the phone within seven days. However if iOS 12 is installed, the thief will only have one hour to attempt a brute force attack. The chances of that being accomplished in an hour are very unlikely. 

This data privacy feature is automatically turned on in the new OS, but Apple does give you the option of disabling it:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/apple-ios-12-is-it-the-most-secure-ios-ever/ios12-usb-restricted-mode-off.png" alt="Turn USB Restricted Mode Off in iOS 12">
</p>

_[Image Source](https://www.macrumors.com/how-to/access-controls-usb-restricted-mode-ios-12/)_

We're excited by both the strength and flexibility of this for greater personal data security.

## Password Reuse Auditing

As is the case with any data security system, humans are the weakest link. If you develop the best tools in the world but the actual human operators aren’t using it, then it’s worthless.

That’s why Apple is trying to make password management as simple as possible in iOS 12. One of the features that makes this easier is Password Reuse Auditing. This feature monitors the passwords that iPhone owners use for different services. When it catches someone using the same password on different websites, it flags the duplicate, offers a more secure password, and then saves it to the iCloud Keychain.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/apple-ios-12-is-it-the-most-secure-ios-ever/ios-12-password-reuse-auditing-flag-present-on-online-service.png" alt="iOS 12 Password Autofill - Password Reuse Auditing flag present on online service">
</p>

_[Image Source](https://www.macrumors.com/how-to/automatic-passwords-and-auditing-in-ios-12/)_

The issues with using one password across different websites are well documented. Security experts have been trying to get around these issues for years and Apple is doing their best to help. Auth0 offers secure authentication using [token-based, passwordless authentication](https://auth0.com/passwordless) that features a brute-force protection system.

### iCloud: The Elephant in the Room

At first glance, this iOS 12 security feature seems slightly less secure. For this feature to work, Apple has to record your current passwords and be able to crawl them for matches. While at the same time, Apple stores everything in the iCloud. 

That concern caused Apple to make this system as secure as possible. This feature uses a highly secure 256-bit AES for encryption. 256-bit AES is nearly impossible to crack. It’s actually the same encryption standard that the US government uses to transmit top secret data. It seems that Apple is pretty serious about security. 

In addition to using the uncrackable 256-bit AES, Apple also recommends users enable two-factor authentication for their iCloud Keychain. That combination makes it extremely unlikely that some nefarious individual would be able to compromise the iCloud. 

Speaking of two-factor authentication…

## Making Two-Factor Authentication Easier

_[Two-Factor Authentication](https://auth0.com/learn/two-factor-authentication/)_ (2FA) is the next big security feature in iOS 12. 

For years, data privacy experts have been begging and pleading with website users to set up 2FA. It's one of the best ways to [protect individuals and small teams from cyber attacks](http://blog.idonethis.com/security-ringcaptcha/). Adoption has still been relatively low because 2FA adds an extra step for users.

The most common type of Two-Factor Authentication is SMS text message 2FA. This authentication protocol sends users a text with a passcode before they are granted access to a system.

The problem is, it requires a lot of switching back and forth between apps to get the code, memorize the code, and then plug it into the app. And, if the user mistypes or misremembers the code, then it's back to square one.

Luckily, Apple is coming to the rescue. With iOS 12, SMS security codes will autofill without the user having to switch from app to app. No memorization, either. It does this by making specific API calls to the messaging app. When iOS 12 recognizes an SMS 2FA security code, it will automatically pull it into autofill for the user. 

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/apple-ios-12-is-it-the-most-secure-ios-ever/ios-12-recognizes-an-sms-2fa-code-and-autofills-it-for-user.gif" alt="iOS 12 SMS 2FA security code and autofill example">
</p>

There is one downside here. It has been well established that SMS 2FA can be compromised. We recently mentioned this _[in a post about a Reddit data breach](https://auth0.com/blog/what-companies-can-learn-from-the-reddit-data-breach/)_. Even still, any 2FA is better than no 2FA. With Apple including this feature in iOS 12’s security, they’ve made users significantly less prone to hacking.

## What Else is New in Data Security for iOS 12?

There are a few other security features worth mentioning in iOS 12. First up is group video chat. What makes this a security feature? Facetime is fully encrypted and extremely secure. Adding group chat to Facetime simply means that Apple is now supporting fully encrypted group chats. 

Apple also introduced a new Face ID feature. The tech company is giving iPhone owners the ability to add a “second face.” This allows two different users to unlock an iPhone using Face ID. We’ve covered the _[security of Face ID](https://auth0.com/blog/is-faceid-really-secure/)_ is another blog post. It’s good to see Apple improving upon this feature.

## Final Thoughts iOS 12 Release

We’re really impressed with Apple’s new iOS 12 security features. Not only are they making the operating system more secure, they’re making security easier to use. That combination is one that security experts are always after. Be looking for "A new iOS update is now available" as iOS 12 will be released soon. Apple unveiled at it's September 12, 2018 Keynote that the public release of iOS 12 becomes available on Tuesday September 18th.

{% include asides/about-auth0.markdown %}