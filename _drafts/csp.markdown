---
layout: post
title:  "The problem with Content Security Policy"
date:   2018-02-24 22:40:00 -0800
categories: programming csp
---

Hang on, before you tear me to shreds, let me get this out of the way: I like Content Security Polcies (CSP). It's a damned good idea, easy to implement, and well documented. A CSP is a header (or a meta tag as I recently learned) that white lists which urls the webpage can make requests to. You can specify where CSS comes from, fonts, Javascript, media, etc. The whole point is to protect against XSS attacks. If you have a vulnerability on your site that displays dynamic content, and a malicious user managed to inject custom javascript, the CSP can prevent it from making requests to urls you haven't whitelisted.  Version 1 is [well-supported](https://caniuse.com/#feat=contentsecuritypolicy), [version 2 is pretty good](https://caniuse.com/#feat=contentsecuritypolicy2) as of this writing. That's the rough gist of it. I recommend reading more [on MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP).

- you can have it report errors too which is neato

Anyways, it's a great idea, but I keep running into problems. Here's a few.
Anyways, it's a great idea, but when it comes to 3rd party libraries, it's a huge pain in the ass for a number reasons.

Most of the problems stem from 3rd party scripts. 

Poor choices
`blocked-uri: eval document-uri: https://geteventbot.com/ violated-directive: script-src`
`blocked-uri: 'self' document-uri: https://geteventbot.com/ violated-directive: script-src`
  'script-sample': 'call to eval() or related function blocked by CSP',
  'source-file': 'https://assets.zendesk.com/embeddable_framework/main.js',
Zendesk uses a script that used an eval.  Hilariously, if you dig into the minified script, you'll find `return eval("false")`. I have nothing but poor options: Block the eval, Allow eval globally, or use some other help desk service.


Third party libraries.
There's little to no documentation about what urls to permit for third party libraries. Google Analytics, Mixpanel, Segment.  Sure you can do report-only for awhile and fix them, but it's annoying. And some you'll miss; if it has a list of backup servers if one is down, or uses different servers for different IPs, etc.

	


Little nuance
blocked-uri: data document-uri: https://geteventbot.com/ violated-directive: font-src 
blocked-uri: blob document-uri: https://geteventbot.com/slack/oauth/auth_success?


Useless alerts
blocked-uri: data document-uri: about:blank violated-directive: font-src 
  - https://stackoverflow.com/questions/32336860/why-would-i-get-a-csp-violation-for-the-blocked-uri-about
blocked-uri: https://translate.googleapis.com document-uri: https://geteventbot.com/



allowed: "data:",

About, blob, segment, specific data types


maybe edge cases aren't well documented?
hard to isolate if there's a bad behavior in a script (use of eval, data, blob)

no subdomain wildcard
wss://us22.zopim.com
wss://us26.zopim.com 
wss://us34.zopim.com
https://v2.zopim.com


Advice to writers of 3rd party libraries.
- Publish a strict example CSP that doesn't include any "unsafe" items
- Keep this example CSP small. If you're have 50 subdomains, please rethink your strategy
- Avoid things that have no nuance, like data, eval (DOUBLE CHECK these)



