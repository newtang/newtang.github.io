---
layout: post
title:  "Flow, tap and my first published npm module: flow-tap"
date:   2017-01-17 11:35:00 -0800
categories: javascript npm flow tap
---

Although I use Javascript a lot these days, I have missed having strong types. Linters like [ESLint]({{site.url}}/posts/eslint-rules) ameliorate the problem a bit by catching tons of bugs, but not everything. Lately, [Flow](https://flow.org) and [Typescript](https://www.typescriptlang.org/) have been garnering lots of attention for solving this very problem. I don't know which one is better; they are syntactically similar and both seem pretty great. I've been using Flow because I already had a largish Javascript project, and I wanted to gradually introduce static types in over time. If I was starting from scratch, I would more strongly consider typescript since it's actually a language, but I digress.

I use [tap](http://www.node-tap.org/) for my unit tests. It doesn't seem to be quite as popular as [MochaJS](https://mochajs.org/), but I appreciate the simplicity. As I started using Flow, my tests would complain about all the foreign characters from my type annotations. I found [babel-tap](https://www.npmjs.com/package/babel-tap) which worked great, but it really slowed my tests down signficantly. I wanted to use something like [flow-remove-types](https://github.com/flowtype/flow-remove-types), but there was no handy integration with tap. So, I built and published my first npm module: [flow-tap](https://www.npmjs.com/package/flow-tap). 

It's inspired by babel-tap and extremely simple. The primary function is that it will remove all your flow type annotations and allow tap to just work. If you install it `npm install flow-tap`, and replace `tap` with `flow-tap` in your package.json file like so:
`"scripts": {
	"test": "flow-tap test/**/*.js"
}`



### Tests
Now that I'm using flow annotations more, I started putting '//@flow' at the top of my tests, and I actually got to eliminate a bunch of tests! I would always have some tests that pass null, undefined, or some other inappropriate argument (like a string where a number belongs, etc). But now that Flow catches this stuff (and complains about these tests), I really don't need them. **Strong typing eliminates the need for these kinds of tests.** The same sort of thing happens in Java or C# or any other strongly typed language; the compiler protects you.

### Building it
The module is incredibly simple. It's essentially a few lines in one file named cli.js
{% highlight javascript %}
#!/usr/bin/env node
'use strict';
process.execPath = require.resolve('.bin/flow-node');
require('tap/bin/run.js');
{% endhighlight %}


### Choosing a license.
Before publishing, I wanted to choose an open source license. I really don't know a lot about this, but [choosealicense.com](https://choosealicense.com/) was really handy. I went with a common [MIT license](https://choosealicense.com/licenses/mit/). 

### Publishing to npm
[Publishing to npm is incredibly easy](https://docs.npmjs.com/getting-started/publishing-npm-packages). At first I was hesitant to publish because I wanted a staging area to make sure everything worked, and I didn't want to increase the version number a bunch of times, etc. Well, it worked as I had planned, and I had to keep increasing the version anyways as I made documentation improvements. Honestly, the internet is a big place, and I just had to get over myself. Admittedly, I do find it a tad annoying I have to increase the version number when I update the readme, but it's really not that big of a deal.







bin directory

Writing tests that test a bunch of nonsensical input.

babel-node, 

Had to patch version to update README and License


https://choosealicense.com/
babel-tap


Easy to publish. No staging area to double check, but if you have to update the version number, nbd.