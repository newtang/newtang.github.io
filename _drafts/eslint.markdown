---
layout: post
title:  "Eslint"
date:   2017-03-18 11:35:00 -0800
categories: javascript eslint
---

ESLint rules that ESLint rules. http://www.theonion.com/article/supreme-court-rules-supreme-court-rules-998

I recently embarked on a fresh new Node.JS project, and after having used JSHint for several previous projects, I wanted to read up on ESLint and give that a shot. I **strongly** recommend using a linter for every Javascript project; they catch a ton of bugs. Anyways, it didn't take long to realize that ESLint is wildly superior. 

 * There are way more rules (this is a good thing)
 * Easy to configure
 * Several gradations within many of the rules
 * Special rules for Node, ES6, ES7, and even React
 * It's extensible if you want to build your own rules.
 * Excellent documentation
 * Well designed console output
 * Well maintained
 * Easy to ignore a rule with special comment
 * You can override the rules on a per folder basis with .eslint files. Handy if you have browser and server Javascript in the same repo.
 * Helpful [Gitter channel](https://gitter.im/eslint/eslint)


I know I'm a little behind the times here, but I'm fully on the ESLint train.

Here's my set of rules. Since I'm early in my project, I have a feeling some of these will change if I get too annoyed by them.  

I have them all set to error. In my experience, people tend to ignore compiler/lint warnings, so I always make them errors.

My choices were based on: Will these catch likely errors and ensure good decisions by future me and potential future collaborators. Also is this a rule I would want enforced >90% of the time

Let's go through some of them.

Node
callback-return
handle-callback-err
"no-path-concat": ["error"],
"no-process-env": ["error"],
"no-process-exit": ["error"],
 "no-restricted-modules": ["error", "assert", "nconf", "winston"],
 "no-sync": ["error"],

 Callback returns have gotten me a few times. I do a callback in the middle of a method, but since I didn't return the rest of the function gets executed. I also love the restricted modules rule. Occasionally, I use wrappers around some libraries and this helps enforce the usage of the wrapper instead of the library.


Holy wars.
curly //enforce consistent brace style for all control statements
"indent": ["error","tab"],
"linebreak-style": ["error","unix"],
"max-len": ["error", {"code": 156}],
"quotes": ["off","double"],

I don't want to get into a space vs tab or single vs double quote war. I like these rules because they enforce the style.

Classic
"eqeqeq": ["error", "always"],        
"no-eval": ["error"], 
"no-with": ["error"],
"radix": ["error", "always"],
"semi": ["error","always"],
"no-implied-eval": ["error"],
 "no-loop-func": ["error"],

I remember these from JSLint and JSHint. I prefer semicolons and eqeqeq I think is still a good practice.

ES6 and ES7
"no-var": ["error"],
"prefer-const": ["error"],
"prefer-rest-params": ["error"],
"prefer-spread": ["error"],
"prefer-template": ["error"],
"no-useless-concat": ["error"],
"no-template-curly-in-string": ["error"],
"no-await-in-loop": ["error"],
"prefer-promise-reject-errors": ["error"],
"require-await": ["error"],

I'm pretty much fully on the ES6/ES7 train. I don't use var, I'm using await/async for all my asynchronous logic, and the spread operator is pretty useful. I think the `prefer-template` rule gets me a lot. For short strings, adding a couple strings together is a little easier for me to read than templates. But maybe I'm just used to it.


Misc ones I like
"no-throw-literal": ["error"],
"strict": ["error", "global"],
"no-empty-function": ["error"], 
"no-unmodified-loop-condition": ["error"],
"no-unused-expressions": ["error"],
"no-extra-label": ["error"],
array-callback-return
"no-self-compare": ["error"],
        





Other
default-case
"new-cap": ["error"],
"new-parens": ["error"],
"no-alert": ["error"],
"no-case-declarations": ["error"],
"no-confusing-arrow": ["error"],    
 "no-div-regex": ["error"],        

        
        









        
        "no-extend-native": ["error"],
        "no-floating-decimal": ["error"],
        "no-global-assign": ["error"],
       
        "no-iterator": ["error"],
        "no-labels": ["error", { "allowLoop": true }],
        "no-label-var": ["error"],
        "no-lone-blocks": ["error"],
       
        "no-return-assign": ["error"],
        "no-magic-numbers": ["error", { "ignore": [-1,0,1] }],
        "no-new-require": ["error"],
        

        "no-proto": ["error"],
       
        
        "no-sequences": ["error"],
        "no-shadow-restricted-names": ["error"],
        
        
        
        "no-undefined": ["error"],
        
        "no-unsafe-negation": ["error"],
        
        "no-unused-labels": ["error"],
        "no-useless-computed-key": ["error"],
        
        "yoda": ["error", "never", { "exceptRange": true }]