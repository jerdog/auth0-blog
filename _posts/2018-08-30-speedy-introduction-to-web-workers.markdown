---
layout: post
title: "Speedy Introduction to Web Workers"
metatitle: "Introduction to Web Workers in JavaScript - Listen and Send Messages"
description: "Learn how to use web workers in JavaScript to create parallel programming and perform multiple operations simultaneously rather than interleaving them."
metadescription: "Learn how to use web workers in JavaScript to optimize development and perform synchronous operations. Follow along, using CodeSandbox to manage web worker messages."
date: 2018-08-30 8:30
category: Technical Guide, Stack, Frontend
auth0_aside: true
design: 
  bg_color: "#29335c"
  image: https://cdn.auth0.com/blog/speedy-introduction-to-web-workers/web-workers.png
author:
  name: Dan Arias
  url: http://twitter.com/getDanArias
  mail: dan.arias@auth0.com
  avatar: https://secure.gravatar.com/avatar/d786d3d179f6bd91ce63df956d433588
tags: 
  - web-workers
  - performance
  - parallelism
  - javascript
  - web
  - frontend
  - architecture
  - optimization
related:
  - 2016-02-22-12-steps-to-a-faster-web-app
  - 2015-10-30-creating-offline-first-web-apps-with-service-workers
  - 2016-12-19-introduction-to-progressive-apps-part-one
---

Web workers enable developers to benefit from parallel programming in JavaScript. Parallel programming lets us run different computations at the same time. Let's see how we benefit from doing tasks in parallel as humans to help us understand the value of web workers.

Let's imagine that each one of us runs a tech blog. We work by ourselves and are responsible for coding a demo app, writing about the app development process, and creating assets for the post (like diagrams, images, or logos). That's a heavy task pipeline to handle by ourselves. Coding the app and writing the content are interconnected since the experience of writing the code motivates writing the content. However, creating assets is something that we could delegate to someone else.

Let's say that we have a group of friends that are talented designers. They agree to create the assets for us. All that they ask is for us to message them a description or sketch of the asset and they will reply back with a cool professional version of it when they are done. Now, we only need to focus on coding, writing, and integrating the assets of the designers once they message us.

This will give us a huge productivity boost. Initially, we were being blocked from coding and writing whenever we had to design assets. The completion of a blog post would have taken much longer if we were to work alone than if we were to delegate one task to a designer friend.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/speedy-introduction-to-web-workers/Performing-Tasks-With-and-Without-Web-Worker.png" alt="Performing tasks with and without web workers.">
</p>

The design team handles the task asynchronously on their own pipeline. The design team acts exactly how a web worker acts in JavaScript applications. JavaScript is a single-threaded language. As such, running expensive logic in the main thread can block it and make our JavaScript application seem slow or unresponsive. Using web workers, we can create a separate thread to run any logic without interrupting the main thread.

{% include tweet_quote.html quote_text="Web workers in JavaScript allows us to create parallel programming to perform multiple operations simultaneously rather than interleaving them." %}

