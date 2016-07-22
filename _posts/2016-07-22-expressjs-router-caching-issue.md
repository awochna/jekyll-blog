---
title: ExpressJS Router Caching Issue?
tags:
  - javascript
  - node
  - express
  - technical
---

It's a fairly common pattern, in my experience, to prepare some routes for your [express](http://expressjs.com) application in a separate file (module) and export them.
Then they can be imported in a file that's only responsible for putting your server together, with routes and middleware.
It's only one step removed from a solution I'm using in a current project in which I export a function that configures and returns a middleware.
Something like this:

```javascript
var express = require('express');
var router = express.router();

module.exports = function (options) {

  // Do some stuff with the options, define some routes, etc.

  return router;
};
```

This makes sense, and in our `server.js` file, where we put everything together:

```javascript
var express = require('express');
var app = express();
var fooRoutes = require('lib/foo-routes');

app.use('/foo', routes);
```

This works great in this setting, but not so much in testing.

The idea of exporting a function that returns the thing you really want to export helps us get around Node's module caching.
Essentially, when your run `require('some-module')`, Node looks for that module and then compiles the resulting code and caches that.
Then, successive calls to that same module using `require()` are much faster.

If this sounds like trouble, it is.

People will often export functions so they can get back a new instance of the thing they want, even if there is no configuration step.
In my case, that's some predefined routes.
Node runs all of the code and then caches (but not executes) the function I'm exporting.
This means that, in my tests, I can require this module multiple times and call the function with different sets of options and get a different result based on those options, right?

Wrong.

Normally that would be the case, but I made a simple mistake that caused me to eventually just start putting `console.log()`'s everywhere so I could try to understand what was going on.
Basically, what I was seeing is that the second set of tests that I imported my routes for were behaving exactly the same as the first set of tests where I imported my routes.
As if it was cached or something.
Four hours later, I still hadn't figured anything out, except that the second set of routes thinks it's the same set as the first.
Eventually, I went for a walk (something I tend to do when I have a particularly hard problem or a stumbling block), and came back to look at the code again, with a more clear mind.
I started reading it over and thought, "Hey, maybe I should try instantiating the router inside the function.
Maybe *that's* what's cached."

Yeah, that was it.
I changed the above to look more like:

```javascript
var express = require('express');

module.exports = function (options) {
  var router = express.router();

  // Do some stuff with the options, define some routes, etc.

  return router;
};
```

I hope that helps you if you've stumbled on this page by searching because you're encountering a similar issue.
What seems to have happened was that successive calls to the exported function were trying to use the same `Router` object that already had some configured routes declared.

The lesson:

* Scope your variables as low as possible?

Yeah, I think it's that one.
