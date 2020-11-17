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

**TL;DR:** As at the time of this writing, the [Svelte]((https://svelte.dev/) frontend framework has overtaken [Vue](https://vuejs.org) in popularity in 2019 according to the [State of JS 2019](https://2019.stateofjs.com/front-end-frameworks/), disrupting the framework "Equilibrium" that frontend developers have enjoyed for a while with [React](https://reactjs.org/), [Vue](https://vuejs.org/) and [Angular](https://angular.io/). Unlike other frameworks, Svelte does not do its DOM-updating work in the browser using the Virtual DOM but instead compiles efficient Javascript code in its build step that efficiently updates your DOM when a state change occurs. This paradigm-changing strategy has led to the fast-rising popularity and adoption of Svelte. In this post, you will learn how to implement authentication in Svelte applications using [Auth0](https://auth0.com/).

## What we are to build

In this tutorial, you will build a simple task list manager using the Svelte framework. Users will be able to sign into this application to have authenticated access and create task items. When a user completes a task, the task can then be ticked off as "completed" on the list. By the end of this exercise, you will be fully armed with all you need to authenticate Svelte apps with Auth0. You can find the complete code for this tutorial at this [Github repository](https://github.com/coderonfleek/svelte-auth0-app/).

## Prerequisites

To get started building this application, you will need to have a few things set up:

1. [Nodejs](https://nodejs.org) installed on your system (`version >= 10` is recommended)
2. A web browser

Surprised? Svelte is a very minimalistic framework. With these two, you are good to go.

## Creating the Auth0 Application

Next up, you will be creating your Auth0 application to handle authentication in your Svelte application, so head to [Auth0](https://auth0.com)'s website and click the **Log in** button to sign in to the console.

Once logged in, click on **Applications** on the left-hand side menu. On the **Applications** page, click on the big orange **CREATE APPLICATION** button.

On the **Create Application** dialog that pops up, enter an appropriate name for your application and select **Single Page Web Applications** from the options below the application name field.

![Create Auth0 Application](images/2020-11-17-new-app.png "Create Application - Auth0 Console")

Now click the **Create** button to complete the process.

After the successful creation of the application, go to the **Settings** section of your newly created app. In the **Allowed Callback URLs**, **Allowed Web Origins**, **Allowed Logout URLs** and **Allowed Origins (CORS)** fields, enter `http://localhost:5000`. This address is the default address of the Svelte application you will be creating later on. Make sure you update these settings once your application moves to production.

Once you're done entering these values, scroll down and hit the **SAVE CHANGES** button.

## Scaffolding the Svelte Project

Your next task is to scaffold a new Svelte project. You can do this by cloning the standard Svelte project template using [degit](https://www.npmjs.com/package/degit) at any preferred location on your system as follows:

```bash
npx degit sveltejs/template my-svelte-project
```

In the command above, `npx` invokes `degit` to clone the standard template in your current working directory. `my-svelte-project` is the folder in which the project code will be cloned. Once this process is complete, go into the root of your project by running `cd my-svelte-project`.

Next, you need to install the project dependencies with the following command:

```bash
npm install
```

When all dependencies have been installed, run the following command to boot up a development server to run your app in:

```bash
npm run dev
```

Once the above command is done, you will see the development URL of your application printed to the console, i.e. `http://localhost:5000`. Load this URL in your browser, you will see a page similar to the one below:

![New Svelte Application](images/2020-11-17-svelte-home.png "New Svelte Application")
