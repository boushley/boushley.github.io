---
layout: post
title: "Web Applications Without Servers"
date: 2015-10-03 07:03:52 -0700
comments: true
categories:
    - js
    - JAWS
    - AWS
---

## Web Applications using Just AWS Without Servers
In my [last post](/2015/10/02/serverless-architecture-with-jaws/) I discussed the basics of [JAWS](need link) a tool 
enabling serverless architectures on AWS. In this post we'll talk about how you can add a few more key AWS services and 
end up with a complete monstrously scalable Web Application without any servers and *it's cheap*.

The combination we talked about last time works for your API, and for some applications that's all they need to host. 
But this is about web applications, and for those there's a load of static content you need to host. On top of needing 
to host these static assets somewhere, the [same-origin policy](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)
means that if your static assets are on a different domain from your API, you have to deal with [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS)
which can be a big pain.

Today I'll show you how to host your web application without any servers using JAWS, Amazon S3 and CloudFront, using a 
single domain / origin.

## The Pieces

### Review

[AWS Lambda](https://aws.amazon.com/lambda/) lets you provide piece of code and AWS will run that code in response to
events. They take care of running that code as much or as little as necessary.

[Amazon API Gateway](https://aws.amazon.com/api-gateway/) is a service proxy, with the key benefit of translating HTTP 
Requests to AWS events that Lambda can process.

[JAWS](https://github.com/jaws-framework/JAWS) is an open source project that automates the setup of Amazon API Gateway 
endpoints and Lambda functions.

### The new Pieces

#### Amazon S3
One of the oldest Amazon services [Amazon S3](https://aws.amazon.com/s3/) is for storing files in a highly available and extremely
redundant manner. S3 supports [hosting static websites](http://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteHosting.html) today.
We'll be using this functionality and teaming it up with JAWS.

#### Amazon CloudFront
[Amazon CloudFront](https://aws.amazon.com/cloudfront/) is the AWS CDN. It is highly configurable, and works well using 
Amazon S3 as an [origin server](http://docs.aws.amazon.com/general/latest/gr/glos-chap.html#originserver). CloudFront 
also allows routing between multiple origin servers based on [CacheBehaviors](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-cloudfront-cachebehavior.html).
Which is a fancy way of saying that CloudFront lets you route traffic between different backends based on the path of 
the request.

## Alright, let's bring it together
First we need to have our static assets hosted in S3. Setup S3 static website hosting, and be sure that this works by 
visiting the URL that the S3 console gives you for your page.

Next we need to setup our API Gateway endpoints. To get these to be easily grouped on our CloudFront distribution we 
should prefix all of these routes with a common path, like `/api/`. JAWS makes it easy to create API Gateway endpoints 
that are backed by Lambda services. Get one of those setup and test it with something like curl to be sure it works as
expected.

Finally we bring it all together with a CloudFront distribution with two origins. The first (default) origin is our S3
bucket, then we add our API Gateway endpoint as our second origin. The base path that we set on the API gateway origin
is best to set to your API Gateway stage. Finally we can setup a Cache Behavior that routes traffic for a specific set
of routes, like `/api/*` (the prefix we used when setting up our API Gateway endpoint) to our API Gateway origin.

## Manual configuration! Can we automate that?
Indeed, we can make this part of our JAWS resources CloudFormation template. Luckily JAWS is extensible using
[awsm modules](https://github.com/awsm-org/awsm) and I wrote the [awsm cloudfront](https://github.com/boushley/awsm-cloudfront)
module that contains a CloudFormation resource definition that automates most of this setup. At the time of writing there are some
manual steps where you have to modify the CloudFormation template to fill in your S3 bucket and API Gateway endpoint, but there
are [extensions](https://github.com/awsm-org/awsm/issues/2) to the awsm module format that would make that more automated.

## Drawbacks
There are lots [paths](https://github.com/boushley/awsm-cloudfront#watch-your-paths) involved here and they
can get confusing, so keep an eye on that.

There are also some missing features in API Gateway. One of these missing features is the ability to [dynamically set 
headers on the HTTP response](https://forums.aws.amazon.com/thread.jspa?messageID=675607) based on values in the Lambda
event response. This is necessary for setting cookies or performing a HTTP redirect, which require setting the `Cookie`
and `Location` header respectively. Another missing feature is the ability to set the HTTP Status Code based on something
in the Lambda event response. This makes something like OAuth a pain to implement, however it's definitely doable.

I've heard of a few other drawbacks, but none of them are show stoppers. There are bumps on this road, but it's a pretty 
amazing setup without much of the common frustrations.

## This is an exciting time
This is amazing! Previously to host a web application with an API in a robust manner was fairly costly either in money 
or time (and often in both). This system makes it cheap and relatively simple to setup and deploy a web application. 
Even more once you've got that application deployed it scales well beyond many standard web application architectures 
without getting any more complex.

Keep an eye on JAWS and the serverless architecture, this could be huge!
