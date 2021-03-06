---
layout: post
title: A Scout Approach to Software Development
metatitle: "Apply Scouting Rules to Code - Scout Development Approach"
description: Learn how to apply the Scout code to software development to get code to a better state continuously.
metadescription: "How to apply the Scout Rule to your software development and coding approach to scale, maintain, and collaborate on codebases in a cleaner, more effective manner."
date: 2018-09-20 10:30
category: Technical Guide, Auth0 Engineering, Software Development
auth0_aside: true
author:
  name: José F. Romaniello
  url: https://twitter.com/jfroma
  mail: jose@auth0.com
  avatar: https://cdn.auth0.com/blog/auziros/Jose-Romaniello.png
design:
  bg_color: #333333
  image: https://cdn.auth0.com/blog/logos/camping.png
tags:
  - engineering
  - enterprise
  - authorization
  - architecture
  - development
  - best-practices
  - programming
  - teamwork
  - teams
  - growth
  - advice
related:
  - 2018-08-15-auth0-architecture-running-in-multiple-cloud-providers-and-regions
  - 2016-03-22-how-we-hire-engineers.markdown
  - 2018-07-26-cloud-scale-thinking-from-day-one-for-your-saas-products
---

When I was a kid, I practiced [_Scouting_](https://en.wikipedia.org/wiki/Scouting) for eight years I picked up a lot of excellent values and my scouting experiences helped to shape my character and make me a better programmer. Let me show you why.

As a Scout, you get to experience nature by camping outdoors which teaches you one of the core values — **respect life**. Scout rules help support core Scout values. One of the first rules I learned was:

> Always leave the campground cleaner than you found it.

This rule comes from a quote by [_Robert Baden-Powell_](https://en.wikipedia.org/wiki/Robert_Baden-Powell,_1st_Baron_Baden-Powell), founder and first [_Chief Scout_](<https://en.wikipedia.org/wiki/Chief_Scout_(The_Scout_Association)>) of [_The Scouts Association_](https://en.wikipedia.org/wiki/The_Scout_Association), and founder of the [_Girl Guides_](https://en.wikipedia.org/wiki/Girlguiding):

> Leave this world a little better than you found it.

What this rule represents is that you should not only pick up your trash, clean the area where you started a wood fire and the soil where you built your tent, but also you need to take a look at the campsite and do something to leave it better than it was before.

It is an explicit instruction. It helps you accept the notion that something can always be better. There’s always room for improvement. You had the opportunity to enjoy the area and its resources but you also have the opportunity to pay back by leaving the area in better shape for others.

I believe that when kids are taught this kind of values through games, repetition, and rewards, it becomes a habit. This is nominally known as [_positive reinforcement_](https://en.wikipedia.org/wiki/Reinforcement#Positive_reinforcement). Positive reinforcement takes place when a reward is presented as the result of certain behavior and the behavior increases. This trait has been embedded in my mind so that I don’t need to apply it consciously. It has become a behavioral reflex that integrates naturally in my day-to-day activities.

## The Codebase Is the Developer's Campsite

Even if I don't go camping often nowadays, I follow this rule in a lot of different situations in my life. One of them is in my daily work — programming!

Working on a new feature or a bug feels excellent. Working on a codebase change feels like camping as a Scout. It is an opportunity to:

- Experience the environment.
- Understand the problem.
- Put what I know in practice.
- Use my skills to create solutions.

The codebase will host me for hours or days. Even if I don't enjoy the codebase too much, I still have to respect it because other people have worked hard on it. It gives me an opportunity to learn new things and to possibly use tools I have never been exposed to before.

{% include tweet_quote.html quote_text="Even if we don't enjoy a codebase too much, we still have to respect it because other people have worked hard on it. We can work on taking little steps to improve it." %}

Once again, I have to assume that other people will get to "camp" in the project after me. People I haven't met yet and probably will never meet. They may be developers, project managers, even end-users. As thought leader and software craftsman [_Uncle Bob Martin_](https://twitter.com/unclebobmartin) says in his [_Scout Rule_](https://www.amazon.com/Things-Every-Programmer-Should-Know/dp/0596809484):

> “Indeed, the act of leaving a mess in the code should be as socially unacceptable as littering. It should be something that just isn't done.”

Therefore, not only my work in the codebase needs to be clean, but it should leave the code at least a little better than I found it.

These are some examples in which I have applied this improvement of the status quo within a project:

- It took me 30 minutes to understand what this function does. Can I rename it to something more declarative or expressive so that others can understand it quicker than I did?
- I wasn't able to run this project with the provided instructions. Can I write the instructions that I followed but are not documented in a README file?
- This code is more convoluted than it should be. Can I make it easier and clearer?
- This function doesn’t have enough tests. Can I add some tests?

These small improvements will help my teammates and myself later. The code is not going to end up perfect, but it will be better than it was before. By following this practice, the code grows in a better direction with every action that you take on it.

{% include tweet_quote.html quote_text="Small but powerful actions for teams that improve a codebase gradually: Vague naming? Make it explicit. Confusing code? Make it clearer. No documentation? Add steps to the README. Missing tests? Write one." %}

I have been doing software development for years and being a Scout had already prepared me to transfer the mindset and values exposed by Uncle Bob from the campsite to the codebase. But this approach is not an excuse for not allocating exclusive time and developers to fix issues in a codebase; it is just one way to continuously get things to a better state.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/scout-approach-to-software-development/Binary-Tree.png" alt="Binary tree - example of clean code growing organically and gradually following the Scout development approach.">
</p>

{% include profile_card.html picture="https://cdn.auth0.com/blog/auziros/Jose-Romaniello.png" name="José F. Romaniello" title="Head of Engineering" team="Office of the CTO" location="Córdoba, Argentina" body="I started working at Auth0 before the first line of code for our authentication platform existed. I’ve been able to go through the journey of seeing our ideas go from prototype to living production code that impacts millions of people. It’s satisfying to know that my actions here are having a positive impact in solving problems for developers like me." %}

{% include asides/about-auth0.markdown %}
