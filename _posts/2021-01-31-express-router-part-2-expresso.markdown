---
layout: post
title:  "A better router for Express - Part 2: Expresso"
date:   2021-01-31 23:35:00 -0800
categories: programming javascript expresso
---

[In my previous post](/posts/express-router-part-1), I noted that the default router for Express, an extremely popular Node module, hasn't seen any substantial optimization in years. So, I was curious if I could make a faster, drop-in replacement router, and in early summer of 2020, I broke ground on this project. In the ensuing months, I made incremental progress, and I'm thrilled to share that the [first public version of Expresso is now available](https://www.npmjs.com/package/expresso-router)!

```sh
npm i expresso-router
```

My goals were to make Expresso faster than the default Express router, reasonably backwards compatible, throw helpful errors on problematic setup, and finally, to allow routes to be added in an order-independent manner. Let's walk through these!

### Speed

I tweaked [router-benchmark](https://github.com/delvedor/router-benchmark) to include Expresso, and I included the results in my API doc. For static routes Expresso wins hands down, at least according to my quick and dirty local testing environment.

```sh

====================
 expresso benchmark
====================
short static: 8,375,979 ops/sec
static with same radix: 8,533,307 ops/sec
long static: 7,822,262 ops/sec

=================================
 default express router benchmark
=================================
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

=================================
 default express router benchmark
=================================
dynamic route: 743,979 ops/sec
mixed static dynamic: 613,461 ops/sec
```

The truth is, I'm certain I left some optimization on the table, particularly for parameterized routes, but I was getting anxious to share this with the public. Also, I want to point out that the default express router can get slower as more routes are added. For example, if it has 100 routes, it would run through all of them until it found a match. Expresso-router utilizes a tree for parameterized routes, so it scales much more efficiently. 

### Backwards compatible 

The [API for Expresso is basically identical to the default Express router](https://github.com/newtang/expresso/blob/HEAD/API.md#api-1) . However, there are a few missing features like wildcards, and support for parameters in the `use` function. I have [issues](https://github.com/newtang/expresso/issues) filed for several of these, and some [additional details in the Migration section](https://github.com/newtang/expresso/blob/HEAD/API.md#migration).

### Errors

If possible, it's preferable to make the right thing easy to do, and make the wrong thing, like a source of common mistakes, something destructive, or a suboptimal decision, more difficult. Expresso tries to warn users about common mistakes on setup, but [allows some optional, explicit overrides in the constructor](https://github.com/newtang/expresso/blob/HEAD/API.md#config). Here's a couple examples.

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

// will never get called in default Express router, but will get called in Expresso 
router.get('/api/v1/settings', (req, res) => {res.send('settings')});

```

In the above example, a GET request to `/api/v1/settings` will never trigger the second route in the default Express router because the first one would match even though it's less specific. However, with Expresso, this is no longer a concern. Routes are order independent, and Expresso will check the most specific route first.

### Conclusion

Naively, this project took a little longer than I intended. There were a lot of details to get right, and there's some interesting features the default Express router has that are easy to overlook. For example, it handles all HEAD and OPTIONS requests, and carefully adjusts the `url` and `baseUrl` properties on the `req` object when multiple routers are used. A lot of these I discovered while going through the default router's tests to maximize backwards compatibility where I could.

Because it took longer than estimated, I have some mild regret taking on this project. It wasn't particularly distinct from my [day job](https://mapbox.com), so I started to lose a little bit of momentum towards the end. I'll probably be reinvigorated to add missing features if someone finds Expresso useful.

