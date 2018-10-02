---
layout: post
title: NgRx Authentication Tutorial
metatitle: How to Add Auth0 Authentication to Angular App in an NgRx Project
description: Learn how to use CLI schematics, effects, Auth0, and more to secure your NgRx application.
metadescription: Learn how to add auth to Angular app using NgRx schematics. See how to define authentication state and set up effects to control your login flow, while Auth0 handles authentication.
date: 2018-10-02 08:50
category: Technical Guide, Authentication, NgRx
post_length: 5
author:
  name: Sam Julien
  url: http://twitter.com/samjulien
  mail: sam.julien@auth0.com
  avatar: https://pbs.twimg.com/profile_images/1042806646546739200/WjQvIURg_400x400.jpg
design:
  bg_color: "#2f333b"
  image: https://cdn.auth0.com/blog/logos/ngrx.png
tags:
- angular
- ngrx
- authentication
- spa
- tutorial
- auth0
related:
- 2016-09-29-angular-2-authentication
- 2017-03-07-managing-state-in-angular-with-ngrx-store
- 2018-07-10-upgrade-your-angularjs-authentication-service
---

**TL;DR:** In this article, we’ll get a quick refresher on NgRx basics and get up to speed on more features of the NgRx ecosystem. We'll then walk through how to add Auth0 authentication to an NgRx app. You can access the finished code for this tutorial on the [ngrx-auth GitHub repository](https://github.com/auth0-blog/ngrx-auth).

## NgRx: From Humble Beginnings to a New Ecosystem

