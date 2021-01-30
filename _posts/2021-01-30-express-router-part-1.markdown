---
layout: post
title:  "A better router for Express - Part 1"
date:   2021-01-30 14:45:00 -0800
categories: programming javascript
---

It all started when I was doing some digging into the internals of [Express](https://expressjs.com) for [work](https://mapbox.com). I noticed its router, the logic that matches a url path to the appropriate handler, was not particularly efficient. Essentially, every path that's registered is converted to a regular expression, and the path of incoming requests are checked against every regex, until it matches.

There's a lot I like about Express; it has an easy learning curve, and an intuitive API. It's arguably one of the most important and well-used Node libraries in the world, but its routing strategy hasn't seen any optimization in years. If a service provides 100 routes, it may have to iterate through all 100 to find a match. For a tiny service, running a regular expression isn't _that_ costly, but as services get bigger, and traffic gets more intense, these inefficiencies can make a difference.

So, I started wondering if I could make a new, faster Express. As I continued to study the code, I realized the Express router wasn't as tightly coupled as I originally thought. In fact, i the alpha version of Express 5, the router is removed completely and resides in [its own distinct npm project](https://github.com/expressjs/express/blob/5.x/lib/express.js#L18).

So, my project pivoted; are there other routers I could drop into Express? Well, sort of! I found [this repo](https://github.com/delvedor/router-benchmark) which listed several routers and their benchmarks. Almost all of them are significantly faster than Express' router, especially [koa-tree-router](https://www.npmjs.com/package/koa-tree-router)
 and [find-my-way](https://www.npmjs.com/package/find-my-way). But, are they compatible with Express? And, what does it even mean to be compatible? This leads me to the first major part of my project; assessing compatibility and features. 

I created a series of [express-router-compatibility tests](https://github.com/newtang/express-router-compatibility) on 6 routers. I also experimented with [Moa](https://www.npmjs.com/package/moa-router), which looked promising but its Express example didn't work. I also tried [@hapi/call](https://www.npmjs.com/package/@hapi/call), but it functioned far too differently.

I tried to assess how easy it was to replace the default Express router with these other routers by testing expected features. This is like fitting a square peg in a round hole; some tests were straightforward, others, tricky. I wrapped each router in an [Adapter](https://en.wikipedia.org/wiki/Adapter_pattern). Some tests required additional set up in order to check feature compatibility. It's difficult to justify making a small changes and calling it "supported", but I did my best. For example, Find-My-Way claims to support regular expression routes, but it does so in an unusual way where a regex must be inside parentheses after a parameter name in a string, like so: `'/example/:file(^\\d+).png'`. Because it's different than the Express Router way of handling regex, and there's really no way around it, it fails the compatibility check.  

Additionally, Find-My-Way required a custom wrapper to function as Express middleware. As a result, it can technically function with the standard `req, res, next` middleware arguments so it passes that compatibility check. Like I said, this is a little more art than science.

The Express router obviously passed all the compatibility tests. Both Koa-routers were fairly compatible as well, even though they were made with [Koa](https://koajs.com) in mind. The others were spottier. There really isn't an alternative router that's close to drop-in ready that I could find.

So, I decided to build a new router for Express! Part 2 coming soon.


