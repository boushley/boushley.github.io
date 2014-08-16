---
layout: post
title: "Internet Explorer Tracking Protection and Shared CDNs"
date: 2014-08-07 19:03:52 -0700
comments: true
categories: IE Shared-CDN
---

## How I Got Here
At work we recently released an [online demo](https://de.demo.extrahop.com/extrahop/) of our product. We were pretty
happy to get this out and there is an expanded version of the demo for the Enterprise Edition of our product. To see
this full demo you need to fill out a [form](http://www.extrahop.com/enterprise/start/).  This all went great, until
about a day after launch we get an email from the CEO of our company talking about he and his wife were unable to use
the site in IE 11. I was pretty confident we had tested this, but the CEO was saying the site was broke... so I
started digging in.

I checked IE 11 in a VM and things worked fine. Well that's good and bad, at least I don't look like a total idiot that
just didn't test in even a modern browser. The down side is that now I have to figure out why it isn't working in this
specific case. Eventually got dev console output in IE 11 and saw this:

```
SEC7114: A download in this page was blocked by Tracking Protection.  https://ajax.googleapis.com/ajax/libs/jquery/2.1.1/jquery.min.js
```

Hmm, so "Tracking Protection" is blocking my jQuery from the Google CDN. What?!

## Shared CDNs
The [Google Hosted Libraries](https://developers.google.com/speed/libraries/devguide) is a Shared CDN. Google is hosting 
a set of popular JavaScript libraries so that multiple sites can use them. They are not the only ones to offer a service 
like this, [Microsoft](http://www.asp.net/ajaxlibrary/cdn.ashx) and [cdnjs](http://cdnjs.com/) both have their own 
versions. Shared CDNs are a great idea, they allow for the benefits of caching on common files across multiple websites. 
With frameworks like jQuery being so [ubiquitous](http://trends.builtwith.com/javascript/jQuery) it's fairly likely it 
may be cached before a user ever hits your site.

Those are the up sides to a shared CDN, but back to the part where IE is "protecting" this browser so well that it can't
use jQuery.

## Alright, So What is "Tracking Protection"
After doing some digging it looks like in IE 9 this idea of
[Tracking Protection](http://windows.microsoft.com/en-us/internet-explorer/products/ie-9/features/tracking-protection)
was introduced. It isn't turned on by default but after a couple clicks you can have you're wonderful new Tracking 
Protection enabled. The default version of this is the "Personalized Tracking Protection List". The way this works is 
that when IE detects the same script being loaded on multiple different sites it decides it must be tracking code 
(because what else would any set of sites want to share? right?) and marks it as probable tracking code.

To be fair in the default mode this personalized list simply identifies these values and then lets the user decide 
whether to block or not. However this browser was running in the more aggressive mode where it just auto blocks these
scripts.  So there you go. IE blocks scripts that are shared by many sites. This is probably pretty effective (initially,
until real tracking companies with all their money just start sharding across multiple domains) but it also destroys the
idea of a shared CDN for common code like jQuery.

## Side Note: Foil Hat Time
My initial reaction was that certainly since Microsoft has their own shared CDN for jQuery it would be whitelisted. Now 
I admit this blog post would be more fun if that were the case, but it looks like Microsoft doesn't have any sites 
whitelisted. Their shared CDN is just as broken as Google's shared CDN with Tracking Protection on.
![IE Blocking Shared CDN Files](/images/ie-blocking-cdns.png)

## CDN Fallbacks
So, here's the thing. I knew I should be using a
[CDN fallback](http://www.hanselman.com/blog/CDNsFailButYourScriptsDontHaveToFallbackFromCDNToLocalJQuery.aspx) but
hadn't put one in. I planned to have a fallback eventually, but I rationalized that the Google CDN never goes down so 
what would be the harm in just getting this out and worrying about a fallback later? I let my confidence in Google's
infrastructure lull me into a sense of security. Well that was my mistake, Google's infrastructure was fine, but this
"Tracking Protection" was blocking the CDN regardless.

### If you use a shared CDN you should be using a fallback for your CDN.

## More "Protection"
We got a fallback in place for the Google CDN and now the page works fine with Tracking Protection in full swing. 
However, there are still problems with the page that we can't easily resolve. We use [Typekit](https://typekit.com/)
for our fonts and their loading script is blocked by Tracking Protection. In the case of Typekit it's actually worse, 
since there isn't an easy way to fallback. The [Google Fonts](https://www.google.com/fonts) also has this same problem. 
It's disappointing that IE has this feature so easily accessible that really destroys sharing of resources on the web.
