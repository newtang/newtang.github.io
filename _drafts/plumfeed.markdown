---
layout: post
title:  "I created a simple RSS feed aggregator named Plumfeed"
date:   2017-01-17 11:35:00 -0800
categories: programming plumfeed
---


Over the last dozen years or so, I've had a few methods for consuming content from blogs and sites I liked. My original method (and probably my most clever) was to save websites in [Delicious](https://delicious.com) and tag them with "dailyblog". Then, I added the RSS feed for that tag to a Firefox Live Bookmark. Anyone born in the 90s probably has no idea what I'm talking about. Anyways, I would just randomly open several of the sites in the Live Bookmark to see if they had updated.

At some point I switched to Chrome which didn't have the Live Bookmark feature. I found myself reading more links from [Twitter](https://twitter.com) and [Hacker News](https://news.ycombinator.com) and missing out on sites and comics I read regularly. Additionally, I know I'm in the minority here, but I didn't like most feed readers, Google's included. I didn't like treating these sites as an inbox I had to maintain. Also, I strongly prefer to read the content on the website instead of sucked out into a feed reader. I feel like there are details, messages, easter eggs that get missed when you avoid the site. I felt like I was missing the ambiance of the website if that's a thing.

As a proof of concept, I wrote a PHP script that iterated the dailyblog tagged rss feed and displayed a link to the most recent post in all of them.It was very slow to load, and cumbersome to update, but I still used it a lot. 

I was toying with rebuilding this so it was faster and easier to update. I was considering the possibility of allowing others to sign up and use it, but it would add a lot of complication. Around the same time I saw a talk by Phil Libin who suggested that if you wanted to build something to scratch and itch, that there are probably other people who have the same itch. I also wanted a new personal project to work on, so in 2014 I got started on what became [Plumfeed](https://plumfeed.com)

