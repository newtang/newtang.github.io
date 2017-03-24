---
layout: post
title:  "UUID creation in Postgres vs Node"
date:   2017-03-18 11:35:00 -0800
categories: javascript uuid postgres
---

I was making a new database table and I decided to do UUID primary keys instead of autoincremented integers as I had done in the past. There's some interesting articles and debates around why UUIDs are a good idea in spite of their increased size. 

When I wrote the query for insertion, I defaulted to letting Postgres do the work for UUID creation. 

`INSERT INTO users (id, username) VALUES (uuid_generate_v4(), 'newtang');`

I didn't have a good reason for doing this other than I tended to let the database handle the next autoincremented integer. But, the more I thought about it, the more it made sense to generate the UUID in Node (this was a Node.JS, Postgres stack). The first advantages is that I wouldn't have to wait for the Postgres `INSERT` query to complete to use the generated UUID elsewhere. If I needed this UUID in multiple places, I could fire off several queries at once, or it would be easier to build out a transaction with multiple insertions.

Certainly a plus for Node. As a general rule I don't like to add a lot of extra work to the database if I don't have to. But what about speed? How long would it take Node and Postgres to each generate 1,000,000 UUIDs? I'm not a great Postgres guy, but with the help from Stack Overlfow, I cobbled together this method.

{% highlight postgres %}
CREATE FUNCTION uuid_loop() RETURNS void
    LANGUAGE plpgsql
    AS $$
BEGIN
  FOR Loopid  IN 0..1000000 LOOP
     PERFORM uuid_generate_v4();
  END LOOP;
RETURN;
END;
$$;
{% endhighlight}

`PERFORM` just means run this function, but don't do anything with the result. Oh, and I had to activate `\timing` to show the duration. So I ran this a few times, and my results:
43286.626 ms
57559.374 ms
54655.993 ms

45 seconds to a minute. This struck me as slow, but let's see how Node does.

{% highlight postgres %}
const uuidV4 = require('uuid/v4');
console.time("uuid");
for(let i=0; i<1000000; ++i){
	uuidV4();
}
console.timeEnd("uuid");
{% endhighlight}

I'm using the uuid npm library. The `console.time` and `console.timeEnd` are builtin, handy ways to measure duration in Javascript without having to build `Date` objects and comparing.  Anywho, the results:

4792.588ms
4809.111ms
4709.646ms

<iframe src="//giphy.com/embed/3oriNYQX2lC6dfW2Ji" width="480" height="270" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/foxhomeent-xmen-quicksilver-3oriNYQX2lC6dfW2Ji">via GIPHY</a></p>

Wow, we're talking over 10x faster. 


