---
layout: post
title:  "Exploring the uses of next() in ExpressJS"
date:   2017-09-09 16:50:00
description: Some explanations and explorations about how next() in ExpressJS middleware can be helpful.
categories:
- blog
- JavaScript
- ExpressJS
- middleware
- NodeJS
---

When our Turing cohort explored ExpressJS this module, there was a quick comment at the bottom of one of our lessons about the `next()` function in ExpressJS. While I was in the middle of a project figuring out API endpoints and using [Knex](http://knexjs.org/){:target=>"blank"} with NodeJS, I could not fully absorb the possibilities of `next()`.  Having now gone through that project, I wanted to circle back and gain a better understanding of how `next()` worked and how to look for patterns where it might be useful.  In short, `next()` sends the request to the next function (those clever developers with their fancy names) that handles the request. Still unclear?  I know it certainly was when I first looked at `next()`.

## Routes VS Middleware
The first thing I had to understand was the difference between Routes and Middleware.  The [last tutorial](http://qnimate.com/express-js-middleware-tutorial/){:target=>"blank"} in the resources list below really helped me wrap my head around this.  The TLDR; of the difference is that routes are responsible for concluding a successful request (usually by `.send()`ing a response). Middleware, on the other hand, sits between the request and the routes and does things to the request (including possibly terminating it early because of a problem).  One of the confusing things is that ExpressJS can have multiple actions on a single route using `next()`. Let us explore this with just routes for now:

```javascript
var app = require("express")();

app.get("/", function(request, response, next){
  console.log("step 1");
  next();
});

app.get("/", function(request, response, next){
  console.log("step 2");
  end();
});
```

Would log:
```
step 1
step 2
```
when the user visits the root path.  We can also achieve the same thing with middleware and a route (note `app.get()` changes to `app.use()` for the first function):

```javascript
var app = require("express")();

app.use("/", function(request, response, next){
  console.log("step 1");
  next();
});

app.get("/", function(request, response, next){
  console.log("step 2");
  end();
});
```

Both of these examples show we can have multiple steps to our app when the user visits the root path.  

At this point we have only really illustrated that `next()` passes the request to the next function in line. Let's explore a bit further with a more real world scenario than `console.log`s.  Suppose we have a user profile page, which we only want to show it to the user who is logged in.  We can accomplish this with either a route or a middleware, but since the job of the function is to look at the request and decide to send it on, it feels more like a middleware usecase than a middleware chain.  This is how I would structure that requests functions:

```javascript
var app = require("express")();

app.use("/user-profile", function(request, response, next){
  // check that the user is signed in.
  // if she/he is:
      next();
  // else
  // return a not logged in message, redirect to the login page, or just send her/him a 404
});

app.get("/user-profile", function(request, response, next){
  request.send('Your profile page');
});
```

This middleware functionality can also be used for request error handling and redirects (i.e. maybe the user entered a page slug that doesn't exist and needs to be directed to an all-posts page).
Other uses of `next()` and middleware:
* Writing to logs
* Serving static assets
* Tracking what time a request was received

Also, watch out that you use middleware first and then your routes.  If you have
```
Middleware 1 -> next
Route 1 -> next
Middleware 2 ->
Route 2 -> send
```

`Middleware 2` will never get hit because `next()` in a route sends to the next route function never a middleware function.

Middleware are waht allow us to expand and shap the functionality of Express to fit our specific needs.  `next()` allows us to be more explicit about that functionality. In my next project, which will blend ExpressJS with Ethereum contracts. I will be using this technique to check if the user has MetaMask installed or an RPC connection available. If they do not, then I will `next()` them to an instructions page that explains how to get those set up.


Resources:
- [Writing middleware for use in Express apps - Express Guides](https://expressjs.com/en/guide/writing-middleware.html){:target=>"blank"}
- [Express next function, what is it really for? - StackOverflow](https://stackoverflow.com/questions/13133071/express-next-function-what-is-it-really-for){:target=>"blank"}
- [Using middleware - Express Guides](https://expressjs.com/en/guide/using-middleware.html){:target=>"blank"}
- [Express.js Middleware Tutorial](http://qnimate.com/express-js-middleware-tutorial/){:target=>"blank"}