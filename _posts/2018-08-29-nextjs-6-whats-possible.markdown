---
layout: post
title: "Next.js 6 Features: A Practical Introduction"
metatitle: "A practical introduction to the features of Next.js 6"
description: What's possible in Next.js 6? What improvements were made? Learn how to build highly performant Universal JavaScript apps with the latest Next.js release.
metadescription: "How to build Universal JavaScript apps with Next.js 6"
date: 2018-08-29 08:30
category: Hot Topics, Frameworks, Next.js
design:
  bg_color: "#222228"
  image: https://cdn.auth0.com/blog/next3/logo.png
author:
  name: Prosper Otemuyiwa
  url: http://twitter.com/unicodeveloper
  mail: prosper.otemuyiwa@auth0.com
  avatar: https://en.gravatar.com/avatar/1097492785caf9ffeebffeb624202d8f?s=200
tags:
- nextjs
- universal-webapp
- javascript
- auth
related:
- 2016-11-01-building-universal-apps-with-nextjs
- 2017-04-24-build-better-universal-apps-with-nextjs2
- 2017-09-27-nextjs-3-release-what-is-new
---

---

**TL;DR:** Next.js, a lightweight framework for static and server-rendered applications is popular for building Universal JavaScript applications. In this article, I'll cover the features of **Next.js 6**.

---

## A Refresher: What is a Universal JavaScript Application?