> [Interleaving](https://www.dictionary.com/browse/interleaving) means "to arrange (an operation) so that two or more programs, sets of instructions, etc., are performed in an alternating fashion."

Let's explore what we need to know to make use of web workers in JavaScript and what benefits it brings to a web application.

<br>
<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">HEY<br><br>USE WEB WORKERS</p>&mdash; Jason Miller 🦊⚛ (@_developit) <a href="https://twitter.com/_developit/status/995162140016177152?ref_src=twsrc%5Etfw">May 12, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">To free up the already-congested main thread so it can spend all of it&#39;s time on layout and paint 😃</p>&mdash; Jason Miller 🦊⚛ (@_developit) <a href="https://twitter.com/_developit/status/995792286947643392?ref_src=twsrc%5Etfw">May 13, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
<br>

## Why use JavaScript Web Workers?

Let's expand a bit more on what [Jason Miller](https://twitter.com/_developit) explained in his tweets.

In JavaScript, we can create parallel programming to perform multiple operations simultaneously using [web workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers). Web workers let us create background threads that are separate from the main execution thread, where we usually run our user interface logic. The core advantage of this workload separation is that we can run expensive operations within an isolated thread without interrupting or affecting the responsiveness and usability of the main thread. When the background thread completes its task it seamlessly notifies the main thread about the results through an event that is managed through regular [JavaScript event handling](https://eloquentjavascript.net/15_event.html).

{% include tweet_quote.html quote_text="Web workers are JavaScript objects that can be treated just like any other object: you can pass them around as function arguments, assign them to class properties, and since they have a common interface, even extend them!" %}

Web workers effectively enable a form of multi-threading in JavaScript with [some restrictions](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Functions_and_classes_available_to_workers#APIs_available_in_workers) such as not being able to access the DOM and not having access to the web worker's parent page (the page that created it). With that in mind, let's learn next how we can create web workers.

## Setting Up a Development Environment

Getting hands-on with web workers will help us understand them better! For the purpose of this blog post, we'll be running the sample code within a [CodeSandbox](https://codesandbox.io/) project. It's easy to bootstrap and run a vanilla JavaScript project there. Please, follow these steps:

- Visit [CodeSandbox](https://codesandbox.io/).

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/speedy-introduction-to-web-workers/codesandbox-home-page.png" alt="CodeSandbox home page">
</p>

- On the main page, click on the **Box** icon that represents a Vanilla JavaScript project.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/speedy-introduction-to-web-workers/codesandbox-javascript-project-icon.png" alt="CodeSandbox JavaScript project icon">
</p>

- Scroll down and notice that the project preview is shown within the page.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/speedy-introduction-to-web-workers/codesandbox-javascript-project-preview.png" alt="CodeSandbox JavaScript project preview">
</p>

- Click on the **Edit on CodeSandbox** link to open the project on its own browser tab.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/speedy-introduction-to-web-workers/codesandbox-javascript-full-project.png" alt="CodeSandbox full JavaScript project">
</p>

- Delete `index.js` in the `src` folder.

- Create `main.js` and `worker.js` under the `src` folder.

- Open `index.html` and change the `src` attribute of `<script>` to `src/main.js`:

{% highlight html %}
{% raw %}

<html>

<head>
    <title>Parcel Sandbox</title>
    <meta charset="UTF-8" />
</head>

<body>
    <div id="app"></div>

    <script src="src/main.js"></script>

</body>

</html>

{% endraw %}
{% endhighlight %}

We'll soon learn why we are creating these files. CodeSandbox uses [ParcelJS](https://parceljs.org/) to bundle the JavaScript application easily.

> Feel free to use your own environment or local configuration! If you are using Webpack, there is [extra configuration using `worker-loader`](https://github.com/webpack-contrib/worker-loader) that needs to be done. We'll cover that in a future post.

## Creating Web Workers

To create a web worker, we use the [`Worker()`](https://developer.mozilla.org/en-US/docs/Web/API/Worker/Worker) constructor from the [Web Workers API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API). The `Worker()` constructor has the following signature:

```javascript
Worker(aURL, options);
```

`aURL` is a string that represents the URL of the script that we want the worker to execute.

`options` is an object to customize the `Worker` instance. The [allowed options](https://developer.mozilla.org/en-US/docs/Web/API/Worker/Worker#Parameters) are `type`, `credentials`, and `name`. We don't need to configure them for the scope of this post.

In practice, we instantiate a web worker in the main thread. The main thread could be represented by a JavaScript file, for example, `main.js`, that is the entry point to the application. The web worker thread could be represented by another file, for example, `worker.js`. `main.js` then creates a new `Worker` using the `worker.js` file. Let's see this in action.

Let's open `src/main.js` in our project and populate it with the following code:

```javascript
// src/main.js

const worker = new Worker("../src/worker.js");
```

In the code above, `worker` becomes a `Worker` instance that will execute the script on `worker.js`.

> When following along in CodeSandbox, we need to specify the full path to `worker.js` for it to work properly: `../src/worker.js`.

That's it for the creation of a web worker! We effectively now have two threads available in our application: `main` and `worker`. Next, we'll learn how to communicate between threads.

## Sending Messages To and From a Web Worker

In the introduction, we discussed how the internal collaboration between our Content and Design teams at Auth0 resemble the interaction between threads using web workers in JavaScript. In our case, a Content Engineer represents the `main` thread and the Designer represents the `worker` thread. How would the `main` thread ping the `worker` thread and vice versa? We do that through the [`postMessage()`](https://developer.mozilla.org/en-US/docs/Web/API/Worker/postMessage) method and the [`onmessage`](https://developer.mozilla.org/en-US/docs/Web/API/Worker/onmessage) event handler from the Web Workers API.

Let's use the classic [Marco Polo game](<https://en.wikipedia.org/wiki/Marco_Polo_(game)>) to see this communication in action. In this game, one player shouts "Marco!" and the other player must reply "Polo!". Within our context we want to do the following:

1.  `main.js` and `worker.js` are on standby listening for any message between each other.

2.  `main.js` sends a message to `worker.js`: `"Marco!"`.

3.  `worker.js` gets the message from `main.js` and replies: `"Polo!"`.

4.  **Step 2** and **Step 3** are repeated infinitely.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/speedy-introduction-to-web-workers/Marco-Polo-Relay.png" alt="Marco Polo relay game between threads.">
</p>

### Step 1: Listen for Messages

The `Worker.onmessage` event handler let us listen for messages between the threads. The signature of this `Worker` event handler property is as follows:

```javascript
myWorker.onmessage = e => {
  // Event handler logic
};
```

The function assigned to `onmessage` is called when a `message` event occurs.

To set this up in `main.js`, we use the `Worker` instance we created:

```javascript
// src/main.js

const worker = new Worker("../src/worker.js");

worker.onmessage = e => {};
```

To set this up in the web worker thread represented by `worker.js`, we use the `onmessage` property directly:

```javascript
// src/worker.js

onmessage = e => {};
```

How do we access the message data that is being sent? The message payload can be accessed from the message event's `data` property.

Let's update our code as follows:

```javascript
// src/main.js

const worker = new Worker("../src/worker.js");

worker.onmessage = e => {
  const message = e.data;
  console.log(`[From Worker]: ${message}`);
};
```

```javascript
// src/worker.js

onmessage = e => {
  const message = e.data;
  console.log(`[From Main]: ${message}`);
};
```

Let's save our work for each file. On CodeSandbox, we can use `CMD + S` or `CTRL + S` to save each file.

We got our threads listening for messages between each other. Next, let's learn how to send messages.

### Step 2: Send a Message from Main Thread to Worker Thread

To send messages, we rely on the `Worker.postMessage()` method:

```javascript
worker.postMessage(message);
```

The `postMessage()` takes a single parameter representing the data that we want to send. This data may be any value or JavaScript object handled by the [structured clone algorithm](https://developer.mozilla.org/en-US/docs/Web/API/Worker/postMessage). As noted by MDN, the structured clone algorithm is an algorithm defined by the HTML5 specification for [copying complex JavaScript objects](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Structured_clone_algorithm). Why do we need to rely on this algorithm? Data transferred through web workers is **passed as a copy, not as a reference**.

With an understanding of how `postMessage()` work, let's use this method to send a message from the main thread to the worker thread:

```javascript
// src/main.js

const worker = new Worker("../src/worker.js");

worker.onmessage = e => {
  const message = e.data;
  console.log(`[From Worker]: ${message}`);
};

worker.postMessage("Marco!");
```

Let's save our work and open the application preview on its own tab. This can be done by clicking on the **Open In New Window** button present in the navigation bar of the embedded browser:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/speedy-introduction-to-web-workers/CodeSandbox-open-preview-in-new-window.png" alt="CodeSandbox button to open application preview in new tab.">
</p>

In the new preview browser tab, let's open the browser developer console and refresh the page. We should see the following output:

```shell
// [From Main]: Marco!
```

This output in the console confirms that our web worker is listening and reacting to the `message` event sent from `main.js`. Now, we need to reverse the communication. We need to send a message reply from `worker.js` to `main.js`.

### Step 3: Send a Message from Worker Thread to Main Thread

This will be quick. We need to use the `postMessage()` method in the `onmessage` event handler in `worker.js`:

```javascript
// src/worker.js

onmessage = e => {
  const message = e.data;
  console.log(`[From Main]: ${message}`);

  postMessage("Polo!");
};
```

Let's save our work and refresh the preview browser tab. In the console, we should now see the following output:

```shell
// [From Main]: Marco!
// [From Worker]: Polo!
```

We have achieved bi-directional communication between threads, but the communication is short-lived. Let's make this multi-threaded Marco Polo game run infinitely.

### Step 4: Send Messages Between Main and Worker Infinitely

We are going to keep the communication between threads going endlessly. To better pace the back and forth, we are going to rely on [`setTimeout()`](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout) to delay messaging by 3 seconds.

To start, when `main.js` gets a message from `worker.js`, it replies back after 3 seconds:

```javascript
// src/main.js

const worker = new Worker("../src/worker.js");

worker.onmessage = e => {
  const message = e.data;
  console.log(`[From Worker]: ${message}`);

  const reply = setTimeout(() => worker.postMessage("Marco!"), 3000);
};

worker.postMessage("Marco!");
```

Next, when `worker.js` gets a message from `main.js` it also replies back after 3 seconds:

```javascript
// src/worker.js

onmessage = e => {
  const message = e.data;
  console.log(`[From Main]: ${message}`);

  const reply = setTimeout(() => postMessage("Polo!"), 3000);
};
```

The 3 seconds delay creates an eye-friendly pause to be able to see the communication calmly in the developer console. What makes this work infinitely is that every handled message event executes a `postMessage()` response. Before, the `worker.onmessage` in `main.js` did not have a reply within its body.

Let's save our work and head back to the browser preview. Let's refresh the page. After a few seconds, we should see the following output:

```shell
// [From Main]: Marco!
// [From Worker]: Polo!
// [From Main]: Marco!
// [From Worker]: Polo!
```

<video controls style="margin: 35px 0;">
  <source src="https://cdn.auth0.com/blog/speedy-introduction-to-web-workers/web-workers-playing-marco-polo.mp4" type="video/mp4">
   Your browser doesn't support HTML5 video. Here is a <a href="https://cdn.auth0.com/blog/speedy-introduction-to-web-workers/web-workers-playing-marco-polo.mp4">link to Web Workers Marco Polo game video</a> instead.
</video>

This will go on forever until we close the browser tab running the preview of our application. But, we could also terminate the web worker manually. Let's see how we can do that next.

## Terminating a Web Worker

We can terminate web workers from the main thread immediately or from the worker thread.

From the main thread, we can terminate a web worker by calling the [`terminate()`](https://developer.mozilla.org/en-US/docs/Web/API/Worker) method of the Web Workers API:

```javascript
worker.terminate();
```

After `terminate()` is issued, the web worker is destroyed immediately without any chance of completing any ongoing or pending operations. The web worker is also given no time to clean up. Thus, terminating a web worker abruptly may lead to memory leaks.

We can also terminate a web worker from the worker thread using its own [`close`](https://developer.mozilla.org/en-US/docs/Web/API/DedicatedWorkerGlobalScope/close) method:

```javascript
close();
```

Upon calling `close()`, any queued tasks present in the event loop are discarded and the web worker scope is closed.

Checking the documentation for `close()` may be confusing at first because there is a version of the [`close()` method that has been deprecated](https://developer.mozilla.org/en-US/docs/Web/API/WorkerGlobalScope). The deprecated version belongs to the `WorkerGlobalScope` interface. In reality, there are two types of web workers that we can create: dedicated and shared web workers. Each web worker type has its own interface, [`DedicatedWorkerGlobalScope`](https://developer.mozilla.org/en-US/docs/Web/API/DedicatedWorkerGlobalScope) and [`SharedWorkerGlobalScope`](https://developer.mozilla.org/en-US/docs/Web/API/SharedWorkerGlobalScope/close) respectively. For the scope of this introduction, we've used a dedicated web worker under the hood. The difference between these two types of web workers and how and where to use them will be addressed in a future post along with best practices on terminating workers!

## Recap

We've learned the basics of how to create a web worker. We learned how to effectively send messages between two threads and how to react to those messages. We briefly touched on the subject of terminating web workers. This last task is to be handled with care and deserves a more detailed explanation. Badly terminated web workers may lead to memory leaks in the application.

> The complete code sample used in this blog post is present in [this JavaScript CodeSandbox](https://codesandbox.io/s/xp901z23o4). Feel free to fork it.

What's left to learn? A lot! Web workers have been around for a long time and they are great at executing expensive logic. This logic will be much more complex than what we've done in this blog post. We'd need to learn topics like handling errors, spawning subworkers, using external libraries, and monitoring web workers using developer tools.

Please let me know in the comments how you liked this introduction to web workers and what else you'd like to learn about this handy technology that lets us perform parallel programming in JavaScript.

{% include asides/about-auth0.markdown %}
