---
layout: post
title:  "A better router for express Part 2"
date:   2020-05-28 16:23:00 -0800
categories: programming javascript
---

If your codebase follows some wonky fallthrough patterns, expresso might not work for you.

`router.get(/^api*/, (req, res, next) => next())`
`router.get("/api/", (req, res, next) => doSomething())//won't get called`

alternatives:
router.get("/api/", doSomething, (req, res, next) => () );
`express.use(/^api*/, (req, res, next) => doSomething());` //check this one

There's some interesting things the Router does for you that you forget about. It handles all HEAD and OPTIONS requests for you.

Maybe it wasn't worth doing, but I hope it was!


I sort of regret doing this project. It took me longer than anticipated, it wasn't particularly distinct from my [day job](https://mapbox.com), and I don't think I learned as much as I hoped to. Hopefully, someone finds it useful!