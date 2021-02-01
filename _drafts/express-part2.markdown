---
layout: post
title:  "A better router for express Part 2"
date:   2020-05-28 16:23:00 -0800
categories: programming javascript
---

[After fiddling around with router compatibility](/posts/express-router-part-1), in early summer of 2020, I broke ground on a new router for Express. After tinkering with it on and off for the last several months, I'm happy to share that the first public version of Expresso is now available!

My goals were to make Expresso faster than the default Express router, reasonably backwards compatible, throw helpful errors on problematic setup, and finally, to allow routes to be added in an order-independent manner. Let's step through these!

### Speed

I tweaked [router-benchmark](https://github.com/delvedor/router-benchmark) to include Expresso, and I included the results in my API doc. For static routes Expresso wins hands down, at least according to my quick and dirty local environment.

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

For parameterized routes (ie `/api/v1/:user`), Expresso-router is again faster, but the difference isn't as stark.

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

The truth is, I'm certain I left some optimization on the table, particularly for parameterized routes, but I wanted to get this out into the public. Also, I want to point out that the default express router can get slower as more routes are added. For example, if it has 100 parameterized routes, it would run through all of them until it found a match. Expresso-router utilizes a tree, so it scales much more efficiently. 

### Backwards compatible 

The API for Expresso is basically identical to the default Express router. However, there are a few missing features like wildcards, and support for parameters in the `use` function.

### Errors

If possible, it's preferable to make the right thing easy to do, and make the wrong thing, like a source of common mistakes, something destructive, or a suboptimal decision, more difficult. Expresso tries to warn users about common mistakes on setup, but allows some optional, explicit overrides. Here's a couple examples.

```js
	const router = expresso();
	router.get('/api', () => {...});
	router.get('/api', () => {...}); //throws exception for duplicate route
```

```js
	const router = expresso();
	router.get('/id/:id', () => {...});
	router.get('/id/:value', () => {...}); //throws exception for duplicate route
```

```js
	const router = expresso({allowDuplicatePaths: true});
	router.get('/api', () => {...});
	router.get('/api', () => {...});

	router.get('/id/:id', () => {...});
	router.get('/id/:value', () => {...});
```

Although these compact examples might look a little silly or improbable, these errors are more likely to manifest themselves if the routes are distributed across multiple files.

### Order Independence 

Order independence is a big feature for Expresso. In the default Express router, this situation was possible:

```js
router.get('/api/v1/:user', (req, res) => {res.send(req.params.user)});

// will never get called in Express, but will get called in Expresso-Router
router.get('/api/v1/settings', (req, res) => {res.send('settings')});

```

In the above example, a GET request to `/api/v1/settings` will never trigger the second route in the default Express router because the first one would match even though it's less specific. However, with Expresso, this is no longer a concern. Routes are order independent, and Expresso will check the most specific route first.

### Conclusion

Naively, this project took me a little longer than I intended. There were a lot of details to get right, and there's some interesting features the default express router does that are easy to overlook. For example it handles all HEAD and OPTIONS requests, and carefully adjusts the `url` and `baseUrl` properties when multiple routers are userd. I took the time to go through the default router's tests to maximize backwards compatibility where I could.

Because it took longer than estimated, I have some mild regret taking on this project. It wasn't particularly distinct from my [day job](https://mapbox.com), so I started to lose a little bit of momentum towards the end. I'll probably be more reinvigorated to add missing features if someone finds Expresso useful.

