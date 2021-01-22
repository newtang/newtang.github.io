---
layout: post
title:  "A better router for express Part 2"
date:   2020-05-28 16:23:00 -0800
categories: programming javascript
---

In early summer of 2020, I broke ground on a new router for express. After tinkering with it on and off for the last several months, I'm happy to share that the first public beta version of Expresso is now available!

My goals were to make the Expresso faster than the default Express router, reasonably backwards compatible, throw helpful errors on problematic setup, and finally, to allow routes to be added in an order-independant manner. Let's go through these!

### Speed

I tweaked router-benchmark to include Expresso, and I included the results in my API doc. For static routes, Expresso wins hands down.

```sh

====================
 expresso benchmark
====================
short static: 8,375,979 ops/sec
static with same radix: 8,533,307 ops/sec
long static: 7,822,262 ops/sec

==========================
 express router benchmark
==========================
short static: 1,676,429 ops/sec
static with same radix: 1,590,129 ops/sec
long static: 829,426 ops/sec

```

For parameterized routes, Expresso-router is again faster, but the difference isn't as stark.

```sh
====================
 expresso benchmark
====================
dynamic route: 928,018 ops/sec
mixed static dynamic: 765,725 ops/sec

==========================
 express router benchmark
==========================
dynamic route: 743,979 ops/sec
mixed static dynamic: 613,461 ops/sec
```

The truth is, I feel pretty confident I left some optimization on the table, but I wanted to get this out into the public. Also, I want to point out that the default express router can get slower as more routes are added. For example, if it has 100 parameterized routes, it would run through all of them until it found a match. Expresso-router, utilizes a tree, so it scales much more efficiently. 

### Backwards compatible 

The API for Expresso is basically identical to the default Express router. However, there are a few features missing like wildcards, and parameters in the use function.

### Errors





Somewhat naively, this project took me a little longer than I intended. There were a lot of details to get right, and there's some interesting features the default express router does that are easy to forget. For example it handles all HEAD and OPTIONS requests, and carefully adjusts the `url` and `baseUrl` properties when multiple routers are entered or left. 

Because it took longer than estimated, I sort of regret doing this project. It wasn't particularly distinct from my [day job](https://mapbox.com), so I started to lose a little bit of steam towards the end.Hopefully, someone finds it useful! I'll probably be more reinvigorated to add the missing features if someone finds Expresso useful, but I'll probably be taking a break.



If your codebase follows some wonky fallthrough patterns, expresso might not work for you.

`router.get(/^api*/, (req, res, next) => next())`
`router.get("/api/", (req, res, next) => doSomething())//won't get called`

alternatives:
router.get("/api/", doSomething, (req, res, next) => () );
`express.use(/^api*/, (req, res, next) => doSomething());` //check this one

There's some interesting things the Router does for you that you forget about. It handles all HEAD and OPTIONS requests for you.

Maybe it wasn't worth doing, but I hope it was!






Blog post
 - motivation
 - things that went well (testing againt router)
 - not so well
 - still some optimization and features left on the table
 - longer than I expected
 - where will this go?
 - regret?






 



--Do I need to include this stuff?  Maybe it's helpful for part 2--

 There are a few things I don't like about the Express router. First, two of the same route can be declared:

```
  router.get('api/v1/:user', (req, res, next) => {/*do something*/}
  router.get('api/v1/:user', (req, res, next) => {/*do something else*/}
```

This is legal. And, technically, if the first function calls `next`, it will fall into the second one. Personally, I think this it's potentially misleading, and a bit of an antipattern. Either have two middlewares for this route, or combine them into a single function. No need to have both.



 Second, the order in which routes are declared matters. Take this example:

 ```
 router.get('api/v1/:user', (req, res, next) => {...});
 router.get('api/v1/help', (req, res, next) => {...}
 ```

`api/v1/help` will never get called, because it was declared after the parameterized route


Blog post part 1 notes
* Started with digging into Express code base

* Looked into handle 405s through provided methods. Doesn't work out well