---
layout: post
title:  "Problems with Content Security Policy"
date:   2018-02-17 22:40:00 -0800
categories: programming csp
---

As I've been working on the website for [Eventbot](https://geteventbot.com), I've been playing around with the site's [Content Security Policy (CSP)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP). Now, before you tear me to shreds for the incendary title, let me get this out of the way: **I like Content Security Policies**. It's a damned good idea, easy to implement, and well documented. If you already know all about CSPs you can skip the next paragraph. 

A CSP is a header (or a [meta tag](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy#Examples) as I recently learned) that whitelists which resources a webpage is allowed to load. Developers can whitelist urls that specify where CSS comes from, fonts, Javascript, media, etc. The whole point is to protect against XSS attacks or other [unwanted](https://news.ycombinator.com/item?id=15890551), [injected](https://scotthelme.co.uk/protect-site-from-cryptojacking-csp-sri/) scripts. [Browsers can also report errors back to the server](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP#Enabling_reporting), which is neato. As of this writing, version 1 is [well-supported](https://caniuse.com/#feat=contentsecuritypolicy), and [version 2 is pretty good](https://caniuse.com/#feat=contentsecuritypolicy2). There's a little more to it, but that's the rough gist of it.

Anyways, it's a great idea, but when it comes to working with 3rd party libraries, it's a huge pain in the ass for a number reasons:

### Minimal documentation for what to whitelist.
There's rarely any documentation about what urls and permissions to grant for third party libraries like [Google Analytics](https://analytics.google.com), [Mixpanel](https://mixpanel.com), or [Segment](https://segment.com). Sure, [report-only](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only) allows devs to fix errors as they appear, but it's annoying and it very well may be incomplete. Some urls may only be triggered in rare conditions, or, worse, the script could change without warning.	

### Strange programming choices
I got this CSP error from embedding a [Zendesk](https://zendesk.com) script:
```
violated-directive: script-src 
script-sample: 'call to eval() or related function blocked by CSP', 
source-file: 'https://assets.zendesk.com/embeddable_framework/main.js',
```

Zendesk uses a script that has an eval. Hilariously, in the minified script, you'll find `return eval("false")`. Maybe this is a weird result from minification? Anyways, to deal with this, I have nothing but blah options: Block the eval, Allow eval globally, or use some other help desk service.

### Use of Data and Blob
Here's a report from a script using a data url:
```
blocked-uri: data, 
violated-directive: font-src, 
source-file: https://v2.zopim.com
```

Interestingly, I cannot whitelist a particular [data url](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/Data_URIs) e.g. `data:abcd1234`. It's either all or nothing. The same problem goes for ["blob" urls](https://developer.mozilla.org/en-US/docs/Web/API/URL/createObjectURL) as well. I think this could be an interesting place where CSP could improve.

### Segment
[Segment](https://segment.com/) is an interesting service that allows a user to embed one script, and turn on or off various services (like Zendesk, Google Analytics, etc). It's handy for all sorts of reasons, but it's a nightmare if the webpage has a CSP. Someone can just flip on Mixpanel, but if its urls are never added to the CSP, well, it's going to be blocked. I don't think there's much Segment can do here, but improved documentation, as I mentioned above, would be a good start.

### No subdomain wildcards 
Here's some very similar looking reports. Note the subdomains:
```
blocked-uri: wss://us34.zopim.com, violated-directive: connect-src...
blocked-uri: wss://us22.zopim.com, violated-directive: connect-src...
blocked-uri: wss://us24.zopim.com, violated-directive: connect-src...
```

I have no idea what [Zopim's](https://www.zopim.com/) rationale for having multiple different subdomains, but unfortunately, CSPs do not allow subdomain wildcards. It's not a big deal if there's a few, but in this case there's *a ton*. Bummer. Anyways, this is another area I think CSP could potentially improve.

### Alerts from extensions
```
blocked-uri: data document-uri: about:blank violated-directive: font-src 
blocked-uri: https://translate.googleapis.com document-uri: https://geteventbot.com/
```

The first report above is [from an extension](https://stackoverflow.com/questions/32336860/why-would-i-get-a-csp-violation-for-the-blocked-uri-about). The second is because someone used Chrome's handy translation feature! It's not a huge deal, but it raises some confusing questions. Am I breaking someone's extensions. Should I? I wouldn't think I would want to. Should browsers even send these kind of CSP reports?


Anyways, I hope this doesn't dissuade you from using a CSP. You should, and you should test your headers on [securityheaders.io](https://securityheaders.io/).

Finally, if you're writing a library you intend others to consume as a 3rd party, I have some suggestions I hope you keep in mind:
- Publish an example CSP that would allow your script to work that doesn't include any "unsafe" items
- Keep this example CSP compact. If you have 50 subdomains, please rethink your strategy
- Avoid all-or-nothing properties, like data, eval, and blob if possible


Anything else that should be on this list? Did I get something wrong? Let me know!


