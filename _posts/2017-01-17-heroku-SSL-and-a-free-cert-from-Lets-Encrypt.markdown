---
layout: post
title:  "Heroku SSL and a free cert from Let's Encrypt"
date:   2017-01-17 11:35:00 -0800
categories: security ssl
---
I prefer to build and use websites delivered over HTTPS. However, my RSS feed reader, [Plumfeed](https://plumfeed.com) (which will be the subject of a future post) was using [Cloudflare](https://cloudflare.com) for SSL termination. So, the connection between the user and Cloudflare was secure, but the connection between Cloudflare and Plumfeed, hosted on [Heroku](https://heroku.com), was not. In the past year, Heroku came out with a free SSL solution, and free SSL certificates can be obtained from [Let's Encrypt](http://letsencrypt.org). It turned out setting this all up was pretty straightforward. I followed Heroku's instructions [here](https://devcenter.heroku.com/articles/ssl) and [here](https://devcenter.heroku.com/articles/acquiring-an-ssl-certificate), and this somewhat dated medium post for Let's Encrypt [here](https://medium.com/@franxyzxyz/setting-up-free-https-with-heroku-ssl-and-lets-encrypt-80cf6eac108e#.te94icwgh).

Here's roughly what my steps looked like. First I installed certbot:

{% highlight bash %}
$ brew install certbot
{% endhighlight %}

Then I got the process started for creating a cert on the domains I own. I was not able to use a wildcard, I had to specify specific domains, namely plumfeed.com and www.plumfeed.com
{% highlight bash %}
$ sudo certbot certonly --manual
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Please enter in your domain name(s) (comma and/or space separated)  (Enter 'c'
to cancel):plumfeed.com www.plumfeed.com
Obtaining a new certificate
Performing the following challenges:
http-01 challenge for plumfeed.com
http-01 challenge for www.plumfeed.com

NOTE: The IP of this machine will be publicly logged as having requested this
certificate. If you're running certbot in manual mode on a machine that is not
your server, please ensure you're okay with that.

Are you OK with your IP being logged?
-------------------------------------------------------------------------------
(Y)es/(N)o: Y
{% endhighlight %}

I was not exactly comfortable with having my IP logged, but I reluctantly agreed. I emailed Let's Encrypt about it, and they said this info is not actively publicly logged, "but the information can be made public, for example as part of an investigation into mis-issuance. We do not consider it to be confidential information. It's important that we be able to be transparent about our validation process if necessary since we are trusted by many on the Web." 

Next I had to update my site to resolve these urls (x's and y's are placeholders of course).

{% highlight bash %}
Make sure your web server displays the following content at
http://plumfeed.com/.well-known/acme-challenge/xxxxxxxxxxxxxxxxxxxxxxxxxxxx-xxxxxxxxxxxxxx before continuing:

yyyyyyyyyyyyyyyyyyyyyyyyyyyy-yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy--yyyyyyyyyyyyyy
{% endhighlight %}

A couple notes here. First even though Plumfeed redirects all http traffic to https, that did not negatively affect this process. Secondly, you're asked to display unique content per domain that you enter earlier (I'm only showing one for brevity). And you have to do one at a time or it'll abort the process. It wasn't clear to me the first time because the urls looked similar.

Then, assuming that certbot can resolve those URLs, you have have your certs!

{% highlight bash %}
Generating key (2048 bits): /etc/letsencrypt/keys/0000_key-certbot.pem
Creating CSR: /etc/letsencrypt/csr/0000_csr-certbot.pem
Congratulations! Your certificate and chain have been saved at
   /etc/letsencrypt/live/plumfeed.com/fullchain.pem.
{% endhighlight %}

Great. Now we have to add them to Heroku.

{% highlight bash %}
$ sudo heroku certs:add /etc/letsencrypt/live/plumfeed.com/fullchain.pem /etc/letsencrypt/keys/0000_key-certbot.pem

=== Your certificate has been added successfully. Update your application's DNS settings as follows
Domain            Record Type  DNS Target
────────────────  ───────────  ──────────────────────────────
www.plumfeed.com  CNAME        www.plumfeed.com.herokudns.com
plumfeed.com      ALIAS/ANAME  plumfeed.com.herokudns.com
{% endhighlight %}

I updated my DNS settings as suggested on my registrar, [Name.com](https://name.com). I used a CNAME for plumfeed.com without problem. I checked to see if it was working properly.

{% highlight bash %}
curl -vI https://plumfeed.com
{% endhighlight %}

Initially it was:
{% highlight bash %}
* Server certificate: sni112605.cloudflaressl.com
{% endhighlight %}

I checked the next day after the DNS records were updated. Then it changed to:
{% highlight bash %}
* Server certificate: Let's Encrypt Authority X3
{% endhighlight %}

<img alt="Zoidberg Hooray" src="{{ site.url }}/assets/herokussl/zoidberg.jpg" style="margin:auto;" />

Now that my traffic is going directly to Heroku, I no longer need Cloudflare, so I disabled the service. I have no real problems with Cloudflare. In fact, I think they offer some pretty great services for load balancing and defense from DDoS attacks. Plumfeed only has a few users, so I'm not too concerned with this at the moment.

I hope that Github Pages comes up with a mechanism so I can do something similar with SSL and a custom domain, so I can securely deliver this blog's contents.

If you end up getting a free cert from Let's Encrypt, consider sending a few bucks to the [EFF](https://eff.org)!


