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
 * Special rules for Node, ES6, and ES7
 * It's extensible if you want to build your own rules.
 * Excellent documentation
 * Well designed console output
 * Well maintained
 * Easy to ignore a rule with special comment
 * You can override the rules on a per folder basis with .eslint files. Handy if you have browser js and server js in the same repo.
 * Helpful [Gitter channel](https://gitter.im/eslint/eslint)


I know I'm a little behind the times here, but I'm fully on the ESLint train.

Here's my set of rules. Since I'm early in my project, I have a feeling some of these will change if I get too annoyed by them.  

I have them all set to error. In my experience, I don't like having a bunch of compiler/lint warnings, so I always make them errors.

My choices were based on: Will these catch likely errors and ensure good decisions by future me and potential future collaborators 

Let's go through some of them.


Why aren't these on by default.
array-callback-return
"no-self-compare": ["error"],


Holy wars.
curly //enforce consistent brace style for all control statements
"indent": ["error","tab"],
"linebreak-style": ["error","unix"],
"max-len": ["error", {"code": 156}],
"quotes": ["off","double"],

Node
callback-return
handle-callback-err
"no-path-concat": ["error"],
"no-process-env": ["error"],
"no-process-exit": ["error"],
 "no-restricted-modules": ["error", "assert", "nconf", "winston"],
 "no-sync": ["error"],


ES6
"no-caller": ["error"],
"no-var": ["error"],
"prefer-const": ["error"],
"prefer-rest-params": ["error"],
"prefer-spread": ["error"],
"prefer-template": ["error"],
"no-useless-concat": ["error"],
"no-template-curly-in-string": ["error"],
        

ES7.
"no-await-in-loop": ["error"],
"prefer-promise-reject-errors": ["error"],
"require-await": ["error"],

Other
default-case
"new-cap": ["error"],
"new-parens": ["error"],
"no-alert": ["error"],
"no-case-declarations": ["error"],
"no-confusing-arrow": ["error"],    
 "no-div-regex": ["error"],        
        
        
        
Classic
"eqeqeq": ["error", "always"],        
"no-eval": ["error"], 
"no-with": ["error"],
"radix": ["error", "always"],
"semi": ["error","always"],
"no-implied-eval": ["error"],
 "no-loop-func": ["error"],

Other ones I like
"no-throw-literal": ["error"],
"strict": ["error", "global"],
"no-empty-function": ["error"],	
"no-unmodified-loop-condition": ["error"],
"no-unused-expressions": ["error"],
"no-extra-label": ["error"],
        






        
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