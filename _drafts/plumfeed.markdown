---
layout: post
title:  "I created a simple RSS feed aggregator named Plumfeed"
date:   2017-01-17 11:35:00 -0800
categories: programming plumfeed
---

I never had a chance to write in depth about [Plumfeed](https://plumfeed.com), so I'm going to do that now!

Over the last dozen years or so, I've had a few methods for consuming content from blogs and sites I liked. My original method (and probably my most clever) was to save websites in [del.icio.us](https://del.icio.us) and tag them with "dailyblog". Then, I added the RSS feed for that Delicious tag to a Firefox Live Bookmark. If I tagged a new site, it would automatically be in the Live Bookmark. Anyone born in the 1990s probably has no idea what I'm talking about. Anyways, everyday I would just randomly open several of the sites in the Live Bookmark to see if they had updated.

At some point I switched to Chrome which didn't have the Live Bookmark feature. I found myself reading more links from [Twitter](https://twitter.com) and [Hacker News](https://news.ycombinator.com) and missing out on sites and comics I read regularly. Additionally, I know I'm in the minority here, but I haven't liked most feed readers, Google's included. I didn't like treating these sites as an inbox I had to maintain, and I strongly prefer to read the content on the original website instead of sucked out into a feed reader. I feel like there are details, messages, easter eggs that get missed when you avoid the site. It's like I was missing the ambiance of the website if that's a thing.

As a proof of concept, I wrote a PHP script that iterated my old delicious dailyblog tagged RSS feeds and displayed a link to the most recent post in all of them. It was very slow to load, and cumbersome to update, but I still used it a lot. 

I was toying with rebuilding this so it was faster and easier to update. I was considering the possibility of allowing others to sign up and use it too, but it would add a lot of complexity. Around the same time I saw a talk by [Phil Libin](https://en.wikipedia.org/wiki/Phil_Libin) who suggested that if you wanted to build something to scratch an itch, that there are probably others who are as weird as you who have the same itch. I decided to go for it, and build something anyone could use. Besides, I needed a new personal project to work on, so in 2014 I got started on what became [Plumfeed](https://plumfeed.com).

I built Plumfeed to be exactly what I want in an RSS feed aggregator. Once a day it fetches the most recent link from every RSS feed in the database. When a user visits, their links are displayed in chronological order. The feed is also public. That's it! I'm the most prolific user of course, so check out [my plumfeed stream](https://plumfeed.com/jon). Plumfeed is geared towards sites that update once a day or less so it's perfect for most blogs and comics. It's not really built for consuming [TechCrunch](https://techcrunch.com) or a large news site. You could add it if you wanted, but you would miss a lot of content.

I wish the name, Plumfeed, had a good story, but truthfully, I really struggled to come up with a name that I could easily acquire a domain for. I wanted to stay away from the wacky novelty TLDs because, well, I hate them. One day I was eating a plum which was particularly tasty, and out of name exhaustion I decided on Plumfeed. It occurred to me that "plum" can also mean prime or desired (like a plum job). If I was choosing the best feeds, maybe this would make more sense, but... eh, it's fine.

Tech Spec time! Plumfeed is built on [Heroku's](https://heroku.com) Hobby Dyno which costs $7 a month. I'm sure I could move it AWS and save a few bucks, but Heroku is so handy, and it was fun learning about it. It's [Express](https://expressjs.com/) running on [Node.js](https://nodejs.org/en/). I'm using [Postgres](https://www.postgresql.org/) for my database. I don't have great reasons for choosing Postgres. I know SQL pretty well, people seem to really dig it, and I wanted to learn about it.  

I wrote most of the authentication layer, but I'm using [bcrypt](https://www.npmjs.com/package/bcrypt) for password hashing. Authentication, passwords, hashing, salting, [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)_Prevention_Cheat_Sheet), sessions, cookies, etc were things I knew about, but you really start to appreciate the nitty-gritty details once you build it. 

To render the front-end I'm using [Dust.js templates](https://dustjs.com). Honestly, I don't remember why I chose that engine. Around that time there was a lot of excitement around logicless templates. Although I appreciate the rationale, it's a little too draconian for my tastes. I feel like there's a little room for occasionally having some layout related logic, and Dust allows this. But I digress. I decided to use just plain [vanilla javascript](http://vanilla-js.com/). I didn't think my site would have much javascript, so I didn't want the overhead of using a large library. It ended up having more than I thought, but it's been easy to maintain. I also made the site responsive for mobile viewing using media queries and CSS.

People who have known me awhile, know I used to do a lot of design work in college, and I was actually ok at it. Those skills have long since atrophied, but I think I still have an appreciation for what I like and don't like in designs. For Plumfeed, I designed the page layouts for web and mobile and the logo myself. I don't love the logo, but it gets the job done. I really wanted the site to be just about the feeds, so it has pretty basic layout. Getting the edit functionality looking right was tricky, and I'm not totally sure I succeeded. I wanted it to be out of the way. I figured users would be reading more than they would be adding so that's why it's a little tucked away.

Finally, I have a worker job on Heroku that goes through all the feeds that have been collected, and updates them once a day. If you see that your site was visited by PlumBot, that's me requesting your RSS feed.

I used to have [Cloudflare](https://cloudflare.com) in front of my site for SSL termination, but [I recently switched to just having Heroku handle it](/posts/heroku-ssl-and-a-free-cert-from-lets-encrypt).

I've had a bunch of people sign up, but most just leave after visiting once or twice. I have a few regular users who are very nice friends of mine. I haven't really advertised or even pushed it on my [Plumfeed Twitter feed](https://twitter.com/plumfeed) much. Although I still had feeds from time to time, I don't update the site much except for bug fixing and some package upgrades. 

Anyways, I'd love for you to [give Plumfeed a try!](https://plumfeed.com). Let me know what you thoughts positive and negative! Oh, and definitely contact me if you find a security issue.


