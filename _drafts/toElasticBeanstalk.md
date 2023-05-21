---
layout: post
title:  "My experience attempting to migrate to Elastic Beanstalk"
date:   2023-12-27 13:30:00 -0800
categories: tech aws
---

TL;DR: Trying to migrate a site from Heroku to Elastic Beanstalk was a miserable experience, and I wouldn't recommend it. 

I decided to embark on migrating my small feed reader site, Plumfeed from Heroku to AWS's Elastic Beanstalk (EBS). The cost of operating on Heroku jumped from $7 to $12 a month. In the grand scheme of things, this is relatively inexpensive, but the price jump motivated me to try to operate it in a more cost effective manner. Besides, all I really need is to run Node, have access to a Postgres database, and run a worker everyday. 

Now, I'm not a particularly talented AWS developer. For earlier jobs I've made use of S3, DynamoDB, ECS, lambdas, and CloudFormation. But, this was within the safe confines of an already well-taylored system; all the various security zones and IAM roles were setup and usage patterns were were well established. In this case, I was starting fresh.

For a project that I naively hoped wouldn't be overly difficult, I ran into problem after problem. A well designed system typically makes it difficult to do the wrong thing. EBS seems to gleefully push you towards the wrong thing while charging you for the privilege of doing so. Here's a sample of some of pain:

 - EBS lets you select a database instance size that's too small to use. Then, when you find the error, they tell you unhelpfully to look up the documentation. They should prevent me from making this choice at all in the dashboard, or at least include a link to the documentation in the error

 - When going through the EBS creation process you must go into "Configure more options" and make sure you add it to the vpc and a couple subnets. I cannot understand why a mandatory step is hidden behind a customize more options banner. 

 - I ran into sooo many timeouts when trying to access my database, or any number of requests. Most of the time, this is because my security zones weren't configured properly. It would be nice to get a 401 status, or a more useful error messages.


 - Not explicitly EBS related , but When creating a lambda, [the documentation](https://aws.amazon.com/blogs/compute/using-lambda-layers-to-simplify-your-development-process/) points out that the AWS SDK is available if you just use `require('aws-sdk')` (or the import equivalent). This would be a wonderful feature if it was true. Instead, there are [multiple](https://github.com/aws/aws-sdk-js-v3/issues/3230) [Github](https://github.com/aws/aws-sdk-js-v3/issues/3386) [issues](https://github.com/aws/aws-sdk-js-v3/issues/3578) pointing out this doesn't work. The solution was helpfully provided in [Stack Overflow](https://stackoverflow.com/questions/72049613/cannot-import-package-in-aws-lambda-with-nodejs14-x-es-module): `import AWS from '/var/runtime/node_modules/aws-sdk/lib/aws.js';` Sadly, this issue has existed for over a year.

So, ironically, my attempts to save a few bucks ended up costing me way more from spinning up misconfigured servers. 

On the plus side, I did learn more about VPCs, and IAM rules, and AWS in general. I also nearly completed my migration goal, although I never figured out why `elasticbeanstalk.describeConfigurationSettings` call never resolved. I could have completed this project, but I just ran out of steam. Doing this kind of IT work in addition to my day job is pretty draining, especially when it felt like I was constantly overcoming obstacles to things that should've been simple. 

I also spent some time playing with [Vercel](https://vercel.com/). It's very Heroku-like in terms of how easy it is to setup. I basically got my entire site moved over in part of a weekend. Unfortunately, because I need a regular cronjob, the price will jump to $20/mo after they move out of Beta, which isn't worth it to me. So, I'm sticking with my reliable Heroku setup. 




=====

EBS gives you an absurd amount of leeway to footgun yourself over and over again. 
When things don't go well, it's difficult and painful to dig yourself out.

My original deployments failed because they let me choose an instance size that was too small for my postgres database. They should, just, not let me make that choice?

Creating RDS database: awseb-e-9ybwmn3ynr-stack-awsebrdsdatabase-oas3p2qopk88 failed Reason: Resource handler returned message: "RDS does not support creating a DB instance with the following combination: DBInstanceClass=db.t2.micro, Engine=postgres, EngineVersion=14.6, LicenseModel=postgresql-license. For supported combinations of instance class and database engine version, see the documentation. (Service: Rds, Status Code: 400, Request ID: 8e3079b2-3c07-4b64-a40a-489095ab00da)" (RequestToken: ae798736-9fee-3bd1-29bc-5d850b9497fe, HandlerErrorCode: InvalidRequest)

Why does it even let me do this? Give me an error before creation. And give me a documentation link. I don't know where this is.




I had so many timeout errors with no explanations. Usually they were because I had a security zone misconfigured, but a helpful error message would've been nice.


Many of the tutorials just flat out don't work.

Deployments take an extremely long time. It's very difficult to get anything done.

A lot of documentation indicates that the AWS Node library is available in the lambda, so there's no need to upload it. However, for months accessing it hasn't work. I had to import it in this backwards, undocumented way: import AWS from '/var/runtime/node_modules/aws-sdk/lib/aws.js'. This is embarassing.

It ends up costing money to try things over and over. 

I also played around with Vercel. It works a lot like Heroku. Very erognomic, very easy to deploy. Unfortunately, the crons don't work as nicely. They have very short run times on the hobby tier. 


