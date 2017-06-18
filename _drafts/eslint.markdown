---
layout: post
title:  "ESLint Rules that ESLint Rules"
date:   2017-03-18 11:35:00 -0800
categories: javascript eslint
---

[Title inspired by The Onion](http://www.theonion.com/article/supreme-court-rules-supreme-court-rules-998)

I recently embarked on a fresh new Node.JS project, and after having used JSHint for several previous projects, I wanted to read up on ESLint and give that a shot. Linters catch a ton of bugs by enforcing best practices, particularly in untyped languages like Javascript. I **strongly** recommend using a linter. Anyways, it didn't take long to realize that ESLint is wildly superior to it's predecessors.

 * There are way more rules (this is a good thing)
 * Easy to configure
 * Several gradations within many of the rules
 * Special rules for Node, ES6, ES7, browsers, and even React
 * It's extensible if you want to build your own rules.
 * Excellent documentation
 * Well designed console output
 * Well maintained
 * Easy to ignore a rule with special comment
 * You can override the rules on a per folder basis with .eslint files. Handy if you have browser and server Javascript in the same repo.
 * Helpful [Gitter channel](https://gitter.im/eslint/eslint)

I know I'm a little behind the times here, but I'm fully on the ESLint train. [Here's my set of rules](https://github.com/newtang/eslint). Since I'm early in my project, I have a feeling some of these will change if I get too annoyed by them. I have them all set to error. In my experience, people tend to ignore compiler/lint warnings, so I always make them errors. My choices were based on: Will these catch likely errors and ensure good decisions by future me and potential future collaborators. Also is this a rule I would want enforced >90% of the time

Let's go through some of them.

NodeJS specific
 * [callback-return](http://eslint.org/docs/rules/callback-return)
 * [handle-callback-err](http://eslint.org/docs/rules/handle-callback-err)
 * [no-path-concat](http://eslint.org/docs/rules/no-path-concat)
 * [no-process-env](http://eslint.org/docs/rules/no-process-env)
 * [no-process-exit](http://eslint.org/docs/rules/no-process-exit)
 * [no-restricted-modules](http://eslint.org/docs/rules/no-restricted-modules): ["error", "assert", "nconf", "winston"],
 * [no-sync](http://eslint.org/docs/rules/no-sync)

Callback returns have gotten me a few times. I do a callback in the middle of a method, but since I didn't return the rest of the function gets executed. I also love the restricted modules rule. Occasionally, I use wrappers around some libraries and this helps enforce the usage of the wrapper instead of the library. For sync libraries, you should have a good reason for using it (and there are some).


Holy wars.
 * [curly](http://eslint.org/docs/rules/curly)
 * [indent](http://eslint.org/docs/rules/indent): ["error","tab"],
 * [linebreak-style](http://eslint.org/docs/rules/linebreak-style): ["error","unix"],
 * [quotes](http://eslint.org/docs/rules/quotes): ["off","double"],

I don't want to get into a space vs tab or single vs double quote debate. I like these rules because they can enforce the style of your choosing, and you don't have to worry about all sorts of weird git commit diffs. I do happen to prefer enforcing curly braces for if-statements, and snugly braces, but that's just me.

Classic
 * [eqeqeq](http://eslint.org/docs/rules/eqeqeq): ["error", "always"],        
 * [no-eval](http://eslint.org/docs/rules/no-eval): 
 * [radix](http://eslint.org/docs/rules/radix): ["error", "always"],
 * [semi](http://eslint.org/docs/rules/semi): ["error","always"],
 * [no-implied-eval](http://eslint.org/docs/rules/no-implied-eval): ["error"],
 * [no-loop-func](http://eslint.org/docs/rules/no-loop-func): ["error"],

I remember these from JSLint and JSHint. I prefer semicolons and I think eqeqeq is still a good practice. I like the radix rules with parseInt because there are some instances where it tries to be smart (like having "0x" in front of a number), and I almost always want a radix of ten.

ES6 and ES7
 * [no-var](http://eslint.org/docs/rules/no-var): ["error"],
 * [prefer-const](http://eslint.org/docs/rules/prefer-const): ["error"],
 * [prefer-rest-params](http://eslint.org/docs/rules/prefer-rest-params): ["error"],
 * [prefer-spread](http://eslint.org/docs/rules/prefer-spread): ["error"],
 * [prefer-template](http://eslint.org/docs/rules/prefer-template): ["error"],
 * [no-template-curly-in-string](http://eslint.org/docs/rules/no-template-curly-in-string): ["error"],
 * [no-await-in-loop](http://eslint.org/docs/rules/no-await-in-loop): ["error"],
 * [prefer-promise-reject-errors](http://eslint.org/docs/rules/prefer-promise-reject-errors): ["error"],
 * [require-await](http://eslint.org/docs/rules/require-await): ["error"],

I'm pretty much fully on the ES6/ES7 train (for Node use anyways). I don't use var, I'm using await/async for all my asynchronous logic, and the spread operator is pretty useful. I think the `prefer-template` rule gets me a lot. For short strings, adding a couple strings together is a little easier for me to read than templates. But maybe I'm just used to it.


Misc ones I like
 * [no-throw-literal](http://eslint.org/docs/rules/no-throw-literal)
 * [strict](http://eslint.org/docs/rules/strict): ["error", "global"],
 * [no-empty-function](http://eslint.org/docs/rules/no-empty-function)
 * [no-unmodified-loop-condition](http://eslint.org/docs/rules/no-unmodified-loop-condition): ["error"],
 * [no-unused-expressions](http://eslint.org/docs/rules/no-unused-expressions)
 * [array-callback-return](http://eslint.org/docs/rules/array-callback-return)
 * [max-len](http://eslint.org/docs/rules/max-len): ["error", {"code": 156}],
        
I like rules that catch unused code (which is sometimes an error). I also like to remember to `"use strict";` and to throw only Error (or subclasses). `No-self-compare` hasn't come in useful but it makes sense to activate


Things that don't happen often, but there's no harm in protecting against them anyways.
 * [no-extra-label](http://eslint.org/docs/rules/no-extra-label)
 * [no-self-compare](http://eslint.org/docs/rules/no-self-compare)
 * [no-with](http://eslint.org/docs/rules/no-with)
 * [no-unused-labels](http://eslint.org/docs/rules/no-unused-labels): ["error"],
 * [no-labels](http://eslint.org/docs/rules/no-labels): ["error", { "allowLoop": true }],
 * [no-useless-concat](http://eslint.org/docs/rules/no-useless-concat): ["error"],


Other
[default-case](http://eslint.org/docs/rules/default-case)
[new-cap](http://eslint.org/docs/rules/new-cap)
[new-parens](http://eslint.org/docs/rules/new-parens)
[no-alert](http://eslint.org/docs/rules/no-alert)
[no-case-declarations](http://eslint.org/docs/rules/no-case-declarations)
[no-confusing-arrow](http://eslint.org/docs/rules/no-confusing-arrow)
[no-div-regex](http://eslint.org/docs/rules/no-div-regex)









[no-extend-native](http://eslint.org/docs/rules/no-extend-native): ["error"],
[no-floating-decimal](http://eslint.org/docs/rules/no-floating-decimal): ["error"],
[no-global-assign](http://eslint.org/docs/rules/no-global-assign): ["error"],

[no-iterator](http://eslint.org/docs/rules/no-iterator): ["error"],

[no-label-var](http://eslint.org/docs/rules/no-label-var): ["error"],
[no-lone-blocks](http://eslint.org/docs/rules/no-lone-blocks): ["error"],

[no-return-assign](http://eslint.org/docs/rules/no-return-assign): ["error"],
[no-magic-numbers](http://eslint.org/docs/rules/no-magic-numbers): ["error", { "ignore": [-1,0,1] }],
[no-new-require](http://eslint.org/docs/rules/no-new-require): ["error"],
[no-proto](http://eslint.org/docs/rules/no-proto): ["error"],


[no-sequences](http://eslint.org/docs/rules/no-sequences): ["error"],
[no-shadow-restricted-names](http://eslint.org/docs/rules/no-shadow-restricted-names): ["error"],

[no-undefined](http://eslint.org/docs/rules/no-undefined): ["error"],

[no-unsafe-negation](http://eslint.org/docs/rules/no-unsafe-negation): ["error"],


[no-useless-computed-key](http://eslint.org/docs/rules/no-useless-computed-key): ["error"],

[yoda](http://eslint.org/docs/rules/yoda): ["error", "never", { "exceptRange": true }]