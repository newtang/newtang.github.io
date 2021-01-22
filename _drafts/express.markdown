---
layout: post
title:  "A better router for express"
date:   2020-05-28 16:23:00 -0800
categories: programming javascript
---

This all started when I was doing some digging into the internals of [express](https://expressjs.com) for [work](https://mapbox.com). I noticed its router, the logic that matches a url path to the appropriate handler, was surpisingly naive and not particularly efficient. Essentially, every route that's registered is converted to a regular expression, and the path of incoming requests are checked against every single regex, until it matches.

There's a lot I like about Express; it has an easy learning curve, can be quite powerful, and has an elegant API. But, I wondered if we could do better than running every request against (potentially) every registered regex. It's arguably one of the most important and well-used Node libraries in the world, and this strategy seemingly hasn't seen any optimization in years. If a service provides 100 routes, for the 100th to be accessed, it's going to have to run through all 100 regexes. For a tiny service, running a regular expression isn't _that_ costly, but as services get bigger, and traffic gets more intense, these inefficiencies can make a difference.

So, I started wondering if I could make a new, faster Express. As I continued to study the code, I realized the Express router wasn't as tightly coupled as I originally thought. In fact in the alpha version of Express 5, the router is removed completely and resides in [its own distict npm project](https://github.com/expressjs/express/blob/5.x/lib/express.js#L18).

So, my project pivoted; are there other routers I could drop into Express? Well, sort of! I found [this repo](https://github.com/delvedor/router-benchmark) which listed several routers and their benchmarks. Almost all of them are significantly faster than Express' router, especially [koa-tree-router](https://www.npmjs.com/package/koa-tree-router)
 and [find-my-way](https://www.npmjs.com/package/find-my-way). But, are they express compatible? And, what does it mean to be express compatible? This leads me to the first major part of my project; assessing compatibilty and features. 

I created a series of [express-router-compatibility tests](https://github.com/newtang/express-router-compatibility) on 6 routers. I also experimented with [Moa](https://www.npmjs.com/package/moa-router), which looked promising but its Express example didn't work. I also tried [@hapi/call](https://www.npmjs.com/package/@hapi/call), but it functioned far too differently, and I had a difficult time integrating it into Express.

I tried to assess how easy it was to replace the Express router with these various other routers by testing expected features. This is a lot like sticking a square peg in a round hole; some tests were straightforward, others, tricky. I wrapped each router in an [Adapter](https://en.wikipedia.org/wiki/Adapter_pattern). Some tests required getting things set up in a non-typical Express way, in order to check the compatibilty of other features. It's difficult to justify making a small changes and calling it "supported", but I did my best. For example, Find-My-Way claims to support regular expression routes, but it does so in an unusual way where a regex must be inside parenthesis after a parameter name in a string, like so: `'/example/:file(^\\d+).png'`. Because it's different than the Express Router way of handling regex, it fails the compatibility check.  

Additionally, Find-My-Way required a custom wrapper to function as Express middleware. Because of this, it can technically function with the default `req, res, next` arguments so it passes that compatibility check. Like I said, this is a little more art than science.

The Express router obviously passed all the compatibility tests. Both Koa-routers were fairly compatible as well, even though they were made with [Koa](https://koajs.com) in mind. The others were spottier.

There weren't many tests that passed across the board. Unsurprisingly, all routers support static routes and parameterized routes the same way Express' router does. Wildcard routes were almost universal, but koa-tree-router opted for a different approach when supporting them.

Is there a router I forgot? Or a test that can be added or improved? Please, feel free to make a pull request.




