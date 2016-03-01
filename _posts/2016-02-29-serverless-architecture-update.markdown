---
layout: post
title: "Web Apps Without Servers - Update"
date: 2016-02-29 19:03:52 -0700
comments: true
categories:
    - js
    - AWS
    - Serverless
---

## Serverless Update
In my last set of posts back in October I talked about the serverless architecture and how it had so much potential to
change the way we build web apps. It turns out that potential has only gotten greater!

### Redirects
Back in October there was a glaring hole in the API Gateway mapping templates. That was that you couldn't use a value
from the Lambda response to fill in a header in your response template. Luckily [back in December](https://forums.aws.amazon.com/thread.jspa?threadID=216264)
support for this was added to API Gateway.

This means that no we can set headers in our responses from AWS Lambda. Suddenly redirects are no longer something you
have to hack your way around.

### Cookies
Setting cookies was a pretty big missing piece too. As it turns out this is enabled by the same functionality that 
enabled redirects. Since we can now set response headers based on Lambda return values we can now set the `Set-Cookie` 
header enabling cookies!

There is one drawback here. Unfortunately we cannot set more than one cookie per request. There is no way to send 
multiples of the same header as would be required for setting multiple cookies.

### Serverless, the future is bright
Serverless has reached a point of maturity where we can use it as a serious contender. The ecosystem is still sparse, 
but the [Serverless](http://serverless.com) startup is working to fix that.
