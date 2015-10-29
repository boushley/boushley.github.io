---
layout: post
title: "HTML5 Style Deep Linking on S3"
date: 2015-10-28 19:03:52 -0700
comments: true
categories:
    - js
    - AWS
    - Serverless
---

## The Want
In my last post I talked about running a Single Page Application using [JAWS and Amazon S3](/2015/10/03/web-apps-without-servers/).
One of my first questions after getting that working was how I might be able to support deep linking. Specifically how 
might I be able to handle HTML 5 style deep linking. [History Management (e.g. pushState)](http://caniuse.com/#feat=history)
has pretty good browser support and the new style URLs look much cleaner, so in new projects this is the way I like to 
go.

## The Problem
Usually to support the new style URLs you need a backend server that can dynamically render the same routes that the 
front end is rendering dynamically. And when we're hosting a site on S3 we don't have that capability. We are very much 
hosting a static site, no frills.

For a while I thought I was stuck. I didn't see how I could have those nice URLs and still support sharing links or even 
just browser refreshes. If you refreshed the browser on some page other than the index (like /foo/bar) you get the nasty 
S3 Key Not Found screen.

## The Ray of Light
I could at least be sure the S3 Key Not Found / 404 page was a nice page... then it hit me! What if we just tell S3 to 
serve our Single Page App as the error page?

I hopped into the S3 console and changed the configuration on my bucket. When enabling website hosting for an S3 bucket 
you can also set the "Index Document" and "Error Document". So on my bucket I configured both documents to be 
`index.html`. The result is that my page loads whenever you go to a non-existent route.

This means that when you arrive at `/` initially you load the application as expected. As you navigate around the URL 
updates. If you hit refresh you are served a 404, but the application loads recognizes the URL and renders the page you 
expect. The only way you can tell it's a 404 is if you open up your tools and look at the Status Code.

This works amazingly well, you can see it in action [here](http://s3-static-test.s3-website-us-west-2.amazonaws.com/).
(That gif is so mesmerizing, I've spent far too long watching that thing loop...)

## So what about actual unknown pages?
This presented one other oddity, what about actual 404s? The solution I came up with was to just handle those 404s in 
the Single Page Application itself. So the same application still loads, but since the application doesn't [recognize the 
URL](http://s3-static-test.s3-website-us-west-2.amazonaws.com/this/page/is/not/real) it shows an error screen.

## Serverless
Running servers is quickly becoming something you should outsource to the experts. There will be oddities along the way 
(like serving an HTTP 404 when the URL is actually fine), but the wins you get from completely washing your hands of any 
infrastructure management are huge!

I'm sure this approach will offend some peoples sensibilities, but it's amazing to see how much can be done without 
needing to maintain your own servers.
