---
layout: post
title:  "UUID creation in Postgres vs Node"
date:   2017-03-24 11:35:00 -0800
categories: uuid postgres javascript node
---

I was making a new [Postgres](https://www.postgresql.org/) database table and I decided to do [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) primary keys instead of auto-incremented integers as I had done in the past. There are some [interesting discussions](https://blog.codinghorror.com/primary-keys-ids-versus-guids/) around why UUIDs are a good idea in spite of their increased size. 

When I wrote the query for insertion, I defaulted to letting Postgres do the work for UUID creation. 

{% highlight sql %}
INSERT INTO users (id, username) 
	VALUES (uuid_generate_v4(), 'newtang');
{% endhighlight %}

I didn't have a good reason for doing this other than the habit of letting the database handle the primary key creation. But, the more I thought about it, the more it made sense to generate the UUID in Node (this was a [Node.JS](https://nodejs.org/en/), Postgres stack). I wouldn't have to wait for the Postgres `INSERT` query to complete to use the generated UUID elsewhere. If I needed this UUID in multiple places, I could fire off several queries at once or more easily build out a transaction. But, is there a performance difference between the two? I'm not a great Postgres guy, but with the help from Stack Overflow, I cobbled together this method.

{% highlight sql %}
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
{% endhighlight %}

[`PERFORM`](https://www.postgresql.org/docs/9.6/static/plpgsql-statements.html) just executes this function, but discards the result. Oh, and I had to activate `\timing` to show the duration. So I ran this a few times, and my results:

{% highlight javascript %}
43286.626ms
57559.374ms
54655.993ms
{% endhighlight %}

Seems slow. If anyone has another way of testing this on Postgres please let me know. On to Node.

{% highlight postgres %}
const uuidV4 = require('uuid/v4');
console.time("uuid");
for(let i=0; i<1000000; ++i){
	uuidV4();
}
console.timeEnd("uuid");
{% endhighlight %}

I'm using the [uuid npm](https://www.npmjs.com/package/uuid) module. The `console.time` and `console.timeEnd` are built-in, handy ways to measure duration in Javascript without having to build `Date` objects and comparing. Anywho, the results:

{% highlight javascript %}
4792.588ms
4809.111ms
4709.646ms
{% endhighlight %}

![Zooooooom]({{ site.url }}/assets/uuid-postgres-node/quicksilver.gif)

Dang, we're talking 10x faster! Granted, this isn't a perfect test. I ran this on my Mac laptop which isn't going to have the same hardware as a server. But, it's hard to imagine the disparity being that much different on different hardware. Also, my project will probably never involve cranking out rapid fire UUIDs at this rate, but it's fascinating nonetheless. Time to create some UUIDs in Node!

**Edit 4/13/2017**<br/>
[Jason Owen wrote a very nice follow up](https://jasonaowen.net/blog/2017/Apr/13/benchmarking-uuids/) that includes timing UUID generation from the [pgcrypto extension](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) which I was unaware of. He also made some good points on the [RETURNING clause](https://www.postgresql.org/docs/9.6/static/sql-insert.html), and pre-optimization. Go read it!






