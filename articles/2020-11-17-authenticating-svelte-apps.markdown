---
layout: post
title: "Authentication Svelte Apps with Auth0"
description: "Authentication Svelte Apps with Auth0"
date: "2020-11-17 08:30"
author:
  name: "Fikayo Adepoju"
  url: "coderonfleek"
  mail: "fik4christ@yahoo.com"
  avatar: "https://twitter.com/coderonfleek/profile_image?size=original"
related:
  - 2017-11-15-an-example-of-all-possible-elements
---

**TL;DR:** As at the time of this writing, the [Svelte]((https://svelte.dev/) frontend framework has overtaken [Vue](https://vuejs.org) in popularity in 2019 according to the [State of JS 2019](https://2019.stateofjs.com/front-end-frameworks/), disrupting the framework "Equilibrium" that frontend developers have enjoyed for a while with [React](https://reactjs.org/), [Vue](https://vuejs.org/) and [Angular](https://angular.io/). Unlike other frameworks, Svelte does not do its DOM-updating work in the browser using the Virtual DOM but instead compiles efficient Javascript code in its build step that efficiently updates your DOM when a state change occurs. This paradigm-changing strategy has led to the fast-rising popularity and adoption of Svelte. In this post, you will learn how to implement authentication in Svelte applications using [Auth0](https://auth0.com/). You can find the complete code for this tutorial at this [Github repository](https://github.com/coderonfleek/svelte-auth0-app/).

## What we are to build

In this tutorial, you will build a simple task list manager using the Svelte framework. Users will be able to sign into this application to have authenticated access and create task items. When a user completes a task, the task can then be ticked off as "completed" on the list. By the end of this exercise, you will be fully armed with all you need to authenticate Svelte apps with Auth0.

## Prerequisites

To get started building this application, you will need to have a few things set up:

Usually, we start describing again what this article is about. The difference, however, is that here we can explain with more words. Another important thing that we should include in this first paragraph are the name of the main technologies that we will talk about, and add a link to them. For example, we could say that we are going to use [React](https://reactjs.org/) and [Redux](https://redux.js.org/) to develop a simple [SPA (Single Page Application)](https://en.wikipedia.org/wiki/Single-page_application).
