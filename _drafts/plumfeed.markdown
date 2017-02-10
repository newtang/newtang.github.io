---
layout: post
title:  "I created a simple RSS feed aggregator named Plumfeed"
date:   2017-01-17 11:35:00 -0800
categories: programming plumfeed
---

I never had a chance to write in depth about Plumfeed, so I'm going to do that now!

Over the last dozen years or so, I've had a few methods for consuming content from blogs and sites I liked. My original method (and probably my most clever) was to save websites in [Delicious](https://delicious.com) and tag them with "dailyblog". Then, I added the RSS feed for that Delicious tag to a Firefox Live Bookmark. If I tagged a new site, it would automatically be in the Live Bookmark. Anyone born in the 1990s probably has no idea what I'm talking about. Anyways, everyday I would just randomly open several of the sites in the Live Bookmark to see if they had updated.

At some point I switched to Chrome which didn't have the Live Bookmark feature. I found myself reading more links from [Twitter](https://twitter.com) and [Hacker News](https://news.ycombinator.com) and missing out on sites and comics I read regularly. Additionally, I know I'm in the minority here, but I haven't liked most feed readers, Google's included. I didn't like treating these sites as an inbox I had to maintain, and I strongly prefer to read the content on the original website instead of sucked out into a feed reader. I feel like there are details, messages, easter eggs that get missed when you avoid the site. It's like I was missing the ambiance of the website if that's a thing.

As a proof of concept, I wrote a PHP script that iterated the dailyblog tagged rss feed and displayed a link to the most recent post in all of them.It was very slow to load, and cumbersome to update, but I still used it a lot. 

I was toying with rebuilding this so it was faster and easier to update. I was considering the possibility of allowing others to sign up and use it too, but it would add a lot of complexity. Around the same time I saw a talk by Phil Libin who suggested that if you wanted to build something to scratch an itch, that there are probably others who are as weird as you who have the same itch. I decided to go for it, and build something anyone could use. Besides, I needed a new personal project to work on, so in 2014 I got started on what became [Plumfeed](https://plumfeed.com).

I built Plumfeed to be exactly what I want in an RSS feed aggregator. Once a day it fetches the most recent link from every RSS feed that it's given. When a user visits, these links are displayed in chronological order. The feed is also public. That's it. I'm the most prolific user of course, so check out [my plumfeed stream](https://plumfeed.com/jon). Plumfeed is geared towards sites that update once a day or less so it's perfect for most blogs and comics. It's not really built for consuming [TechCrunch](https://techcrunch.com) or a large news site. You could add it if you wanted, but you would miss a lot of content.

Tech Specs! Plumfeed is built on [Heroku's](https://heroku.com) Hobby Dyno. It costs me $7 a month. I'm sure I could move it AWS and save a few bucks, but Heroku is so handy, and it was fun learning about it. It's Express running on Node.js. I'm using Postgres for my database. I don't have a great reason for choosing Postgres other than people seem to really dig it, and I wanted to learn about it. 

I wrote most of the authentication layer, but I'm using [bcrypt](https://www.npmjs.com/package/bcrypt) for password hashing. Authentication, sessions, cookies, etc were things I knew about, but you really start to appreciate the nitty-gritty details once you build it. 

To render the front-end I'm using [Dust.js](https://dustjs.com). I don't really remember why I chose that engine. Around that time there was a lot of excitement around logicless templates. Although I appreciate the rationale, it's a little too draconian for my tastes. I feel like there's a little room for occasionally having some layout related logic. But I digress.

On the front-end I decided to use just plain [vanilla js](http://vanilla-js.com/). I didn't think my site would have much Javascript, so I didn't want the overhead of building a library. It ended up having more than I thought, but it's been easy to maintain. I also made the site responsive for mobile viewing using media queries and CSS.

Finally, I have a worker job on Heroku that goes through all the feeds that have been collected, and updates them once a day.

I used to have [Cloudflare](https://cloudflare.com) in front of my site for SSL termination, but [I recently switched to just having Heroku handle it](/posts/heroku-ssl-and-a-free-cert-from-lets-encrypt).

I've had a bunch of people sign up, but most just leave after visiting once. I have a few regular users who are very nice friends of mine. I haven't really advertised or even pushed it on my [Plumfeed Twitter feed](https://twitter.com/plumfeed) much.

Heroku $7 tier
link to https
node, express, gulp
authentication, session, cookies, bcrypt
postgres
dust templates
vanilla js
responsive css
worker job
