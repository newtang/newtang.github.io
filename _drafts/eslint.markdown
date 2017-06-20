---
layout: post
title:  "ESLint Rules that ESLint Rules"
date:   2017-06-17 11:35:00 -0800
categories: javascript eslint
---

[Title inspired by The Onion](http://www.theonion.com/article/supreme-court-rules-supreme-court-rules-998)

I recently embarked on a new [Node.JS](https://nodejs.org) project, and after having used [JSHint](http://jshint.com/) for several previous projects, I wanted to read up on [ESLint](http://eslint.org/) and give it a shot. I'm a big fan of static analysis tools particularly for untyped languages like Javascript. They catch a ton of bugs by enforcing best practices and looking for known pitfalls. They also help keep your code clean and consistent. I **strongly** recommend using a linter, and it's much easier to start using one earlier than later in a project. Anyways, it didn't take long to realize that ESLint is wildly superior to it's predecessors for several reasons:

 * There are way more rules (this is a good thing)
 * Easy to configure
 * Several gradations within many of the rules
 * Special rules for Node, ES6, ES7, browsers, and even React
 * It's extensible if you want to build your own rules.
 * Excellent documentation, with plenty of examples.
 * Well designed console output
 * Well maintained
 * Easy to ignore a rule with [special comment](http://eslint.org/docs/user-guide/command-line-interface#--no-inline-config)
 * Handy `--fix` directive to auto-fix certain rules.
 * You can override the rules on a per folder basis with .eslint files. This is useful if you have browser and server Javascript in the same repo.
 * Helpful [Gitter channel](https://gitter.im/eslint/eslint)

I know I'm a little behind the times here, but I'm fully on the ESLint train. [Here's my whole set of rules](https://github.com/newtang/eslint) which extend the [ESLint recommended rules](http://eslint.org/docs/rules/). Since I'm early in my project, I have a feeling some of these will change if I get too annoyed by them. I have them all set to error. In my experience, people tend to ignore compiler/linter warnings, so I prefer errors to enforce build failures. My choices were based on: 
 * Will these catch likely errors and ensure good decisions by future me and potential future collaborators?
 * Is this a rule I would want enforced >90% of the time?

I don't want to talk about every single rule, so I'll arbitrarily bunch them up:

NodeJS specific
 * [callback-return](http://eslint.org/docs/rules/callback-return)
 * [handle-callback-err](http://eslint.org/docs/rules/handle-callback-err)
 * [no-path-concat](http://eslint.org/docs/rules/no-path-concat)
 * [no-process-env](http://eslint.org/docs/rules/no-process-env)
 * [no-process-exit](http://eslint.org/docs/rules/no-process-exit)
 * [no-restricted-modules](http://eslint.org/docs/rules/no-restricted-modules): ["error", "assert", "nconf", "winston"],
 * [no-sync](http://eslint.org/docs/rules/no-sync)

Callback returns have gotten me a few times. I use a callback in the middle of a function, but since I didn't return the rest of the function gets executed which is rarely desirable. I also love the restricted modules rule. Occasionally, I use wrappers around some libraries and this helps enforce the usage of the wrapper instead of the library. In Node, it's preferred to avoid synchronous io, so you should have a good reason for using it.

Holy wars
 * [curly](http://eslint.org/docs/rules/curly)
 * [indent](http://eslint.org/docs/rules/indent): ["error","tab"]
 * [linebreak-style](http://eslint.org/docs/rules/linebreak-style): ["error","unix"]
 * [max-len](http://eslint.org/docs/rules/max-len): ["error", {"code": 156}],
 * [quotes](http://eslint.org/docs/rules/quotes): ["off"]

I don't want to get into a space vs tab or single vs double quote debate. I like these rules because they can enforce the style (or lack there of) of your choosing, and you don't have to worry about all sorts of weird git commit diffs. I happen to prefer enforcing curly braces for if-statements, and I don't care much about single vs double quotes, but that's just me. I don't know if max-len is a holy war exactly, but I have it set so that I don't have to horizontally scroll on my current screens configuration.

Classic
 * [eqeqeq](http://eslint.org/docs/rules/eqeqeq)  
 * [no-eval](http://eslint.org/docs/rules/no-eval)
 * [no-implied-eval](http://eslint.org/docs/rules/no-implied-eval)
 * [no-loop-func](http://eslint.org/docs/rules/no-loop-func)
 * [radix](http://eslint.org/docs/rules/radix)
 * [semi](http://eslint.org/docs/rules/semi)

I remember these from JSLint and JSHint. I prefer semicolons and I think eqeqeq is still a good practice. The radix rule might be a little dated because as of ES5, you no longer have to worry about pesky octals, but hexadecimal numbers can cause unwanted results. And, avoiding eval remains wise.

ES6 and ES7
 * [no-await-in-loop](http://eslint.org/docs/rules/no-await-in-loop)
 * [no-template-curly-in-string](http://eslint.org/docs/rules/no-template-curly-in-string)
 * [no-var](http://eslint.org/docs/rules/no-var)
 * [prefer-const](http://eslint.org/docs/rules/prefer-const)
 * [prefer-promise-reject-errors](http://eslint.org/docs/rules/prefer-promise-reject-errors)
 * [prefer-rest-params](http://eslint.org/docs/rules/prefer-rest-params)
 * [prefer-spread](http://eslint.org/docs/rules/prefer-spread)
 * [prefer-template](http://eslint.org/docs/rules/prefer-template)
 * [require-await](http://eslint.org/docs/rules/require-await)

I'm using ES6/ES7 full time (for Node use anyways). I don't use var, I'm using await/async for all my asynchronous logic, and the spread operator is pretty useful. The `prefer-template` rule tripms me up a lot. For short strings, adding a couple strings together is a little easier for me to read than templates, but maybe I'm just used to the old ways of doing things.


Things that don't happen often, but there's no harm in protecting against them anyways.
 * [no-div-regex](http://eslint.org/docs/rules/no-div-regex)
 * [no-extra-label](http://eslint.org/docs/rules/no-extra-label)
 * [no-iterator](http://eslint.org/docs/rules/no-iterator)
 * [no-label-var](http://eslint.org/docs/rules/no-label-var)
 * [no-labels](http://eslint.org/docs/rules/no-labels): ["error", { "allowLoop": true }]
 * [no-proto](http://eslint.org/docs/rules/no-proto)
 * [no-self-compare](http://eslint.org/docs/rules/no-self-compare)
 * [no-unused-labels](http://eslint.org/docs/rules/no-unused-labels)
 * [no-with](http://eslint.org/docs/rules/no-with)

I don't use `with` or loop labels very often, but I figure I might as well protect myself. I don't see `__iterator__` or `__proto__` very often.

Good practices
 * [default-case](http://eslint.org/docs/rules/default-case)
 * [no-case-declarations](http://eslint.org/docs/rules/no-case-declarations)
 * [no-extend-native](http://eslint.org/docs/rules/no-extend-native)
 * [no-global-assign](http://eslint.org/docs/rules/no-global-assign)
 * [no-return-assign](http://eslint.org/docs/rules/no-return-assign)
 * [no-shadow-restricted-names](http://eslint.org/docs/rules/no-shadow-restricted-names)

I don't like altering native libraries or using reserved words as variables. A `default` case is usually a good idea and return-assign is hard to read.

Clean up
 * [no-alert](http://eslint.org/docs/rules/no-alert)
 * [no-console](http://eslint.org/docs/rules/no-console)
 * [no-empty-function](http://eslint.org/docs/rules/no-empty-function)
 * [no-floating-decimal](http://eslint.org/docs/rules/no-floating-decimal)
 * [no-magic-numbers](http://eslint.org/docs/rules/no-magic-numbers): ["error", { "ignore": [-1,0,1] }],
 * [no-unused-expressions](http://eslint.org/docs/rules/no-unused-expressions)
 * [no-useless-computed-key](http://eslint.org/docs/rules/no-useless-computed-key)
 * [no-useless-concat](http://eslint.org/docs/rules/no-useless-concat)

I like rules that catch unused code (which is sometimes an error). No-magic-numbers bites me a lot, but it's definitely nicer to have named constants. For browsers I allow error and warn for for no-console.

Misc ones I like
 * [array-callback-return](http://eslint.org/docs/rules/array-callback-return)
 * [no-throw-literal](http://eslint.org/docs/rules/no-throw-literal)
 * [no-unmodified-loop-condition](http://eslint.org/docs/rules/no-unmodified-loop-condition)
 * [no-unsafe-negation](http://eslint.org/docs/rules/no-unsafe-negation)
 * [strict](http://eslint.org/docs/rules/strict): ["error", "global"],
 * [yoda](http://eslint.org/docs/rules/yoda): ["error", "never", { "exceptRange": true }]
 * [no-unsafe-regex](https://www.npmjs.com/package/eslint-plugin-no-unsafe-regex)

I also like to remember to `"use strict";` and to throw only Error (or subclasses). The array-callback-return rule is a handy one. The last one, no-unsafe-regex, is an eslint plugin to prevent unsafe, exponential time regexes.

This can probably seem overwhelming if you're just starting, but the [default recommended rules](http://eslint.org/docs/rules/) are pretty good. You've probably already made your Javascript better by just choosing the default rules.  Also, [Airbnb has a well-liked set of eslint rules](https://www.npmjs.com/package/eslint-config-airbnb) as well.

Happy linting!

