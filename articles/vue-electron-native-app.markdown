---
layout: post
title: "Building Desktop Applications with Vue.js and Electron"
description: "Building Desktop Applications with Vue.js and Electron"
date: "2019-03-18 08:30"
author:
  name: "Fikayo Adepoju"
  url: "coderonfleek"
  mail: "fik4christ@yahoo.com"
  avatar: "https://twitter.com/coderonfleek/profile_image?size=original"
related:
  - 2017-11-15-an-example-of-all-possible-elements
---

# Building Desktop Applications with Vue and Electron

**TL;DR:** In the emerging world of "all things Javascript", it is no longer a mind-blowing fact that developers can now write desktop applications with Javascript. Since the initial release of Electronjs about 5 years ago, web developers have been empowered to use web technologies to develop applications that run as native desktop apps. One of the many beautiful things about Electronjs is that you can use any Javascript framework you prefer to build the interface of your desktop application. In this article, you will learn how easy it is to use Vue to build interfaces for Electronjs apps.

## Prerequisites

1. Basic knowledge of [Vue](https://vuejs.org)
2. Basic knowledge of [Electronjs](https://electronjs.org/)
3. [Nodejs](https://nodejs.org) installed on your system
4. [Vue-CLI](https://cli.vuejs.org) installed on your system : `npm install -g @vue/cli`
5. Electronjs installed on your system : `npm install electron -g`

## What You Will Build

You will be building a To-Do List desktop application using Vue and Electronjs. This application will require users to sign in to the application. The application will then make an authenticated call to fetch a list of To-Dos from a secured backend API. Users will also be able to sign out of this application.



### Cloning and Running the API

To get a backend API up and running, we would be cloning a simple API built with node [from this repo](https://github.com/coderonfleek/simple-node-api). After cloning the project, simply run `npm install` to install the required packages. Then run `node server` to get the server up and running.

Our backend API simply exposes an endpoint `http://localhost:1337/todos` which returns a collection of To-Dos. The project contains 3 main files:

1. `server.js`: This is the entry point to the application
2. `todos.js`: This is a module that simply returns an array of To-Do objects
3. `routes.js`: This is the routes module which returns the routes used in the backend application. This file only contains one route `/todos`.

## Developing Native Apps with Vue.js and Electron

### Scaffolding a Vue and Electron Application

To scaffold your desktop application, you would be making use of an excellent open source project that makes it super simple to work with Vue and Electronjs. This project is the [electron-vue](https://github.com/SimulatedGREG/electron-vue) project which comes bundled with other useful Vue libraries like [Vue Router](https://router.vuejs.org/) and [Vuex](https://vuex.vuejs.org/).

Now scaffold your application by navigating to the directory where you want your project to reside and run the following command:

`vue init simulatedgreg/electron-vue my-desktop-app`

Where `my-desktop-app` is the name you want to give to your project hence you can replace this with any name of your choosing.

Running this command will take you through an interactive installation process. You can simply hit enter to all questions to accept the defaults asides the areas where it asks you about tests and linting, to this you type `n` to decline as we won't be writing tests in this exercise. At some point you will be asked if you want to use `electron-builder` or `electron-packager`, simply go with `electron-builder`.

After responding to all the questions in the installation process a new `electron-vue` project is scaffolded for you. Go into your new project by running `cd my-desktop-app`.

By default, a new `electron-vue` project uses electron@2.x, you need to update the version of electron installed in the `package.json` file to use electron@3.x to ensure that we are using the latest version of Electronjs, thus, in your `devDependencies`, update the `electron` line to this:

`"electron": "^3.0.4"`

Now install the required dependencies by running `npm install`. After all required modules required have been installed, run `npm run dev` to start the application.

You should have a screen similar to the one below.

![vue-electron homepage](images/vue-electron-initialpage.png)

The developer tools panel is open by default because the application is currently being run in development mode.

### Creating a To-Do List Route

Your next task is to create a page for the To-Do list, preferably you would want to make this the first page of the application. Routes in `electron-vue` projects are defined in the `src/renderer/router/index.js` file while components are located in the `src/renderer/components` directory. Go into the `index.js` routes file and replace the contents in the file with the one below:

```javascript

import Vue from "vue";
import Router from "vue-router";

Vue.use(Router);

export default new Router({
  routes: [
    {
      path: "/",
      name: "todos-page",
      component: require("@/components/ToDos").default
    },
    {
      path: "*",
      redirect: "/"
    }
  ]
});

```

As seen, the landing page is now pointing to a component named `ToDos` which has not been created yet.

So the next step is to create this component and see the landing page display it.

Go into the `components` folder and delete everything inside it. Then create a new file named `ToDos.vue` and paste the content below in it:

```javascript

<template>

	<div>
		<h2>Welcome to the To-Dos Page</h2>
	</div>
</template>

```

Now, when you view your application you should see the screen below:

![todos initial screen](images/todos-initial-screen.png)



### Consuming the API

Time to get your To-Dos into your application.

To do this, you would fetch the collection of To-Dos from the backend API (ensure that this is running) and display it in a list on the landing page.

Thus replace the code in `ToDos.vue` with the one below:

```javascript

<template>
  <div>
    <div>
      <div>
        <button @click="fetchTodos()" class="btn btn-primary">Fetch Todos</button>
      </div>
    </div>

    <div>
      <div>
        <ul>
          <li v-for="todo in todos" :key="todo.id">{{todo.task}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
const axios = require("axios");

export default {
  name: "ToDos",
  data: () => {
    return {
      todos: []
    };
  },

  methods: {
    async fetchTodos() {
      axios
        .get("http://localhost:1337/todos")
        .then(response => {
          console.log(response);
          this.todos = response.data;
          console.log(this.todos);
        })
        .catch(error => {
          if (error) throw new Error(error);
        });
    }
  }
};
</script>


```

In the code above, we create a `data` property called `todos` to hold our To-Dos collection, then create a method called `fetchTodos` to call our backend endpoint and load our To-Do collection into the `todos` data property.

In the template, we create a button that calls the `fetchTodos` method on click and a list that displays the To-Dos on the page.

After making this change you should have a screen similar to the one below when you click the `Fetch Todos` button.

![todos list](images/todos-fetch.png)

Wholla! You have successfully created a desktop application that fetches data from a remote backend.

In the next section, you will be securing the data from the backend such that only authenticated users will be able to fetch it.


## Securing Native Vue and Electron Apps


 When it comes to securing applications, [Auth0](https://auth0.com/) stands out as a leading platform to help developers easily add enterprise standard authentication and security features to their applications.

 Whether its a simple application for a demo or a large enterprise application to be used by millions of users, [Auth0](https://auth0.com/) offers a suite of highly secure, easy to use solutions to achieve features such as Single Sign-On, User management, Multifactor authentication, etc.

 On the 11th of February, 2019, Auth0 was named as the Frost & Sullivan's 2019 Global Identity and Access Management Technology Innovation Award winner, further stamping Auth0 as an authority in Identity management.

You will be using Auth0 to secure your backend API and your Electron desktop application.

Below is a summary of the steps you will be taking to achieve that:

1. Create a new Auth0 account (use an existing one if you have previously registered)
2. Register an Auth0 API for your backend
3. Create an Auth0 native client application for the Electronjs app
4. Secure the backend using the Auth0 API details to add token authentication to protect your endpoint
5. Secure the Electronjs application using your Auth0 client application details to load an authentication page for users to sign into your application 
6. Upon successful sign in, the application receives authentication tokens to make authenticated calls to your now secured backend API

### Create the Auth0 API

Navigate to [Auth0 Website](https://auth0.com) and register a new account or sign into an already existing one.

Click on **APIs** on the left side menu. On the APIs page, click on **CREATE API** and enter details to register a new Auth0 API as shown below:

![Create Auth0 API](images/create-auth0-api.png)

In the **Name** field, simply enter any descriptive name for your API.
In the **Identifier** field, enter a URL for your API, this doesn't have to be an existing or publicly available URL, it only serves as an identifier for your API.
Leave the default **RS256** as the Signing algorithm.

Click the **Create** button and your Auth0 API is all set.

The next page you see is your API details page. Click on the **Settings** tab, scroll down and ensure that the *Allow Offline Access* option is enabled, if not, enable it and click the **Save** button. This will enable Auth0 to allow applications request for refresh tokens for your API.


### Securing the Backend API with Auth0

The next step is to secure your backend API with Auth0. Go into the root of your backend application (remember? that simple Nodejs backend we cloned). Shut down the API using `Ctrl + C` if it is currently running.

You will need to install 2 packages, the `jwks-rsa` library which is used to retrieve RSA signing keys from a JWKS (JSON Web Key Set) endpoint, thus this will be retrieving keys from the Auth0 API you just created.

The second library you would need is the `express-jwt` library which would help you authenticate HTTP requests using JWT tokens in your Node.js backend.

Simply install these two libraries by running the following command:

`npm install jwks-rsa express-jwt --save`

Great! After successfully installing these libraries, you will need to create a file to hold your Auth0 API credentials. Create a file name `env.json` in the root of your backend project and paste in the code below (using your own Auth0 API credentials):

```javascript

{
  "apiIdentifier": "YOUR_API_IDENTIFIER",
  "auth0Domain": "YOUR_AUTH0_DOMAIN"
}


```

Your Auth0 domain should be your `[YOUR_USERNAME].auth0.com`.

The next step to securing the API is to use the libraries installed and your Auth0 API credentials to implement token authentication on the backend. To do this, simply replace the code in `server.js` with the one below:

```javascript

const express = require("express");
var jwt = require("express-jwt");
var jwks = require("jwks-rsa");
const env = require("./env");
var cors = require('cors');

// App
const app = express();

//Use cors
app.use(cors());

// Set port
const port = process.env.PORT || "1337";
app.set("port", port);

var jwtCheck = jwt({
  secret: jwks.expressJwtSecret({
    cache: true,
    rateLimit: true,
    jwksRequestsPerMinute: 5,
    jwksUri: `https://${env.auth0Domain}/.well-known/jwks.json`
  }),
  audience: env.apiIdentifier,
  issuer: `https://${env.auth0Domain}/`,
  algorithms: ["RS256"]
});

app.use(jwtCheck);

// Routes
require("./routes")(app);

// Server
app.listen(port, () => console.log(`Server running on localhost:${port}`));


```

In the above code, token authentication is set up using the `jwks-rsa` and `express-jwt` libraries with the Auth0 API details.

One the line `app.use(jwtCheck);`, the application is instructed to use the token check implemented to check that each request to the API comes with the correct token in its `Authorization` header.

Now if you try to access the endpoint `http://localhost:1337/todos` (remember to start your server by running `node server`), you will get an error that says **No authorization token was found**. This indicates that an access token is now required to access that endpoint

In the next section, you would go ahead to add authentication to your Electronjs desktop application.

### Creating the Auth0 Application

To create an Auth0 client Application, return to your Auth0 dashboard and on the left side menu click on **Applications**. On the **Applications** page, click on the **Create Application** button.

You will see a dialog like the one shown below, enter a name for your application and select **Native** as the type of application you want to create then click the **Create** button.

![Create Client Application](images/create-client.png)

Once the application has been successfully created you will be taken to a screen where you can see all your application's details including settings.

Click on the **Settings** tab and scroll down to the *Allowed Callback URLs* and enter the value `file:///callback`. Scroll down and click **Save Changes**.

### Securing the Vue and Electronjs Apps with Auth0

Now back to the Electronjs desktop application. What is to be achieved here is to present a Sign In screen for users to log in to access the application before the To-Dos homepage is then presented upon successful login.

To do this, some packages need to be installed in your Electronjs app. First is `jwt-decode` to help decode JSON Web Tokens, `request` library to make API calls in Electronjs main process and `keytar` which is a  native Node module to get, add, replace, and delete passwords in the system's keychain, then we add `bootstrap` for some basic styling.

Install these packages with the following command:

`npm install jwt-decode request keytar bootstrap --save`

**NB:** After installing `keytar` any subsequent running of the `npm install`  or `npm run dev` commands might result in a versioning error between your keytar installation and Nodejs version. To fix this, install the `electron-rebuild` package as a development dependency (`npm install electron-rebuild --save-dev`), then run the command:

`./node_modules/.bin/electron-rebuild ` on Unix/Linux based systems or `.\node_modules\.bin\electron-rebuild.cmd ` if you're on Windows to fix this issue.

Next step is to create an `env.json` file to hold our Auth0 credentials for the client, create this file in the root of your `electron-vue` project.

```javascript

{
  "apiIdentifier": "YOUR_AUTH0_API_IDENTIFIER",
  "auth0Domain": "YOUR_APP_DOMAIN",
  "clientId": "YOUR_CLIENT_ID"
}

```

You can get your client Id from your Auth0 application details page in the **Settings** section.


Next thing you will do is create 3 modules. Each module will do the following

1. A module to hold all the methods and properties to handle the authentication flow (auth-service.js)
2. A module that controls the authentication flow process (auth-process.js)
3. A module that loads the application homepage (app-process.js)

All these modules will reside in the `main` process of the Electronjs application

Navigate to the directory `src/main` and create a folder named `services`.

Inside this `services` folder, create a file named `auth-service.js` and paste in the code below:

```javascript

const jwtDecode = require("jwt-decode");
const request = require("request");
const url = require("url");
const envVariables = require("../../../env");
const keytar = require("keytar");
const os = require("os");

const { apiIdentifier, auth0Domain, clientId } = envVariables;

console.log(clientId);

const redirectUri = `file:///callback`;

const keytarService = "my-todo-app";
const keytarAccount = os.userInfo().username;

let accessToken = null;
let profile = null;
let refreshToken = null;

function getAccessToken() {
  return accessToken;
}

function getProfile() {
  return profile;
}

function getAuthenticationURL() {
  return (
    "https://" +
    auth0Domain +
    "/authorize?" +
    "audience=" +
    apiIdentifier +
    "&" +
    "scope=openid profile offline_access&" +
    "response_type=code&" +
    "client_id=" +
    clientId +
    "&" +
    "redirect_uri=" +
    redirectUri
  );
}

function refreshTokens() {
  return new Promise(async (resolve, reject) => {
    const refreshToken = await keytar.getPassword(keytarService, keytarAccount);

    if (!refreshToken) return reject();

    const refreshOptions = {
      method: "POST",
      url: `https://${auth0Domain}/oauth/token`,
      headers: { "content-type": "application/json" },
      body: {
        grant_type: "refresh_token",
        client_id: clientId,
        refresh_token: refreshToken
      },
      json: true
    };

    request(refreshOptions, function(error, response, body) {
      if (error) {
        logout();
        return reject();
      }

      accessToken = body.access_token;
      console.log(accessToken);
      profile = jwtDecode(body.id_token);

      resolve(accessToken);
    });
  });
}

function loadTokens(callbackURL) {
  return new Promise((resolve, reject) => {
    const urlParts = url.parse(callbackURL, true);
    const query = urlParts.query;

    const exchangeOptions = {
      grant_type: "authorization_code",
      client_id: clientId,
      code: query.code,
      redirect_uri: redirectUri
    };

    const options = {
      method: "POST",
      url: `https://${auth0Domain}/oauth/token`,
      headers: {
        "content-type": "application/json"
      },
      body: JSON.stringify(exchangeOptions)
    };

    request(options, (error, resp, body) => {
      if (error) {
        logout();
        return reject(error);
      }

      const responseBody = JSON.parse(body);
      accessToken = responseBody.access_token;
      profile = jwtDecode(responseBody.id_token);
      refreshToken = responseBody.refresh_token;

      keytar.setPassword(keytarService, keytarAccount, refreshToken);

      resolve();
    });
  });
}

async function logout() {
  await keytar.deletePassword(keytarService, keytarAccount);
  accessToken = null;
  profile = null;
  refreshToken = null;
}

export default {
  getAccessToken,
  getAuthenticationURL,
  getProfile,
  loadTokens,
  logout,
  refreshTokens
};


```

A lot is going on in this file, so let's take some time to break it down.

First, variables are defined to hold the environment variables in the `env.json` file, then another variable is defined to hold the identifier for the keychain service: `my-todo-app` (this value is to be unique for every application) and then another defined to get the `keytar` account.

Next, more variables are defined to hold the access token gotten from Auth0, the profile of the logged in user and the refresh token that helps us get a new access token after the current access token has expired.


The following functions are then defined

1. `getAccessToken`: returns the access token from Auth0 after a successful authentication
2. `getProfile`: returns the profile of the logged in user
3. `getAuthenticationURL` : constructs and returns an Auth0 authentication page URL.
4. `refreshTokens`: This method gets the refresh token saved in our keychain and uses it to fetch a new access token from Auth0's API. It then returns a promise that resolves with the new access token.  
5. `loadTokens`: This method takes the callback URL from Auth0 after successful authentication and gets the **authorization code** from the callback URL query string. It then uses this code to call the Auth0 API to get the access token, user profile and refresh token. The access token and profile are saved in variables while the refresh token is saved in the keychain.
6. `logout`: This method deletes the refresh token from the keychain and sets the profile, access token and refresh token variables to `null`.

All these methods are then returned from the module for public access.

Next, create another file in the `src/main/services` folder named `app-process.js` and paste in the code below:

```javascript

import { BrowserWindow } from "electron";


let mainWindow;
const winURL =
  process.env.NODE_ENV === "development"
    ? `http://localhost:9080`
    : `file://${__dirname}/index.html`;

function createAppWindow() {
  /**
   * Initial window options
   */
  mainWindow = new BrowserWindow({
    height: 563,
    useContentSize: true,
    width: 1000
  });

  mainWindow.loadURL(winURL);

  mainWindow.on("closed", () => {
    mainWindow = null;
  });
}


export default createAppWindow;


```

This module basically exports a method that creates a window that loads the homepage of our Vue application. Based on the environment in which our application is running in, it either returns the development URL from `localhost` or the file URL for the application's `index.html` file.

Now, create a third file in the same directory named `auth-process.js` and paste in the code below:

```javascript

import { BrowserWindow } from "electron";
import authService from "./auth-service";
import createAppWindow from "./app-process";

let win = null;

function createAuthWindow() {
  destroyAuthWin();

  // Create the browser window.
  win = new BrowserWindow({
    width: 1000,
    height: 600,
    webPreferences: {
      nodeIntegration: false
    }
  });

  win.loadURL(authService.getAuthenticationURL());

  const {
    session: { webRequest }
  } = win.webContents;

  const filter = {
    urls: ["file:///callback*"]
  };

  webRequest.onBeforeRequest(filter, async ({ url }) => {
    await authService.loadTokens(url);
    createAppWindow();
    return destroyAuthWin();
  });

  win.on("authenticated", () => {
    destroyAuthWin();
  });

  win.on("closed", () => {
    win = null;
  });
}

function destroyAuthWin() {
  if (!win) return;
  win.close();
  win = null;
}

export default createAuthWindow;



```

This module brings in the two modules earlier created and exports a function that handles the authentication process.

This module contains two methods, one to create the authentication window and another to destroy it.

The `createAuthWindow` starts by destroying any existing authentication window and goes ahead to create an authentication window by loading the Auth0 authentication URL from the `auth-service.js` module. It then calls the `onBeforeRequest` method of the `webRequest` property of Electronjs `Session` class to call the `loadTokens` method which takes in the callback URL from Auth0's authentication process.

Once the promise resolves, the `createAppWindow` method is then called to load our Vue application and lastly, the authentication window is destroyed by calling `destroyAuthWin`.

This method also registers event handlers to handle when the application window is closed and when an `authenticated` event is emitted.

The last thing to do in the main process is to tie everything together in the `src/main/index.js` file which is the entry point for our application's main process.

Replace the content of the `index.js` file with the code below:

```javascript

import { app, BrowserWindow } from "electron";
import createAuthWindow from "./services/auth-process";
import createAppWindow from "./services/app-process";
import authService from "./services/auth-service";

if (process.env.NODE_ENV !== "development") {
  global.__static = require("path")
    .join(__dirname, "/static")
    .replace(/\\/g, "\\\\");
}

let mainWindow;
const winURL =
  process.env.NODE_ENV === "development"
    ? `http://localhost:9080`
    : `file://${__dirname}/index.html`;



async function showWindow() {
  try {
    await authService.refreshTokens();
    
    return createAppWindow();
  } catch (err) {
    createAuthWindow();
  }
}

app.on("ready", showWindow);

app.on("window-all-closed", () => {
  if (process.platform !== "darwin") {
    app.quit();
  }
});

app.on("activate", () => {
  if (mainWindow === null) {
    showWindow();
  }
});

```

The main point of focus here is the `showWindow` method. This method tries to refresh the tokens as a check to see if an authenticated session is in place. If true, the tokens are successfully refreshed and the Vue application is loaded with access tokens to make authenticated API calls. If false, an error is thrown which is caught and reacted to by loading the Auth0 authentication page in order for the user to sign in.

The `showWindow` is set as the handler for our Electronjs application's `ready` event.

Now run the application using `npm run dev`. If you have done everything correctly you should see a screen similar to the one below:

![Auth0 Login Screen](images/auth0-screen.png)


If you do not see this screen or get an error, simply trace your steps back along the article and also ensure that your Auth0 credentials in your `env.json` file are correct.

If you get the versioning error between your `keytar` and Nodejs version, apply the fix stated at the start of this section after installing the required packages.


Now to the final piece of the application. You will now write your frontend to make authenticated calls to the backend API (The To-Dos API) which is now protected.

You will also be adding some little styling with bootstrap.

Simply go into the `src/renderer/components` folder and replace the contents of the `ToDos.vue` component with the code below:

```javascript

<template>
  <div>
    <div class="row">
      <header class="col-md-12">
        <nav class="navbar navbar-light bg-light">
          <a class="navbar-brand">Vue TODO List</a>
          <button class="btn btn-danger my-2 my-sm-0" @click="logout()" type="button">Logout</button>
        </nav>
      </header>
    </div>

    <div class="row" id="fetch-button-row">
      <div class="col-md-12">
        <button @click="fetchTodos()" class="btn btn-primary">Fetch Todos</button>
      </div>
      
    </div>

    <div class="row" id="todos-row">
      <div class="col-md-12">
        <ul class="list-group">
          <li class="list-group-item" v-for="todo in todos" :key="todo.id">{{todo.task}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
const { remote } = window.require("electron");
const axios = require("axios"); 
import authService from "../../main/services/auth-service";
export default {
  name: "HelloWorld",
  data: () => {
    return {
      todos: []
    };
  },
  
  methods: {
    logout() {
      authService.logout();
      remote.getCurrentWindow().close();
    },
    async fetchTodos() {
      console.log(authService.getAccessToken());

      let accessToken = await authService.refreshTokens();

      console.log(accessToken);

      axios
        .get("http://localhost:1337/todos", {
          headers: {
            Authorization: `Bearer ${accessToken}`
          }
        })
        .then(response => {
          console.log(response);
          this.todos = response.data;
          console.log(this.todos);
        })
        .catch(error => {
          if (error) throw new Error(error);
        });
    }
  }
};
</script>


<style scoped>
@import "~bootstrap/dist/css/bootstrap.min.css";

#fetch-button-row,
#todos-row {
  margin: 10px;
}
</style>




```

In the above code, the `fetchTodos` method is refactored to get the access token and use it to make an authenticated call to the backend API setting the `Authorization` header to make use of the access token.

Some bootstrap classes and layout structure were also added to give the page a simple facelift.

A **Log Out** functionality has also been added to the application.

Now run this application again using `npm run dev`. This time you should be able to log in successfully and fetch the To-Dos list from the backend (If your keychain tool prompts you to allow the application to access the keychain, simply click **Allow** or **Always Allow**). 

Your screen should then look like the one below:



![Final Result](images/new-todo-list.png)


## Conclusion

As seen in this article, creating native desktop experiences with web technologies is a breeze when you use the right tools, and the `electron-vue` package enables developers to get the best of both worlds of Vue, a fast-rising developer friendly frontend framework, and Electronjs.