I remember when [Rob Wormald](https://twitter.com/robwormald), an Angular Core Team member, [first wrote ngrx/store](https://github.com/ngrx/store/commit/c7c562cc447416ce6c386b1522f301064af43a95) at the end of 2015. It was a tiny library implementing Redux in Angular (`store.ts` was a single file with 66 lines of code!). Fast forward a few years and [NgRx](https://github.com/ngrx) is much more than a simple library — it’s an entire ecosystem with over 120 contributors! Learning to think in the Redux pattern, while also keeping up with the various structures and pieces of NgRx, can be a challenge at first. Don’t worry, though &mdash; we’ve got your back.

In this article, we’ll do a quick refresher on NgRx concepts and learn about what has changed in its ecosystem in the last year. Then, we’ll look at how authentication works in NgRx and how to add Auth0 to an NgRx application.

Let’s get started!

{% include tweet_quote.html quote_text="I'm learning about adding authentication to an NgRx application." %}

## Basics of NgRx - A Recap

While we have a great tutorial [on managing state with `ngrx/store`](https://auth0.com/blog/managing-state-in-angular-with-ngrx-store/) and implementing Auth0, a lot has changed with NgRx since then. Let’s do a quick recap of what hasn't changed &mdash; the fundamental concepts of NgRx and the Redux pattern.

**State** is an immutable data structure describing the current status of parts of your application. This could be in the UI or in the business logic. State can represent a range of different things, from whether to show a navigation menu to whether a user is logged in.

You can access that state through the **store**. You can think of the store as the main brain of your application. It’s the hub of anything that needs to change. Since we’re using Angular, the store is implemented using an [RxJS subject](https://rxjs-dev.firebaseapp.com/guide/subject), which basically means it’s both an observable and an observer. The store is an observable of state, which you can subscribe to, but what is it observing?

The store is an observer of **actions**. Actions are information payloads that describe state changes. You send (or **dispatch**) actions to the store, and that’s the only way the store receives data or instructions on how to update your application’s state. An action has a _type_ (like “Add Book”) and an optional **payload** (like an object containing the title and description for the book **Return of the King**).

Actions are the bedrock of an NgRx application, so it’s important to follow best practices with them. Here are some pro tips on actions from Mike Ryan’s ng-conf talk [Good Action Hygiene](https://www.youtube.com/watch?v=JmnsEvoy-gY):

- Write your actions first. This lets you plan out the different use cases of your application before you write too much code.
- Don’t reuse actions or use action sub-types. Instead, focus on clarity over brevity. Be specific with your actions and use descriptive action types. Use actions like `[Login Page] Load User Profile` and `[Feature Page] Load User Profile`. You can then use the switch statement in your reducer to modify the state in the same way for multiple actions.
- Remember: good actions are actions you can read after a year and tell where they are being dispatched.

Once the store receives an action to change the state, pure functions called **reducers** use those actions along with the previous state to compute the new state.

If you’re feeling lost on the basics of the Redux pattern, one of my favorite introductory resources is Dan Abramov’s [Egghead course on getting started with Redux](https://egghead.io/courses/getting-started-with-redux).

## Going Further with NgRx

With the basics covered, let’s look at some additional concepts in NgRx we'll need for this tutorial.

### Selectors
Selectors are pure functions that are used to get simple and complex pieces of state. They act as queries to your store and can make your life a lot easier. Instead of having to hold onto different filtered versions of data in your store, you can just use selectors.

For example, to get your current user, you’d set up a selector by first exporting this function:

```typescript
export const selectAuthUser = (state: AuthState) => state.user;
```

You typically put these pure functions in the same file as your feature’s reducer functions and then register both the feature selectors and individual state piece selectors in the `index.ts` file where you register your reducers with NgRx. For example:

```typescript
// state.index.ts
// Feature selectors get registered first.
export const selectAuthState = createFeatureSelector<fromAuth.State>('auth');

// Then you can use that feature selector to select a piece of state.
export const selectAuthUser = createSelector(
  selectAuthState,
  fromAuth.selectUser, // the pure function that returns the user from our auth state
);
```

You can then easily use that selector in your component or your route guards:

```typescript
this.user = this.store.pipe(select(fromAuth.selectAuthUser));
```

To learn more, check out [this talk from ng-conf 2018 on selectors by David East and Todd Motto](https://www.youtube.com/watch?v=Y4McLi9scfc).

### Effects
Effects (used with the library [`@ngrx/effects`](https://github.com/ngrx/platform/tree/master/modules/effects)) are where you connect actions to side effects or external requests. Effects can listen for actions, then perform a side effect. That effect can then (optionally) dispatch a new action to the store. For example, if you need to load data from an API, you might dispatch a `LOAD_DATA` action. That action could trigger an effect that calls your API and then dispatches either a `LOAD_DATA_SUCCESS` or `LOAD_DATA_FAILURE` action to handle the result. Here’s how that looks:

```typescript
@Effect()
loadData$ = this.actions$.ofType(LOAD_DATA)
  .pipe(switchMap(() => {
    return this.apiService.getData().pipe(
      map(data => new LOAD_DATA_SUCCESS(data)),
      catchError(error => of(new LOAD_DATA_FAILURE(error)))
    );
});
```

You can see that the effect is listening for the `LOAD_DATA` action, and then calls the `ApiService` to get the data and return a new action.

Using effects ensures that your reducers aren’t containing too many implementation details and that your state isn’t full of temporary clutter. For more information, check out [this tutorial from Todd Motto on using effects](https://www.youtube.com/watch?v=xkUOQeGqIGI).

### NgRx Schematics
One of the most common complaints about NgRx is the amount of repetitive code that it takes to set up an application and add new features (never say "boilerplate" around NgRx Core Team member [Brandon Roberts](https://twitter.com/brandontroberts)!). Part of this is due to the NgRx philosophy to focus on clarity over brevity, and to front-load the work of app architecture so that future feature additions are easy and straightforward. At the same time, there can be a lot of repetition in the code you write — adding a new reducer, set of actions, effects, and selectors for each new feature. Luckily, Angular CLI *schematics* are here to help.

Schematics for the Angular CLI are blueprints to generate new code quickly and are a huge time-saver. NgRx has its own set of schematics that you can take advantage of when writing your application. These schematics follow best practices and cut out lots of repetitive tasks. They also automatically register your feature states, wire up your effects, and integrate into NgModules. Awesome!

There are NgRx schematics for:

- [Action](https://github.com/ngrx/platform/blob/master/docs/schematics/action.md)
- [Container](https://github.com/ngrx/platform/blob/master/docs/schematics/container.md)
- [Effect](https://github.com/ngrx/platform/blob/master/docs/schematics/effect.md)
- [Entity](https://github.com/ngrx/platform/blob/master/docs/schematics/entity.md)
- [Feature](https://github.com/ngrx/platform/blob/master/docs/schematics/feature.md) (generate an action, reducer, and an effect and automatically wire them up to a module!)
- [Reducer](https://github.com/ngrx/platform/blob/master/docs/schematics/reducer.md)
- [Store](https://github.com/ngrx/platform/blob/master/docs/schematics/store.md)

To get started with schematics, first install them in your project:

```bash
npm install @ngrx/schematics --save-dev
```

Then, set the NgRx schematics as your defaults in your CLI configuration:

```bash
ng config cli.defaultCollection @ngrx/schematics
```

Once that’s done, you can simply run CLI commands to generate new NgRx items. For example, to generate the initial state for your application, you can run:

```bash
ng generate store --name State --root -m app
```

This will generate a store for you at the root of your application and register it in your `AppModule`. Pretty cool!

For more information on NgRx schematics, check out the [repository readme](https://github.com/ngrx/platform/blob/master/docs/schematics/README.md) and [this excellent ng-conf talk](https://www.youtube.com/watch?v=q3UcqG72Zl4&t=1031s) by Vitalii Bobrov.

## Adding Authentication to an NgRx Project

We’re caught up on the latest and greatest with NgRx, so let’s learn how to implement authentication in NgRx.

Authentication is a perfect example of shared state in an application. Authentication affects everything from being able to access client-side routes, get data from private API endpoints, and even what UI a user might see. It can be incredibly frustrating to keep track of authentication state in all these places. Luckily, this is exactly the kind of problem NgRx solves. Let’s walk through the steps. (We’ll be following the best practices Brandon Roberts describes in his ng-conf 2018 talk [Authentication in NgRx](https://www.youtube.com/watch?v=46IRQgNtCGw).)

{% include tweet_quote.html quote_text="I'm learning how to use schematics, selectors, and effects to add authentication to an NgRx application." %}

### Our Sample App
We’re going to use a heavily simplified version of the official [ngrx/platform book collection app](https://github.com/ngrx/platform/tree/master/projects/example-app) to learn the basics of authentication in NgRx. To save time, most of the app is done, but we’ll be adding Auth0 to protect our book collection through an Auth0 login page.

You can access the code for this tutorial at the [Auth0 Blog repository](https://github.com/auth0-blog/ngrx-auth). To get started with the application, you'll need to clone the app, install the dependencies, and check out the "Starting point" commit in order to follow along. You can also run the application using the Angular CLI. You can do all of that with these commands:

```bash
git clone https://github.com/auth0-blog/ngrx-auth.git
cd ngrx-auth
git checkout 23c1b25
npm install
ng serve
```

You can now visit `http://localhost:4200` to see the running application.

We'll also be taking advantage of NgRx schematics to help us set up authentication in this app, so I've added them to the project and set them as the default.

Our app currently has the book library (found in the `books` folder), as well as some basic setup of NgRx already done. We also have the very beginnings of the `AuthModule`. The `UserHomeComponent` just lets us go to the book collection. This is what we'll protect with authentication.

The first phase of this task will be the basic scaffolding of the authentication state, reducer, and selectors. Then, we’ll add Auth0 to the application, create our UI components, and finally set up effects to make it all work.

### Two Quick Asides
Before we get started, I need to make a couple of side notes.

First, because this article is focused on how to set up authentication in an NgRx app, we're going to abstract away and over-simplify things that aren't specific to NgRx. For example, we won't discuss tying the book collection to a specific user or setting up authentication on the back end. Check out our [tutorial on Angular authentication](https://auth0.com/blog/angular-2-authentication/) to learn more about those things. Instead, you'll learn the mechanics of adding the necessary state setup, adding actions and reducers to handle the flow of logging in, and using effects to retrieve and process a token.

Second, almost all NgRx tutorials need a caveat. In the real world, if you had an application this simple, you most likely wouldn't use NgRx. NgRx is extremely powerful, but the setup and learning curve involved prevent it from being a fit for every application, especially super simple ones. However, it's tough to learn these concepts using a large and complex sample application. My goal in this article is to focus only on teaching the core concepts of NgRx authentication so that you can apply them to your own larger application. For more on this subject, see Dan Abramov's iconic article [You Might Not Need Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367).


## Define Global Authentication State

The first step to adding authentication to an NgRx app is to define the piece of your state related to authentication. Do you need to keep track of a user profile, or whether there is a token stored in memory? Often the presence of a token or profile is enough to derive a “logged in” state. In any case, this is what you’ll want to attach to your main state.

We’re going to keep it very simple for this example and just detect the presence of a valid token. If we have a valid token, we'll toggle a global piece of state called `isLoggedIn`.

To get started, let’s use a schematic to create a reducer in the main `state` folder:

```bash
ng g reducer state/auth --no-spec
```

This is where we’ll define our authentication state, reducer, and selector functions. We can go ahead and update the state interface and initial state like this:

```typescript
// src/app/state/auth.reducer.ts
// ...

// Leave everything else and update the following:
export interface State {
  isLoggedIn: boolean;
}

export const initialState: State = {
  isLoggedIn: false
};

// ...
```

We'll come back to the reducer in this file once we've defined our actions in the next section.

Next, import everything from the auth reducer file into the `index.ts` file. That way we can add the authentication state to the overall application state and the authentication reducer to our action reducer map. The complete file will look like this right now:

```typescript
// src/app/state/index.ts
import {
  ActionReducerMap,
  createFeatureSelector,
  createSelector,
  MetaReducer
} from '@ngrx/store';
import { environment } from '../../environments/environment';
import * as fromAuth from './auth.reducer';

export interface State {
  auth: fromAuth.State;
}

export const reducers: ActionReducerMap<State> = {
  auth: fromAuth.reducer
};

export const metaReducers: MetaReducer<State>[] = !environment.production
  ? []
  : [];
```

In a more complex application, you’ll need to think through the state of other pages that use authentication in addition to your overall authentication state. For example, it’s common for login pages to need pending or error states. In our example, we’ll be using Auth0’s [login page](https://auth0.com/docs/hosted-pages/login), which will redirect our application to a login screen to authenticate, then back to the application. Because of this, we won’t need to keep track of our login page state in this example.

### Define Authentication Actions
Before we write our authentication reducer, let’s take Mike Ryan’s advice and write our actions first. Let’s walk through the flow of authentication in our example:

1. User clicks the “Log In” button to trigger the Auth0 login screen.
2. After going through the Auth0 authentication process, the user will be redirected to a `callback` route in our application.
3. That `CallbackComponent` will need to trigger an action to parse the hash fragment and set the user session. (Hint: this is where we’re altering our application state.)
4. Once that’s done, the user should be redirected to a `home` route.
5. If the user refreshes while logged in, the authentication state should persist.

I can identify several actions here:

- **Login** — to trigger the Auth0 login screen.
- **LoginComplete** — to handle the Auth0 callback.
- **LoginSuccess** — to update our authentication state `isLoggedIn` to `true` and navigate to the `home` route.
- **LoginFailure** — to handle errors.
- **CheckLogin** - to see if the user is still logged in on the server and update the state accordingly.

Notice that only the **LoginSuccess** action will modify our application state, which means that one will need to be in our reducers. The rest of these actions will use effects.

The logout process is similar:

1. User clicks “Log Out” button to trigger a confirmation dialog.
2. If the user clicks “Cancel,” the dialog will close.
3. If the user clicks “Okay,” we’ll trigger the Auth0 logout process.
4. Once logged out, Auth0 will redirect the user back to the application, which should default to the `login` route when not authenticated.

Can you think of what actions we’ll need? I spotted these:

- **Logout** — to trigger the logout confirmation dialog
- **LogoutCancelled** — to close the logout dialog.
- **LogoutConfirmed** — to tell Auth0 to log out and redirect home.

We’ll use the **LogoutConfirmed** action to reset our authentication state in a reducer in addition to telling Auth0 to log out. The rest will be handled with effects.

### Add the Auth NgRx Feature
We've defined our actions and identified which will be handled by the reducer and which will be handled with effects. We'll need to add some new files to the `AuthModule` and wire them up to our main application. Luckily, we can use schematics to make this much easier for us:

```bash
ng g feature auth/auth --group --no-spec --module auth
```

Let's break this command down to understand what's happening:
- `g` is short for `generate` in the CLI
- `feature` is the NgRx schematic that generates actions, effects, and reducers for a new feature
- `auth/auth` tells the CLI to put these files under the `auth` folder and name them `auth`
- `group` is an NgRx schematic option that groups all the files under respective folders (e.g. actions go in an `actions` folder)
- `no-spec` skips generation of test files
- Finally, `module auth` wires up the feature in the `AuthModule`

We should end up with `actions`, `effects`, and `reducers` folders inside of `src/app/auth` with corresponding TypeScript files prefixed with `auth` (e.g. `auth.actions.ts`).

We actually won't be using the module-specific `reducers`, so we can delete that file and folder (`src/app/auth/reducers`) and the references to the auth reducer in `auth.module.ts`. We'll need to delete the import of the reducer on line 9 and the reducer registration from the `imports` array on line 22:

```typescript
// src/app/auth/auth.module.ts
import * as fromAuth from './reducers/auth.reducer'; // remove this line
// ...
StoreModule.forFeature(‘auth’, fromAuth.reducer), // remove this line
// ...
```

It's helpful to stop and re-run `ng serve` to prevent getting some errors at this stage.

### Adding the Auth Actions
Let's create the actions we defined earlier in our newly created `/auth/auth.actions.ts` file. We can delete the generated code, but we’ll follow the same pattern in our code: creating an enum with the action type strings, defining each action and optional payload, and finally defining an `AuthActions` type that we can use throughout the application. The finished result will look like this:

```typescript
// src/app/auth/actions/auth.actions.ts
// Finished:
import { Action } from '@ngrx/store';

export enum AuthActionTypes {
  Login = '[Login Page] Login',
  LoginComplete = '[Login Page] Login Complete',
  LoginSuccess = '[Auth API] Login Success',
  LoginFailure = '[Auth API] Login Failure',
  CheckLogin = '[Auth] Check Login',
  Logout = '[Auth] Confirm Logout',
  LogoutCancelled = '[Auth] Logout Cancelled',
  LogoutConfirmed = '[Auth] Logout Confirmed'
}

export class Login implements Action {
  readonly type = AuthActionTypes.Login;
}

export class LoginComplete implements Action {
  readonly type = AuthActionTypes.LoginComplete;
}

export class LoginSuccess implements Action {
  readonly type = AuthActionTypes.LoginSuccess;
}

export class LoginFailure implements Action {
  readonly type = AuthActionTypes.LoginFailure;

  constructor(public payload: any) {}
}

export class CheckLogin implements Action {
  readonly type = AuthActionTypes.CheckLogin;
}

export class Logout implements Action {
  readonly type = AuthActionTypes.Logout;
}

export class LogoutConfirmed implements Action {
  readonly type = AuthActionTypes.LogoutConfirmed;
}

export class LogoutCancelled implements Action {
  readonly type = AuthActionTypes.LogoutCancelled;
}

export type AuthActions =
  | Login
  | LoginComplete
  | LoginSuccess
  | LoginFailure
  | CheckLogin
  | Logout
  | LogoutCancelled
  | LogoutConfirmed;
```

You can see that, in our example, we’re only using a payload for the `LoginFailure` action to pass in an error message. If we were using a user profile, we’d need to define a payload in `LoginComplete` in order to handle it in the reducer. Instead, we'll just be handling the token through an effect and an `AuthService` we'll create later.

Do you notice how thinking through the actions in a feature also helps us identify our use cases? This keeps us from cluttering our reducers and application state because we shift most of the heavy lifting to effects.

> Quick aside: if you'd like your application to continue to build at this step, you'll need to comment out the generated code in `auth.effects.ts`, since it now references the deleted schematic-generated action. Comment out lines 8 and 9:

```typescript
// src/app/auth/effects/auth.effects.ts

// ...

// @Effect()
// loadFoos$ = this.actions$.pipe(ofType(AuthActionTypes.LoadAuths));

// ...
```

### Define Authentication Reducer

Now let’s circle back to authentication reducer (`state/auth.reducer.ts`). Since we’ve figured out what our actions are, we know that we’ll only need to change the state of our application when the login is successful and when the logout dialog is confirmed.

First, import the `AuthActions` and `AuthActionTypes` at the top of the file so we can use them in our reducer:

```typescript
import { AuthActions, AuthActionTypes } from '@app/auth/actions/auth.actions';
```

Next, replace the current reducer function with this:

```typescript
// /state/auth.reducer.ts
export function reducer(state = initialState, action: AuthActions): State {
  switch (action.type) {
    case AuthActionTypes.LoginSuccess:
      return { ...state, isLoggedIn: true };

    case AuthActionTypes.LogoutConfirmed:
      return initialState; // the initial state has isLoggedIn set to false

    default:
      return state;
  }
}
```

Notice that the `LoginSuccess` case toggles the global `isLoggedIn` state to `true`, while the `LogoutConfirmed` case returns us to our initial state, where `isLoggedIn` is `false`.

We’ll handle the rest of our actions using effects later on.

### Define Authentication Selector
We've defined our `isLoggedIn` state, actions related to our login process, and a reducer to modify our application state. But how do we access the status of `isLoggedIn` throughout our application? For example, we’ll need to know whether we're authenticated in a route guard to control access to the `home` and `books` routes.

This is exactly what **selectors** are for. To create a selector, we’ll first define the pure function in `/state/auth.reducer.ts` and then register the selector in `index.ts`. Underneath our reducer function, we can add this pure function:

```typescript
// src/app/state/auth.reducer.ts
// ...all previous code remains the same
export const selectIsLoggedIn = (state: State) => state.isLoggedIn;
```

We can then define our selectors at the bottom of `state/index.ts`:

```typescript
// src/app/state/index.ts
// ...all previous code remains the same
export const selectAuthState = createFeatureSelector<fromAuth.State>('auth');
export const selectIsLoggedIn = createSelector(
  selectAuthState,
  fromAuth.selectIsLoggedIn
);
```

We’ve now got all the basic scaffolding set up for an authenticated state. Let's start setting up our authentication process.

## Adding Authentication with Auth0
In this section, we're going to set up Auth0, create an Angular authentication service, and wire everything up using NgRx Effects. The Auth0 log in screen will look like this:

![Auth0 default login screen](https://cdn.auth0.com/blog/resources/auth0-centralized-login.jpg)

### Sign Up for Auth0
The first thing you'll need to do is sign up for an Auth0 account to manage authentication. You can <a href="https://auth0.com/signup" data-amp-replace="CLIENT_ID" data-amp-addparams="anonId=CLIENT_ID(cid-scope-cookie-fallback-name)">sign up for a free Auth0 account here</a>. (If you've already got an account, great! You can simply log in to Auth0.)

### Set Up an Application
Once you've got your account, you can set up an application to use with our NgRx project. We'll only be setting up a Single Page Application (SPA) in Auth0 since we're using the Google Books API as our back end.

Here's how to set that up:

1. Go to your [**Auth0 Applications**](https://manage.auth0.com/#/applications) and click the "Create Application" button.
2. Name your new app, select "Single Page Web Applications," and click the "Create" button. You can skip the Quick Start and click on **Settings**.
3. In the **Settings** for your new Auth0 app, add `http://localhost:4200/callback` to the **Allowed Callback URLs**. (We're using `localhost:4200` since it's the default port for the Angular CLI `serve` command.)
4. Add `http://localhost:4200` to both **Allowed Web Origins** and **Allowed Logout URLs**.
5. Click the "Save Changes" button.
6. Copy down your **Domain** and **Client ID**. We'll use them in just a minute.
7. If you'd like, you can [set up some social connections](https://manage.auth0.com/#/connections/social). You can then enable them for your app in the **Application** options under the **Connections** tab. The example shown in the screenshot above utilizes username/password database, Facebook, Google, and Twitter.

> **Note:** Under the **OAuth** tab of **Advanced Settings** (at the bottom of the **Settings** section) you should see that the **JsonWebToken Signature Algorithm** is set to `RS256`. This is the default for new applications. If it is set to `HS256`, please change it to `RS256`. You can [read more about RS256 vs. HS256 JWT signing algorithms here](https://community.auth0.com/questions/6942/jwt-signing-algorithms-rs256-vs-hs256).

### Install auth0.js and Set Up Environment Config
Now that we've got the SPA authentication set up, we need to add the JavaScript SDK that allows us to easily interact with Auth0. We can install that with this command:

```bash
npm install auth0-js --save
```

We'll use this library in just a bit when we create our authentication service. We can now set up our environment variables using the client ID and domain we copied from our Auth0 application settings. The Angular CLI makes this very easy. Open up `/src/environments/environment.ts` and add the `auth` section below:

```typescript
// src/environments/environment.ts
export const environment = {
  production: false,
  auth: {
    clientID: 'YOUR-AUTH0-CLIENT-ID',
    domain: 'YOUR-AUTH0-DOMAIN', // e.g., you.auth0.com
    redirect: 'http://localhost:4200/callback',
    scope: 'openid profile email'
  }
};
```

> Note: if we were using an API identifier, we would add an `audience` property to this configuration. We're leaving it out here since we're using the Google Books API.

This file stores the authentication configuration variables so we can re-use them throughout our application by importing them. Be sure to update the `YOUR-AUTH0-CLIENT-ID` and `YOUR-AUTH0-DOMAIN` to your own information from your Auth0 application settings.

### Add Authentication Service

We're now ready to set up the main engine of authentication for our application: the authentication service. The authentication service is where we’ll handle interaction with the Auth0 library. It will be responsible for anything related to getting the token from Auth0, but won’t dispatch any actions to the store.

To generate the service using the CLI, run:

```bash
ng g service auth/services/auth --no-spec
```

We first need to import the `auth0-js` library, our environment configuration, and `bindNodeCallback` from RxJS:

```typescript
// src/app/auth/services/auth.service.ts
// Add these to the imports
import { Injectable } from '@angular/core';
import { bindNodeCallback } from 'rxjs';
import * as auth0 from 'auth0-js';
import { environment } from '../../../environments/environment';

// ...below code remains the same
```

Next, we need to set some properties on our class. We'll need an Auth0 configuration property, a flag we'll use for setting a property in local storage to persist our authentication on refresh, and some URLs for navigation. We'll also add a property for setting the token expiration date. Add these before the class `constructor`:

```typescript
// src/app/auth/services/auth.service.ts
// ...previous code remains the same

// Add properties above the constructor
private _Auth0 = new auth0.WebAuth({
    clientID: environment.auth.clientID,
    domain: environment.auth.domain,
    responseType: 'token',
    redirectUri: environment.auth.redirect,
    scope: environment.auth.scope
  });
// Track whether or not to renew token
private _authFlag = 'isLoggedIn';
// Authentication navigation
private _onAuthSuccessUrl = '/home';
private _onAuthFailureUrl = '/login';
private _logoutUrl = 'http://localhost:4200';
private _expiresAt: number;

// ...below code remains the same
```

We're setting the different URLs here in the service in case multiple places in the application need to perform this redirection.

Next, we'll need to set up two observables using the Auth0 library that we can access with our NgRx effects later on. The Auth0 library uses Node-style callback functions. Luckily, we can use `bindNodeCallback`, built into RxJS, to transform these functions into observables. Add these lines after the properties we just created but before the constructor:

```typescript
// src/app/auth/services/auth.service.ts
// ...above code unchanged
// Create observable of Auth0 parseHash method to gather auth results
parseHash$ = bindNodeCallback(this._Auth0.parseHash.bind(this._Auth0));

// Create observable of Auth0 checkSession method to
// verify authorization server session and renew tokens
checkSession$ = bindNodeCallback(this._Auth0.checkSession.bind(this._Auth0));
// ...below code unchanged
```

The first observable we've created is using Auth0's `parseHash` function, which will parse the access token from the window hash fragment. We'll use this during `LoginComplete`. The second observable is for the `checkSession` function, which we'll use when the app refreshes during `CheckLogin`. You'll notice that both of these have a little bit of JavaScript magic tacked onto them. We need to `bind` the Auth0 object to both of these functions for them to work correctly. This is pretty common when using `bindNodeCallback`.

We'll now need functions to handle logging in, setting the authentication, and logging out, as well as a public getters for our URLs and whether we're logged in. We'll use a flag in local storage to track that the application is authenticated. That way we can check for that flag and call `checkSession` to update our state.

Update the service like so after the constructor:

```typescript
// src/app/auth/services/auth.service.ts
// ...previous code remains the same
// Add these functions after the constructor
get authSuccessUrl(): string {
  return this._onAuthSuccessUrl;
}

get authFailureUrl(): string {
  return this._onAuthFailureUrl;
}

get authenticated(): boolean {
  return JSON.parse(localStorage.getItem(this._authFlag));
}

resetAuthFlag() {
  localStorage.removeItem(this._authFlag);
}

login() {
  this._Auth0.authorize();
}

setAuth(authResult) {
  this._expiresAt = authResult.expiresIn * 1000 + Date.now();
  // Set flag in local storage stating this app is logged in
  localStorage.setItem(this._authFlag, JSON.stringify(true));
}

logout() {
  // Set authentication status flag in local storage to false
  localStorage.setItem(this._authFlag, JSON.stringify(false));
  // This does a refresh and redirects back to homepage
  // Make sure you have the logout URL in your Auth0
  // Dashboard Application settings in Allowed Logout URLs
  this._Auth0.logout({
    returnTo: this._logoutUrl,
    clientID: environment.auth.clientID
  });
}

// ...below code remains the same
```

Auth0 is handling logging in and logging out, and we have a `setAuth` method to set the local storage flag. If we needed to return the token for use in the store, we'd do that here. We're going to handle much of the flow of our authentication using effects. Before we set those up, though, we're going to need some components.

## Build Out the Authentication UI
We've got the authentication service set up, but now we need to build out our UI. We'll need components for logging in, a callback component for Auth0 to redirect to, a logout dialog, and a logout button on our user home screen. We'll also need to add some routing, and we'll want to add a route guard to lock down our `home` route and redirect users back to the `login` route if no token is found.

### Log In Components
We need to create a `login` route with a simple form that contains a button to log in. This button will dispatch the `Login` action. We'll set up an effect in just a bit that will call the authentication service.

First, create a container component called `login-page`:

```bash
ng g c auth/components/login-page -m auth --no-spec
```

Replace the boilerplate with this:

```typescript
// src/app/auth/components/login-page.component.ts
import { Component, OnInit } from '@angular/core';
import { Store } from '@ngrx/store';
import * as fromStore from '@app/state';
import { Login } from '@app/auth/actions/auth.actions';

@Component({
  selector: 'abl-login-page',
  template: `
    <abl-login-form
      (submitted)="onLogin($event)">
    </abl-login-form>
  `,
  styles: [
    `
      :host {
        display: flex;
        flex-direction: column;
        align-items: center;
        padding: 128px 12px 12px 12px;
      }

      abl-login-form {
        width: 100%;
        min-width: 250px;
        max-width: 300px;
      }
    `
  ]
})
export class LoginPageComponent implements OnInit {
  constructor(private store: Store<fromStore.State>) {}

  ngOnInit() {}

  onLogin() {
    this.store.dispatch(new Login());
  }
}
```

Notice that we'll be passing the `onLogin` function into our form, which will dispatch our action.

If you see an error when running the application right now, it's because we haven't yet created `abl-login-form` referenced in the template. Let's create that component now:

```bash
ng g c auth/components/login-form -m auth --no-spec
```

Now replace the contents of that file with this:

```typescript
// src/app/auth/components/login-form.component.ts
import { Component, OnInit, EventEmitter, Output } from '@angular/core';
import { FormGroup } from '@angular/forms';

@Component({
  selector: 'abl-login-form',
  template: `
    <mat-card>
      <mat-card-title>
        Welcome
      </mat-card-title>
      <mat-card-content>
        <form [formGroup]="loginForm" (ngSubmit)="submit()">
          <div class="loginButtons">
            <button type="submit" mat-button>Log In</button>
          </div>
        </form>
      </mat-card-content>
    </mat-card>
  `,
  styles: [
    `
      :host {
        width: 100%;
      }

      form {
        display: flex;
        flex-direction: column;
        width: 100%;
      }

      mat-card-title,
      mat-card-content {
        display: flex;
        justify-content: center;
      }

      mat-form-field {
        width: 100%;
        margin-bottom: 8px;
      }

      .loginButtons {
        display: flex;
        flex-direction: row;
        justify-content: flex-end;
      }
    `
  ]
})
export class LoginFormComponent implements OnInit {
  @Output() submitted = new EventEmitter<any>();

  loginForm = new FormGroup({});

  ngOnInit() {}

  submit() {
    this.submitted.emit();
  }
}
```

Finally, in our `AuthRoutingModule` (`auth/auth-routing.module.ts`), import `LoginPageComponent` at the top of the file and add a new route to the routes array, above the `home` route:

```typescript
// src/app/auth/auth-routing.module.ts
// Add to imports at the top
import { LoginPageComponent } from '@app/auth/components/login-page.component';
// ...
// Add to routes array above the home route
{ path: 'login', component: LoginPageComponent },
// ...below code remains the same
```

We should be able to build the application, navigate to `http://localhost:4200/login`, and see the new form.

![Example view of non-functional login form for our NgRx application](https://cdn.auth0.com/blog/add-auth-to-ngrx/ngrx-login-screen.png)

Of course, nothing will happen when we click the button, because we don't have any effects listening for the `Login` action yet. Let's finish building our UI and then come back to that.

### Add Callback Component
Once Auth0 successfully logs us in, it will redirect back to our application `callback` route, which we'll add in this section. First, let's build the `CallbackComponent` and have it dispatch a `LoginComplete` action.

First, generate the component:

```bash
ng g c auth/components/callback -m auth --nospec
```

This component will just display a loading screen and dispatch `LoginComplete` using `ngOnInit`. Replace the contents of the generated file with this code:

```typescript
// src/app/auth/components/callback.component.ts
import { Component, OnInit } from '@angular/core';
import { Store } from '@ngrx/store';
import * as fromStore from '@app/state';
import { LoginComplete } from '@app/auth/actions/auth.actions';

@Component({
  selector: 'abl-callback',
  template: `
    <p>
      Loading...
    </p>
  `,
  styles: []
})
export class CallbackComponent implements OnInit {
  constructor(private store: Store<fromStore.State>) {}

  ngOnInit() {
    this.store.dispatch(new LoginComplete());
  }
}
```

And, finally, import the new `CallbackComponent` into `auth/auth-routing.module.ts` and add a new route after the `login` route:

```typescript
// src/app/auth/auth-routing.module.ts
// Add to imports at the top
import { CallbackComponent } from '@app/auth/components/callback.component';
// ...
// Add to routes array after the login route
{ path: 'callback', component: CallbackComponent },
// ...below code remains the same
```

Once again, if we build the application and run it, we're now able to navigate to `http://localhost:4200/callback` and see the new component (which, once again, will do nothing yet).

### Log Out Buttons
For logging out of the application, we'll need a confirmation dialog, as well as log out buttons on the user home and books page components.

Let's add the buttons first. In `auth/user-home.component.ts`, add a button in the template under the book collection button. The completed template will look like this:

{% highlight html %}
// src/app/auth/components/user-home.component.ts
<div>
	<h3>Welcome Home!</h3>
   	<button mat-button raised color="accent" (click)="goToBooks()">See my book collection</button>
    <button mat-button raised color="accent" (click)="logout()">Log Out</button>
</div>
{% endhighlight %}

Then, add these imports at the top of the file:

```typescript
// src/app/auth/components/user-home.component.ts
import { Store } from '@ngrx/store';
import * as fromStore from '@app/state';
import { Logout } from '@app/auth/actions/auth.actions';
// ...below code remains the same
```

This will let us add the store to our constructor:

```typescript
// src/app/auth/components/user-home.component.ts
// ...above code unchanged
constructor(private store: Store<fromStore.State>, private router: Router) {}
// ...below code unchanged
```

With that done, we can now add a `logout` function to the component that will dispatch the `Logout` action:

```typescript
// src/app/auth/components/user-home.component.ts
// add anywhere after the constructor
logout() {
    this.store.dispatch(new Logout());
  }
// ...other code unchanged
```

We can do something similar with the `BooksPageComponent` so the user can log out from their book collection. In `books/components/books-page-component.ts`, add the following block underneath the `mat-card-title` tag:

{% highlight html %}
// src/app/books/components/books-page.component.ts
<mat-card-actions>
  <button mat-button raised color="accent" (click)="logout()">Logout</button>
</mat-card-actions>
{% endhighlight %}

Here's what the completed `template` for the `BooksPageComponent` will look like:

{% highlight html %}
// src/app/books/components/books-page.component.ts
<mat-card>
  <mat-card-title>My Collection</mat-card-title>
  <mat-card-actions>
    <button mat-button raised color="accent" (click)="logout()">Logout</button>
  </mat-card-actions>
</mat-card>

<abl-book-preview-list [books]="books$ | async"></abl-book-preview-list>
{% endhighlight %}

Next, add the `Logout` action to the imports:

```typescript
// src/app/books/components/books-page.component.ts
import { Logout } from '@app/auth/actions/auth.actions';
// ...remaining code unchanged
```

And, finally, add a `logout` function to dispatch the `Logout` action from the button:

```typescript
// src/app/books/components/books-page.component.ts
// add anywhere after the constructor
logout() {
  this.store.dispatch(new Logout());
}
// ...remaining code unchanged
```

And that's it! Now we just need to add the logout confirmation.

### Log Out Prompt
We're going to use Angular Material to pop up a confirmation when the user clicks log out.

To generate the component, run:

```bash
ng g c auth/components/logout-prompt -m auth --no-spec
```

Then, replace the contents of the file with this:

```typescript
// src/app/auth/components/logout-prompt.component.ts
import { Component } from '@angular/core';
import { MatDialogRef } from '@angular/material';

@Component({
  selector: 'abl-logout-prompt',
  template: `
    <h3 mat-dialog-title>Log Out</h3>

    <mat-dialog-content>
      Are you sure you want to log out?
    </mat-dialog-content>

    <mat-dialog-actions>
      <button mat-button (click)="cancel()">
        No
      </button>
      <button mat-button (click)="confirm()">
        Yes
      </button>
    </mat-dialog-actions>
  `,
  styles: [
    `
      :host {
        display: block;
        width: 100%;
        max-width: 300px;
      }

      mat-dialog-actions {
        display: flex;
        justify-content: flex-end;
      }

      [mat-button] {
        padding: 0;
      }
    `
  ]
})
export class LogoutPromptComponent {
  constructor(private ref: MatDialogRef<LogoutPromptComponent>) {}

  cancel() {
    this.ref.close(false);
  }

  confirm() {
    this.ref.close(true);
  }
}

```

You're probably seeing an error in the console at this point. That's because there is one thing that the CLI didn't do for us. We need to create an `entryComponents` array in the `NgModule` decorator of `AuthModule` and add the `LogoutPromptComponent` to it.

[Entry components](https://angular.io/guide/entry-components) are components loaded imperatively through code instead of declaratively through a template. The `LogoutPromptComponent` doesn't get called through a template, it gets loaded by Angular Material when the user clicks the `Log Out` button.

Just add this after the `declarations` array in `auth/auth.module.ts` (don't forget a comma!):

```typescript
// src/app/auth/auth.module.ts
// ... imports

@NgModule({
  imports: [ ... ],
  declarations: [ ... ],
  entryComponents: [LogoutPromptComponent]
})

// ...other code unchanged
```

We'll create an effect for `Logout` to open the prompt, listen for the response, and dispatch either `LogoutCancelled` or `LogoutConfirmed` when we wire everything up in just a bit.

### Add Route Guard
We've added our login and logout components, but we want to ensure that a visitor to our site can only access the `home` route if they are logged in. Otherwise, we want to redirect them to our new `login` route. We can accomplish this with a `CanActivate` route guard.

To create the route guard, run this command:

```bash
ng g guard auth/services/auth --no-spec
```

This will create `/auth/services/auth.guard.ts`. Replace the contents of this file with the following:

```typescript
// src/app/auth/services/auth.guard.ts
import { Injectable } from '@angular/core';
import { CanActivate, Router } from '@angular/router';
import { of } from 'rxjs';
import { mergeMap, map, take } from 'rxjs/operators';
import { Store } from '@ngrx/store';

import { AuthService } from '@app/auth/services/auth.service';
import * as fromStore from '@app/state';

@Injectable({
  providedIn: 'root'
})
export class AuthGuard implements CanActivate {
  constructor(
    private authService: AuthService,
    private store: Store<fromStore.State>,
    private router: Router
  ) {}

  canActivate() {
    return this.checkStoreAuthentication().pipe(
      mergeMap(storeAuth => {
        if (storeAuth) {
          return of(true);
        }

        return this.checkApiAuthentication();
      }),
      map(storeOrApiAuth => {
        if (!storeOrApiAuth) {
          this.router.navigate(['/login']);
          return false;
        }

        return true;
      })
    );
  }

  checkStoreAuthentication() {
    return this.store.select(fromStore.selectIsLoggedIn).pipe(take(1));
  }

  checkApiAuthentication() {
    return of(this.authService.authenticated);
  }
}
```

Let's break down what's happening here. When this guard runs, we first call the function `checkStoreAuthentication`, which uses the selector we created to get `isLoggedIn` from our global state. We also call `checkApiAuthentication`, which checks if the state matches `authenticated` on our `AuthService` (which we get from local storage). If these are true, we return true and allow the route to load. Otherwise, we redirect the user to the `login` route.

We'll want to add this route guard to both the `home` route (in our `AuthModule`) and our `books` route (specifically, the `forChild` in `BooksModule`).

In `auth/auth-routing.module.ts`, add the guard to the imports:

```typescript
// src/app/auth/auth-routing.module.ts
import { AuthGuard } from './services/auth.guard';
```

Then, modify the `home` route to the following:

```typescript
{
	path: 'home',
	component: UserHomeComponent,
	canActivate: [AuthGuard]
}
```

Similarly, import the `AuthGuard` at the top of `books/books.module.ts`:

```typescript
// src/app/books/books.module.ts
import { AuthGuard } from '@app/auth/services/auth.guard';
// ...all other code unchanged
```

Then, modify `RouterModule.forChild` to this:

```typescript
// src/app/books/books.module.ts
// ...above code unchanged
RouterModule.forChild([
  { path: '', component: BooksPageComponent, canActivate: [AuthGuard] },
]),
// ...below code unchanged
```

We did it! If we run `ng serve`, we should no longer be able to access the `home` or `books` route. Instead, we should be redirected to `login`.

> Note that we haven't added any sort of 404 redirecting here. To do that, we'd want to add a wildcard route and a `PageNotFoundComponent` to redirect to. You can read more about [wildcard routes](https://angular.io/guide/router#wildcard-route) in the Angular docs.

### Checking Authentication on App Load
We just have one last UI piece to add. We need to dispatch the `CheckLogin` action when the application loads so that we can retrieve the token from the server if the user is logged in. The best place to do this is in the `AppComponent` (`src/app/app.component.ts`).

Our steps in `AppComponent` will be identical to what we did in the `CallbackComponent` &mdash; the only difference is the action we will dispatch. In `app.component.ts`, we'll first need to add `OnInit` to our `@angular/core` imports. We'll also need to import the `Store` from `ngrx`, our app `State`, and our `CheckLogin` action. Our complete imports in the file will look like this:

```typescript
// src/app/app.component.ts
import { Component, OnInit } from '@angular/core';
import { Store } from '@ngrx/store';
import * as fromStore from '@app/state';
import { CheckLogin } from '@app/auth/actions/auth.actions';
// below code unchnaged.
```

Then, in the component class, implement the `OnInit` interface and create the `OnInit` function:

```typescript
// src/app/app.component.ts
// ...imports and template defined above (unchaged)
// The class definition should look like this:
export class AppComponent implements OnInit {
  constructor() { }

  ngOnInit() {}
}
```

Lastly, we'll need to inject the `Store` into the constructor and dispatch the `CheckLogin` action inside of `ngOnInit`. The finished class definition should look like this:

```typescript
// src/app/app.component.ts
// ...imports and template defined above (unchaged)
// The class definition should look like this:
export class AppComponent implements OnInit {
  constructor(private store: Store<fromStore.State>) { }

  ngOnInit() {
    this.store.dispatch(new CheckLogin());
  }
}
```

You won't notice anything different when you run the application, because we'll be calling `checkSession` on the `AuthService` from an effect. So, let's put it all together by creating effects that will control logging in and out in addition to checking for persisted authentication.

## Controlling the Authentication Flow with Effects
Alright, friends, we're ready for the final piece of this puzzle. We're going to add effects to handle our authentication flow. Effects allow us to initiate side effects as a result of actions dispatched in a central and predictable location. This way, if we ever need to universally change the behavior of an action's side effect, we can do so quickly without repeating ourselves.

### Add Imports and Update Constructor
All of our effects will go in `auth/effects/auth.effects.ts`, and the CLI has already connected them to our application through the `AuthModule`. All we need to do is fill in our effects.

Before we do that, be sure that all of these imports are at the top of the file:

```typescript
// auth/effects/auth.effects.ts
import { Injectable } from '@angular/core';
import { Router } from '@angular/router';
import { Actions, Effect } from '@ngrx/effects';
import { tap, exhaustMap, map, catchError } from 'rxjs/operators';
import { MatDialog } from '@angular/material';
import * as fromAuth from '../actions/auth.actions';
import { LogoutPromptComponent } from '@app/auth/components/logout-prompt.component';
import { AuthService } from '@app/auth/services/auth.service';
import { of, empty } from 'rxjs';
```

If you commented out the boilerplate `loadFoos$` effect that the CLI generated earlier, go ahead and delete it:

```typescript
// src/app/auth/auth.effects.ts
// ...
// Delete both of these lines.
// @Effect()
// loadFoos$ = this.actions$.pipe(ofType(AuthActionTypes.LoadAuths));
// ...
```

Next, update the constructor so that we're injecting the router, the `AuthService`, and `MatDialog` (from Angular Material):

```typescript
// auth/effects/auth.effects.ts
constructor(
    private actions$: Actions,
    private authService: AuthService,
    private router: Router,
    private dialogService: MatDialog
  ) { }
```

We'll use all of these in our effects.

### Add Log In Effects
Let's add our log in effects first.

Add the following to our class before the constructor (this is a convention with effects):

```typescript
// auth/effects/auth.effects.ts
// Add before the constructor
@Effect({ dispatch: false })
login$ = this.actions$.ofType<fromAuth.Login>(fromAuth.AuthActionTypes.Login).pipe(
  tap(() => {
    return this.authService.login();
  })
);

@Effect()
loginComplete$ = this.actions$
  .ofType<fromAuth.Login>(fromAuth.AuthActionTypes.LoginComplete)
  .pipe(
    exhaustMap(() => {
      return this.authService.parseHash$().pipe(
        map((authResult: any) => {
          if (authResult && authResult.accessToken) {
            this.authService.setAuth(authResult);
            window.location.hash = '';
            return new fromAuth.LoginSuccess();
          }
        }),
        catchError(error => of(new fromAuth.LoginFailure(error)))
      );
    })
  );

@Effect({ dispatch: false })
loginRedirect$ = this.actions$
  .ofType<fromAuth.LoginSuccess>(fromAuth.AuthActionTypes.LoginSuccess)
  .pipe(
    tap(() => {
      this.router.navigate([this.authService.authSuccessUrl]);
    })
  );

@Effect({ dispatch: false })
loginErrorRedirect$ = this.actions$
  .ofType<fromAuth.LoginFailure>(fromAuth.AuthActionTypes.LoginFailure)
  .pipe(
    map(action => action.payload),
    tap((err: any) => {
      if (err.error_description) {
        console.error(`Error: ${err.error_description}`);
      } else {
        console.error(`Error: ${JSON.stringify(err)}`);
      }
      this.router.navigate([this.authService.authFailureUrl]);
    })
  );
// ...below code unchanged
```

Let's break down what's happening in each of these.

- Login — calls the `login` method on `AuthService`, which triggers Auth0. Does not dispatch an action.
- Login Complete — calls `parseHash$` on `AuthService`, which returns an observable of the parsed hash. If there's a token, this effect calls `setAuth`, clears the hash from the window location, and then dispatches the `LoginSuccess` action. If there's not a token, the effect dispatches the `LoginFailure` action with the error as its payload.
- Login Redirect — This effect happens when `LoginSuccess` is dispatched. It redirects the user to `home` (using the `authSuccessUrl` property on the `AuthService`) and does not dispatch a new action.
- Login Error Redirect — This effect happens when `LoginFailure` is dispatched. It redirects the user back to `login` (using the `authFailureUrl` property on the `AuthService`) and does not dispatch a new action.

If we run the application with `ng serve`, we should now be able to successfully log in to our application using Auth0! We'll see a login screen similar to this:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/add-auth-to-ngrx/auth0-book-login.png"
  style="max-width:400px" alt="Our Auth0 login screen works!">
</p>

On your first login, you'll need to click the "Sign Up"  button to create a user. Alternatively, you can [manually create users from the Auth0 dashboard](https://auth0.com/docs/dashboard/creating-users-in-the-management-portal). In either case, you'll receive an email asking you to verify your email address. Your first login to the application will also trigger an "Authorize App" screen requesting permission. Just click the green button to continue. Once you're all signed up and logged in, you'll be redirected to the `home` route, where you can click the button to view the book collection. Of course, we can't log out yet, so let's add the effects for that now.

> Note: You can also use Google to sign up and log in. However, be aware that you will need to [generate a Google Client ID and Client Secret](https://auth0.com/docs/connections/social/google) in order to complete this tutorial.

### Persist Authentication on Refresh
This is great, but if we refresh the application, we'll lose the access token. Because we already set up the flag in local storage and the route guard is checking for that flag, our application will appear to be logged in on refresh. However, we haven't called `checkSession` yet through Auth0, so we'll no longer have the token. Let's add the effect for `CheckLogin` to fix that.

```typescript
// auth/effects/auth.effects.ts
// add below login effects:
// ...above code unchanged
@Effect()
checkLogin$ = this.actions$.ofType<fromAuth.CheckLogin>(fromAuth.AuthActionTypes.CheckLogin).pipe(
  exhaustMap(() => {
    if (this.authService.authenticated) {
      return this.authService.checkSession$({}).pipe(
        map((authResult: any) => {
          if (authResult && authResult.accessToken) {
            this.authService.setAuth(authResult);
            return new fromAuth.LoginSuccess();
          }
        }),
        catchError(error => {
          this.authService.resetAuthFlag();
          return of(new fromAuth.LoginFailure({ error }));
        })
      );
    } else {
      return empty();
    }
  })
);
// ...below code unchanged
```

When `CheckLogin` is dispatched, this effect will call `checkSession` on the `AuthService`, which, like `parseHash`, returns token data. If there's token data, the effect will call the `setAuth` method and dispatch the `LoginSuccess` action. If there's an error, the effect will dispatch `LoginFailure`. Those actions will work the same way as with logging in - navigating to `home` on success or `login` on failure.

Let's check if it works. Run `ng serve`, navigate to `http://localhost:4200`, and log in. Once we're back at the `home` route, refresh the page with our browser. We should not be redirected back to the `login` route, but remain on the `home` route. Awesome!

Here's a challenge for you: how might you update this feature so that you can persist the previous route, too? That way a user would land back on `books` when refreshing.

### Add Log Out Effects
Let's add our final two effects to finish off this application.

```typescript
// auth/effects/auth.effects.ts
// Add under the login effects
// ...above code unchanged
@Effect()
logoutConfirmation$ = this.actions$.ofType<fromAuth.Logout>(fromAuth.AuthActionTypes.Logout).pipe(
	exhaustMap(() =>
	  this.dialogService
	    .open(LogoutPromptComponent)
	    .afterClosed()
	    .pipe(
	      map(confirmed => {
	        if (confirmed) {
	          return new fromAuth.LogoutConfirmed();
	        } else {
	          return new fromAuth.LogoutCancelled();
	        }
	      })
	    )
	)
);

@Effect({ dispatch: false })
logout$ = this.actions$
	.ofType<fromAuth.LogoutConfirmed>(fromAuth.AuthActionTypes.LogoutConfirmed)
	.pipe(tap(() => this.authService.logout()));
// ...below code unchanged
```

Here's what's happening here:

- Logout Confirmation — This effect will display the log out confirmation dialog. It will then process the result by dispatching either the `LogoutConfirmed` or `LogoutCancelled` actions.
- Logout — This effect happens after `LogoutConfirmed` has been dispatched. It will call the `logout` function on the `AuthService`, which tells Auth0 to log us out and redirect back home. This effect does not dispatch another action.

Running `ng serve` again should now allow us to log in, view the book collection, and log out. Be sure to check if we can also log out from the `home` route!

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/add-auth-to-ngrx/ngrx-logout.png"
  style="max-width:400px" alt="Finished NgRx logout popup">
</p>

Remember, you can access the finished code for this tutorial [here](https://github.com/auth0-blog/ngrx-auth).

## Review and Where to Go From Here
Congratulations on making it to the end! We've covered a lot of ground here, like:

- Using @ngrx/schematics to quickly generate new code
- Defining global authentication state
- Using selectors to access authentication state
- Setting up Auth0 to handle your authentication
- Using effects to handle the login and logout flows
- Persisting authentication on refresh

We've spent time laying the foundation of a basic authentication flow in a very simple application. However, we could easily apply this same setup to a much more complex application. Scaling the setup is very minor, and adding new pieces of state, new actions, or new side effects would be relatively easy. We've got all the building blocks in place.

For example, let's say you needed to add the access token to outgoing HTTP requests as an `Authorization` header. You've already got what you need to get this working quickly. You could add `tokenData` to the authentication state and create a selector for it. Then, add the token data as the `payload` of the `LoginSuccess` action and update the effects that use it. Once that's all set up, you could then create an [HTTP interceptor](https://angular.io/guide/http#intercepting-requests-and-responses) to select the token data from the `store` and add it to outgoing requests.

Now that the foundation of the authentication flow has been built, the sky is the limit for how you want to extend it. My goal for this tutorial was to keep it simple while helping you understand some new, fairly complex concepts. I hope you can take this knowledge and use it in the real world &mdash; let me know how it goes!
