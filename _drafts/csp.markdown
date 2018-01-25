---
layout: post
title:  "The problem with Content Security Policy"
date:   2018-02-24 22:40:00 -0800
categories: programming csp
---

Hang on, before you tear me to shreds, let me get this out of the way: I like Content Security Polcies (CSP). It's a damned good idea, easy to implement, and well documented. A CSP is a header that white lists which urls can make requests. You can specify where CSS comes from, fonts, Javascript, media, etc. The whole point is to protect against XSS attacks. If you have a vulnerability on your site that displays dynamic content, and a malicious user managed to inject custom javascript, the CSP can prevent it from making requests to urls you haven't whitelisted.  Version 1 is [well-supported](https://caniuse.com/#feat=contentsecuritypolicy), [version 2 is pretty good](https://caniuse.com/#feat=contentsecuritypolicy2) as of this writing. That's the rough gist of it. I recommend reading more [on MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP).

Anyways, it's a great idea, but I keep running into problems. Here's a few.

Third party libraries.
There's little to no documentation about what urls to permit for third party libraries. Google Analytics, Mixpanel, Segment.  Sure you can do report-only for awhile and fix them, but it's annoying. And some you'll miss; if it has a list of backup servers if one is down, or uses different servers for different IPs, etc.

Crazy crap
blocked-uri: eval document-uri: about violated-directive: script-src
blocked-uri: data document-uri: https://geteventbot.com/ violated-directive: font-src 
blocked-uri: self document-uri: https://geteventbot.com/ violated-directive: script-src 
blocked-uri: data document-uri: about:blank violated-directive: font-src 
blocked-uri: https://translate.googleapis.com document-uri: https://geteventbot.com/slack/oauth
blocked-uri: blob document-uri: https://geteventbot.com/slack/oauth/auth_success?

allowed: "data:",

About, blob, segment, specific data types


maybe edge cases aren't well documented?
hard to isolate if there's a bad behavior in a script (use of eval, data)

no subdomain wildcard

wss://us22.zopim.com
wss://us26.zopim.com 
wss://us34.zopim.com
https://v2.zopim.com