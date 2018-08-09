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
