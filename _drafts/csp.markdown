---
layout: post
title:  "The problem with Content Security Policy"
date:   2018-02-24 22:40:00 -0800
categories: programming csp
---

Hang on, before you tear me to shreds, let me get this out of the way: I like Content Security Polcies (CSP). It's a damned good idea, easy to implement, and well documented. A CSP is a header (or a meta tag as I recently learned) that white lists which urls the webpage can make requests to. You can specify where CSS comes from, fonts, Javascript, media, etc. The whole point is to protect against XSS attacks or other [unwanted](https://news.ycombinator.com/item?id=15890551) [injected](https://scotthelme.co.uk/protect-site-from-cryptojacking-csp-sri/) scripts. If you have a vulnerability on your site that displays dynamic content, and a malicious user managed to inject custom javascript, the CSP can prevent it from making requests to urls you haven't whitelisted. You can have browsers report errors back to your server, which is neato. Version 1 is [well-supported](https://caniuse.com/#feat=contentsecuritypolicy), [version 2 is pretty good](https://caniuse.com/#feat=contentsecuritypolicy2) as of this writing. That's the rough gist of it. I recommend reading more [on MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP).

Anyways, it's a great idea, but when it comes to 3rd party libraries, it's a huge pain in the ass for a number reasons:

Minimal documentation for what to white-list.
There's rarely any documentation about what urls and permissions to grant for third party libraries like Google Analytics, Mixpanel, or Segment. Sure, you can do report-only for awhile and fix them, but it's annoying and it very well may be incomplete. Some urls may only be triggered in certain conditions, like if it has a list of backup servers if one is down for instance.


Poor programming choices
I got this CSP error from embedding a Zendesk script:

`blocked-uri: eval document-uri: https://geteventbot.com/ violated-directive: script-src`
`blocked-uri: 'self' document-uri: https://geteventbot.com/ violated-directive: script-src`
'script-sample': 'call to eval() or related function blocked by CSP',
'source-file': 'https://assets.zendesk.com/embeddable_framework/main.js',

Zendesk uses a script that has an eval. Hilariously, if you dig into the minified script, you'll find `return eval("false")`. I have nothing but poor options: Block the eval, Allow eval globally, or use some other help desk service.


Data and Blob
Here's a report from a script using a data url:
`blocked-uri: data, document-uri: https://geteventbot.com/, violated-directive: font-src, source-file: https://v2.zopim.com `
Unfortunately, I'd either have to allow "data" for all my font-src. Interestingly, I cannot whitelist a particular data url eg `data:abcd1234` It's either all or none. The same problem goes for "blob" urls as well.

Segment
[Segment](https://segment.com/) is an interesting service that allows you to embed one script, and turn on and off various services (like Zendesk, Google Analytics, etc). It's handy for all sorts of reasons, but it's a nightmare if you have a CSP. Someone can just flip on Mixpanel, and it's on your site, but if you never whitelisted whatever urls Mixpanel uses, well, you're out of luck. And of course, as I mentioned above, it's not going to be documented very well


No subdomain wildcards 
Here's some very similar looking reports:
`
blocked-uri: wss://us34.zopim.com document-uri: https://geteventbot.com/ violated-directive: connect-src
blocked-uri: wss://us22.zopim.com, document-uri: https://geteventbot.com/, violated-directive: connect-src, source-file: https://v2.zopim.com 
blocked-uri: wss://us24.zopim.com, document-uri: https://geteventbot.com/, violated-directive: connect-src, source-file: https://v2.zopim.com/bin/v/widget_v2.233.js `

I have no idea what zopim's rationale for having multiple different subdomains, but unfortunately, CSP do not allow you any sort subdomain wildcard. Bummer.


Alerts from extensions
blocked-uri: data document-uri: about:blank violated-directive: font-src 
blocked-uri: https://translate.googleapis.com document-uri: https://geteventbot.com/

The first report above is [from an extension](https://stackoverflow.com/questions/32336860/why-would-i-get-a-csp-violation-for-the-blocked-uri-about) and the second is because someone used Chrome's handy translation feature! It's not a huge deal, but it raises some confusing questions. Am I be breaking someone's extensions. Should I? I wouldn't think I would want to. Should browsers even send these kind of CSP reports?


Anyways, I hope this doesn't dissuade you from using a CSP. You should. And if you're writing a library you intend others to consume as a 3rd party, I have some suggestions:
- Publish a strict example CSP that would allow your script to work, and doesn't include any "unsafe" items
- Keep this example CSP small. If you have 50 subdomains, please rethink your strategy
- Avoid things that have no nuance, like data, eval



