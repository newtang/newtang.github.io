---
layout: post
title:  "Basic Machine Learning tutorials in Javascript"
date:   2017-02-17 13:30:00 -0800
categories: machine-learning programming
---

I had an entry in my Evernote for years linking to a genetic algorithm tutorial. I finally decided to look into it, and I discovered it was part a [Machine Learning in Javascript](http://burakkanber.com/blog/machine-learning-in-other-languages-introduction/) series by [Burak Kanber](https://twitter.com/bkanber). After skimming through, I thought it looked worthwhile so I went through each one, and I copied out the data and the display logic. For each article I took out bits of code and tried to rebuild the idea from the explanation. I usually went back and forth a few times correcting my errors.

I'm not a great machine learning guy, but [I'm pretty happy with how it all turned out](https://github.com/newtang/jsML). Kanber's explanations are approachable and doesn't get too bogged down in theory. These are all pretty introductory, but still useful. And, it's nice seeing some Machine learning logic in a language I'm already comfortable with. Admittedly, I'm not sure all the articles strictly fall under the umbrella of "Machine Learning", like [Full-text search](http://burakkanber.com/blog/machine-learning-full-text-search-in-javascript-relevance-scoring/), but it's still relevant.

He covers:

 - [k-nearest neighbor](http://burakkanber.com/blog/machine-learning-in-js-k-nearest-neighbor-part-1/): Which pre-existing category does a new data point belong to
 - [k-means clustering](http://burakkanber.com/blog/machine-learning-k-means-clustering-in-javascript-part-1/): Categorizes data into one of 'k' clusters
 - [genetic algorithms](http://burakkanber.com/blog/machine-learning-genetic-algorithms-part-1-javascript/): "Mates" and mutates data to find local optimum
 - [naive bayes](http://burakkanber.com/blog/machine-learning-naive-bayes-1/): Handy for document classification (spam vs ham, or language it's written )
 - [full text search](http://burakkanber.com/blog/machine-learning-full-text-search-in-javascript-relevance-scoring/): How to rank documents when searching for keywords across them

There are a few Javascript patterns in these articles that bug me. Frequently, the for-in loop is misused to iterate through an array. For-in should be reserved iterating over properties in an object. If an array happens to have a property attached to it, it would be iterated upon with for-in. I would suggest using a traditional for loop, [Array.forEach](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach), or even the new [for-of loop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of). The author also makes some strange object-oriented choices. Display logic is frequently a part of certain business logic classes, or an instance has a method (like mateWith) that doesn't affect it's internal state and should probably exist elsewhere. I tended to move methods like this to a utility function outside the prototype. Finally, Kanber threw in localStorage in the naive-bayes tutorial which was unnecessary and only muddied the logic. Keeping data in just a local object would've been fine for example purposes which is what I ended up doing.

These gripes are relatively minor. Kanber explains the concepts well, and I'm grateful he published these articles and open sourced all of his code. It's been a couple years since the last article, but hopefully he'll deliver more.