The term *Universal* simply means the ability to run the same code on the server, browsers, mobile devices and any other platform. *Universal Javascript* is a term people are leaning towards these days. A lot of developers also call it **Isomorphic JavaScript**. Check out the excellent blog post on [Universal JavaScript](https://medium.com/@mjackson/universal-javascript-4761051b7ae9#.ij2c0zh8j) written by [Michael Jackson](https://twitter.com/mjackson).

## Introduction to Next.js 6 Features

### 1. Zero Config Static Exports

Before now, Next.js required the use of a config file, `next.config.js`, in your application to manually set up routes before you can export the static version of your app.

With Next.js 6, modifications to `next.config.js` file is no longer needed unless there's a really advanced custom routing needed. The route map is automatically generated from the files in the `pages/` directory.

Just simply run the export command:

```bash
next build && next export
```

and the fully functional static version of your app will be made available.

**Note:** It's advisable you configure the command in your `package.json` file like so:

```js
"scripts": {
    "export": "next build && next export",
}
```

With the command in place, you can simply run `npm run export` and it will build your Next.js app as a static website.

### 2. Babel 7 Support

Next.js now runs on Babel 7. Babel provides a way for developers to use the latest JavaScript syntax while allowing them to not worry about how to make it backwards compatible for their users by translating it.

Babel 7 is faster and ships with a lot of new features such as:

1. More options for minification.
2. JSX Fragments.
3. A new upgrade tool, `babel-upgrade` that tries to automatically make upgrade changes.
4. Implementation of several TC39 proposals.

### 3. New JSX Fragment Syntax Support

In React 16, Fragments are written like this:

```js
render() {
  return <React.Fragment>
    <Car />,
    <Truck />
  </React.Fragment>
}
```

Next.js supports a new JSX Fragment syntax, `<>`, for creating fragments.

```js
render() {
  return <>
    <Car />,
    <Truck />
  </>
}
```

### 4. `_app.js` Inclusion

Before now, Next.js shipped with the ability to include `_document.js` for extensibility. However, there were limitations using it because it was mostly limited to the initial pre-rendering phrase.

In Next.js 6, a new extension file, `_app.js`, can be added to a Next.js 6 app within the `pages` directory to provide better extensibility that allows hooking into the runtime lifecycle and data fetching. Check out some case studies it enables:

* **Better Error handling**––In Next.js, you can use the React `componentDidCatch` component method to handle exceptions. However, the logic is best placed in `_app.js`. Check out the example below:


_pages/_app.js_

```js
import App from 'next/app'

export default class MyApp extends App {
  componentDidCatch (error, errorInfo) {
    console.log('CUSTOM ERROR HANDLING', error)
    // This is needed to render errors correctly in development / production
    super.componentDidCatch(error, errorInfo)
  }
}
```

* **Page Transitions**––Smooth animations are possible on the client side and it's possible via the use of `_app.js`. Check out the [next version of page transitions app](https://github.com/xavczen/nextjs-page-transitions).

* **Better Integrations from third party tools**––With `_app.js`, it's easy to integrate state management frameworks like [Apollo](https://github.com/zeit/next.js/tree/canary/examples/with-apollo) and [Redux](https://github.com/zeit/next.js/tree/canary/examples/with-redux).

### 5. TypeScript Support

Babel 7 now has built-in support for TypeScript. And Next.js now uses Babel 7. Therefore, Next.js 6 has a first-class Typescript support.

**Note:** Install the latest version of [https://github.com/zeit/next-plugins/tree/master/packages/next-typescript/#readme](https://github.com/zeit/next-plugins/tree/master/packages/next-typescript/#readme).

### 6. Support for Nested .Babelrc

In Next.js 6, you can now include a scoped `.babelrc` file in a nested directory that requires a different Babel configuration.

```bash
src/
    .babelrc      # General .babelrc
  components/
    i18n/
      .babelrc  # This .babelrc only applies to this directory
```

## Securing a Next.js 6 App with Auth0

I'll use the [app](https://github.com/auth0-blog/next3-auth0) from our [previous Next.js article](https://auth0.com/blog/nextjs-3-release-what-is-new/). Obviously, a lot of things have changed since Next.js 3. Therefore, we'll make a lot of changes step-by-step. Check out the [upgraded Next.js 6 app on GitHub](https://github.com/auth0-blog/next6-auth0) if you want to dive straight into the code.

We can secure a Next.js app with Auth0 by using the [auth0.js library](https://github.com/auth0/auth0.js). If you don't already have an Auth0 account, <a href="https://auth0.com/signup" data-amp-replace="CLIENT_ID" data-amp-addparams="anonId=CLIENT_ID(cid-scope-cookie-fallback-name)">sign up</a> for one now. Navigate to the Auth0 [management dashboard](https://manage.auth0.com/), click on `New client` by the right hand side, select Regular Web App from the dialog box and then go ahead to the `Settings` tab where the client ID, client Secret and Domain can be retreived.

> [Auth0 offers a generous **free tier**](https://auth0.com/pricing) to get started with modern authentication.

**Note:** Make sure you set the  `Allowed Callback URLs` to `http://localhost:3000/auth/signed-in` or whatever url and port you are running on. Furthermore, set the `Allowed Logout URLs` to `http://localhost:3000/`.

> Don't forget to rename the `config.sample.json` file to `config.json` and add your credentials.

We have put the necessary things in place to secure our app. Now, let's make the changes required for the app to run on Next.js 6 before we test our authentication toolkit.

1. Re-install _next_, _react-dom_, _react_, _babel-plugin-styled-components_, and _styled-components_ to have the latest respective package versions.

2. Update `babel.rc` like so:

    ```js
    {
      "presets": [
        "next/babel"
      ],
      "plugins": [
        [
          "babel-plugin-styled-components",
          {
            "ssr": true,
            "displayName": false
          }
        ]
      ]
    }
    ```

3. `React.Proptypes` has moved into a different package since React v15.5. Go ahead and install the new `prop-types` library.

    ```bash
      npm install prop-types
    ```

4. Update every file that imports `Proptypes` from React to import `Proptypes` from the new library like this:

    ```js
    ...
    // Before
    import React, { PropTypes } from 'react'

    // Now
    import React from 'react'
    import PropTypes from 'prop-types'
    ...
    ```

5. In `/hocs/defaultPage.js`, there's a block code of code that ensures our page is styled immediately it server-renders. Without this block of code, a flash of unstyled page is shown to the user everytime the page is reloaded.

    ```js
    ...
    {!process.browser && (
      <style>
        {styleSheet.getCSS()}
      </style>
    )}
    ...
    ```

Delete the code. We'll take advantage of `_document.js` to implement style flushing for server-side rendering. Now, create a new `_document.js` file in the `pages` directory and add the following code:

```js
import Document, { Head, Main, NextScript } from 'next/document'
import { ServerStyleSheet } from 'styled-components'

export default class MyDocument extends Document {
  static getInitialProps ({ renderPage }) {
    const sheet = new ServerStyleSheet()
    const page = renderPage(App => props => sheet.collectStyles(<App {...props} />))
    const styleTags = sheet.getStyleElement()
    return { ...page, styleTags }
  }

  render () {
    return (
      <html>
        <Head>
          <title>Next.js + auth0</title>
          {this.props.styleTags}
        </Head>
        <body>
          <Main />
          <NextScript />
        </body>
      </html>
    )
  }
}
```

The code above enables server side rendering of the styles. It does two things:

1. Hook into the `renderPage` method to parse the child component’s styles server-side
2. It hooks into the Document on the initial page load so that the app can update the `<head>` with the `<style>` tags that are required by the components.


#### Authentication Process

_utils/auth.js_

```js
import jwtDecode from 'jwt-decode'
import Cookie from 'js-cookie'

const getQueryParams = () => {
  const params = {}
  window.location.href.replace(/([^(?|#)=&]+)(=([^&]*))?/g, ($0, $1, $2, $3) => {
    params[$1] = $3
  })
  return params
}

export const setToken = (idToken, accessToken) => {
  if (!process.browser) {
    return
  }
  Cookie.set('user', jwtDecode(idToken))
  Cookie.set('idToken', idToken)
  Cookie.set('accessToken', accessToken)
}

export const unsetToken = () => {
  if (!process.browser) {
    return
  }
  Cookie.remove('idToken')
  Cookie.remove('accessToken')
  Cookie.remove('user')

  // to support logging out from all windows
  window.localStorage.setItem('logout', Date.now())
}

export const getUserFromServerCookie = (req) => {
  if (!req.headers.cookie) {
    return undefined
  }
  const jwtCookie = req.headers.cookie.split(';').find(c => c.trim().startsWith('idToken='))
  if (!jwtCookie) {
    return undefined
  }
  const jwt = jwtCookie.split('=')[1]
  return jwtDecode(jwt)
}

export const getUserFromLocalCookie = () => {
  return Cookie.getJSON('user')
}
```

The code above sets and unsets a cookie with the token gotten from the Auth0 server.

_utils/auth0.js_

```js
const getAuth0 = (options) => {
  const config = require('../config.json')
  const auth0 = require('auth0-js');
  return new auth0.WebAuth({
    clientID: config.AUTH0_CLIENT_ID,
    domain: config.AUTH0_CLIENT_DOMAIN,
  });
}

const getBaseUrl = () => `${window.location.protocol}//${window.location.host}`

const getOptions = (container) => {
  return {
    responseType: 'token id_token',
    redirectUri: `${getBaseUrl()}/auth/signed-in`,
    scope: 'openid profile email'
  }
}

export const authorize = () => getAuth0().authorize(getOptions())
export const logout = () => getAuth0().logout({ returnTo: getBaseUrl() })
export const parseHash = (callback) => getAuth0().parseHash(callback)
```

The code above makes a request to Auth0 server to initiate and authorize a user.

_pages/auth/sign-in.js_

```js
import React from 'react'

import defaultPage from '../../hocs/defaultPage'
import { authorize } from '../../utils/auth0'

class SignIn extends React.Component {
  componentDidMount () {
    authorize()
  }
  render () {
    return null
  }
}

export default defaultPage(SignIn)
```

Display the login page once the sign-in component gets mounted.

![Sign in](https://cdn.auth0.com/blog/nextjs3/login.png)
_Sign-in page_

_pages/auth/signed-in.js_

```js
import React, { PropTypes } from 'react'
import Router from 'next/router'

import { setToken } from '../../utils/auth'
import { parseHash } from '../../utils/auth0'

export default class SignedIn extends React.Component {
  static propTypes = {
    url: PropTypes.object.isRequired
  }

  componentDidMount () {
    parseHash((err, result) => {
      if(err) {
        console.error('Something happened with the Sign In request')
        return;
      }

      setToken(result.idToken, result.accessToken);
      Router.push('/')
    })
  }
  render () {
    return null
  }
}
```

Grab the token and ID token from Auth0 as it returns to the callback which is the signed-in page, save it and redirect to the index page.

![Signed in](https://cdn.auth0.com/blog/signedin/authenticated.png)
_Secret page shows that the user is signed in and can access it_

_pages/index.js_

```js

import React, { PropTypes } from 'react'
import Link from 'next/link'

import defaultPage from '../hocs/defaultPage'

const SuperSecretDiv = () => (
  <div>
    This is a super secret div.
    <style jsx>{`
      div {
        background-color: #ecf0f1;
        box-shadow: 0 1px 3px rgba(0,0,0,0.12), 0 1px 2px rgba(0,0,0,0.24);
        border-radius: 2px;
        padding: 10px;
        min-height: 100px;
        display: flex;
        align-items: center;
        justify-content: center;
        color: #333;
        text-align: center;
        font-size: 40px;
        font-weight: 100;
        margin-bottom: 30px;
      }
    `}</style>
  </div>
)

const createLink = (href, text) => (
  <a href={href}>
    {text}
    <style jsx>{`
      a {
        color: #333;
        padding-bottom: 2px;
        border-bottom: 1px solid #ccc;
        text-decoration: none;
        font-weight: 400;
        line-height: 30px;
        transition: border-bottom .2s;
      }

      a:hover {
        border-bottom-color: #333;
      }
    `}</style>
  </a>
)

const Index = ({ isAuthenticated }) => (
  <div>
    {isAuthenticated && <SuperSecretDiv />}
    <div className='main'>
      <h1>Hello, friend!</h1>
      <p>
        This is a super simple example of how to use {createLink('https://github.com/zeit/next.js', 'next.js')} and {createLink('https://auth0.com/', 'Auth0')} together.
      </p>
      {!isAuthenticated && (
        <p>
          You're not authenticated yet. Maybe you want to <Link href='/auth/sign-in'>{createLink('/auth/sign-in', 'sign in')}</Link> and see what happens?
        </p>
      )}
      {isAuthenticated && (
        <p>
          Now that you're authenticated, maybe you should try going to our <Link href='/secret'>{createLink('/secret', 'super secret page')}</Link>!
        </p>
      )}
    </div>
    <style jsx>{`
      .main {
        max-width: 750px;
        margin: 0 auto;
        text-align: center;
      }

      h1 {
        font-size: 40;
        font-weight: 200;
        line-height: 40px;
      }

      p {
        font-size: 20px;
        font-weight: 200;
        line-height: 30px;
      }
    `}</style>
  </div>
)

Index.propTypes = {
  isAuthenticated: PropTypes.bool.isRequired
}

export default defaultPage(Index)

```

The index page is server-rendered. It checks whether the user is authenticated or not and renders content based on the status.

The [secret page](https://github.com/auth0-blog/next6-auth0/blob/master/pages/secret.js) too checks if the user is logged in and determines content based on the user's status.

![Secret page unauthorized](https://cdn.auth0.com/blog/secret/notloggedin.png)
_Not displaying valid content because the user cant access the secret page without signing in_

**Note:** This example performs no server side validation of the token sent by the user in its cookies. For production-ready secure pages this is necessary.

> Auth0 provides the simplest and easiest to use [user interface tools to help administrators manage user identities](https://auth0.com/user-management) including password resets, creating and provisioning, blocking and deleting users.

## Conclusion

Next.js 7.0 is in the works and it promises to be better. [nextjs.org](https://nextjs.org/) codebase is now [open-source](https://github.com/zeit/next-site). A lot can be learned about using Next.js by going through the code extensively. There are also lots of [integration examples](https://github.com/zeit/next.js/tree/canary/examples) for you to be able to use Next.js with several tools in the JavaScript ecosystem.

Finally, you can learn Next.js from scratch via the [official learn site](https://github.com/zeit/next.js/tree/canary/examples).
