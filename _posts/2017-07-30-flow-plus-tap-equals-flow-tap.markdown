---
layout: post
title:  "Flow + tap = flow-tap, my first npm module"
date:   2017-07-30 18:15:00 -0700
categories: javascript npm flow tap
---

Although I use Javascript a lot these days, I miss having strong types. Linters like [ESLint]({{site.url}}/posts/eslint-rules) ameliorate the problem by catching tons of bugs, but it doesn't get everything. Lately, [Flow](https://flow.org) and [Typescript](https://www.typescriptlang.org/) have been garnering lots of attention for adding types to Javascript. I don't know which one is better; they are syntactically similar and both seem pretty great. [There](http://djcordhose.github.io/flow-vs-typescript/2016_hhjs.html#/) [are](https://medium.com/@ckoster22/migrating-from-flow-to-typescript-b065796797db) [plenty](https://blog.mariusschulz.com/2017/01/13/typescript-vs-flow) [of](https://michalzalecki.com/typescript-vs-flow/) [articles](https://michalzalecki.com/typescript-vs-flow/) [comparing](https://www.reddit.com/r/javascript/comments/39cere/typescript_vs_flow_results_from_our_investigation/) [them](https://blog.wearewizards.io/flow-and-typescript-part-1-flow). I've been using Flow because I already had a largish Javascript project, and I wanted to gradually introduce static types in over time. If I was starting from scratch, I would have strongly considered Typescript since it's actually a language, but I digress.

I use [tap](http://www.node-tap.org/) for my unit tests. It doesn't seem to be quite as popular as [MochaJS](https://mochajs.org/), but I appreciate the simplicity. As I started using Flow, my tests would complain about all the foreign characters from my type annotations. I found [babel-tap](https://www.npmjs.com/package/babel-tap) which worked great, but it slowed my tests down significantly. I wanted to use something like [flow-remove-types](https://github.com/flowtype/flow-remove-types), but there was no handy integration with tap. So, I built and published [my first npm module: flow-tap](https://www.npmjs.com/package/flow-tap)!

It's inspired by babel-tap and extremely simple and fast. The primary function is that it will remove all your flow type annotations and allow tap to Just Work™. After installation, replace `tap` with `flow-tap` in your package.json file like so:
{% highlight json %}
"scripts": {
	"test": "flow-tap test/**/*.js"
}
{% endhighlight %} 

That's it! Run `npm run test` and you're good to go.

### Tests
Now that I'm using flow annotations more, I'm actually able to eliminate a bunch of tests! I would always have some tests that passed null, undefined, or some other inappropriate argument (like a string where a number is expected, etc) to my test target. But now that Flow catches any unexpected types being passed to a method, I don't need tests like these. **Strong typing eliminates the need for a lot of tests.** The same sort of thing happens in Java or C# or any other strongly typed language; the compiler protects you.

### Code
The module is simple. It's essentially a few lines in one file named cli.js
{% highlight javascript %}
#!/usr/bin/env node
'use strict';
process.execPath = require.resolve('.bin/flow-node');
require('tap/bin/run.js');
{% endhighlight %}

Overwriting [`process.execPath`](https://nodejs.org/api/process.html#process_process_execpath) is probably a bit of a hack, but it works well. If there's a more kosher way of doing this, please let me know. Also, [`require.resolve`](https://nodejs.org/api/modules.html#modules_require_resolve) is a neat way to get the path of a module.

The package.json file has a handy ["bin" property](https://docs.npmjs.com/files/package.json#bin) for executable files which was perfect for my cli.js

### Choosing a license.
Before publishing, I wanted to choose an open source license. I really don't know a lot about this, but [choosealicense.com](https://choosealicense.com/) was really handy. I went with a common [MIT license](https://choosealicense.com/licenses/mit/). 

### Publishing to npm
[Publishing to npm is incredibly easy](https://docs.npmjs.com/getting-started/publishing-npm-packages). At first I was hesitant to publish because I wanted a staging area to make sure everything worked, and I didn't want to increase the version number a bunch of times, etc. Well, it worked as I had planned, and I had to keep increasing the version anyways as I made documentation improvements. Admittedly, I do find it a tad annoying that I have to increase the version number when I update the readme, but I'll live. Fortunately, [npm version patch](https://docs.npmjs.com/cli/version) handily increases the version number for me.

On the occasions I've thought about publishing an npm module, I usually discover something similar that already exists, or bureaucratic red tape dissuades me from open sourcing, so I'm happy I had the opportunity to publish this. Hopefully it won't be the last one! Oh, and let me know if flow-tap comes in handy for you.

**EDIT:** It turns out this may have been for naught, as [@izs pointed out](https://twitter.com/izs/status/891866449907601409) you can do roughly the same thing with node-tap's [node-arg](http://www.node-tap.org/cli/) flag, which I was unaware of. Oh well! Still a good learning experience nonetheless.


