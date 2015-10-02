---
layout: post
title: "Serverless Architecture with JAWS"
date: 2015-10-02 07:03:52 -0700
comments: true
categories:
    - js
    - JAWS
    - AWS
---

## Where we're going, we don't need <s>roads</s> servers.
With the recent-ish release of [Amazon API Gateway](https://aws.amazon.com/api-gateway/) AWS now has everything you need for a serverless 
architecture. Depending on how you work this should sound like the coolest thing ever, or the scariest thing ever. In 
its current state this architecture does not fit every usecase, but even as such an early technology it is applicable to 
an amazing range of projects.

## The Pieces

#### AWS Lambda
Last year Amazon announced [AWS Lambda](https://aws.amazon.com/lambda/) in a preview form, and earlier this year they release Lambda as a production service. 
Lambda is a dramatic shift in the way things run on AWS. Lambda charges for compute time in 100ms intervals. You provide 
a piece of code and AWS will run that code in response to events.

This is really interesting because you don't need to have a server always ready to take requests. You don't need to 
worry about scaling your servers with demand. Events come in and your code processes them, that's all you have to worry 
about. AWS scales up their infrastructure as needed and will run as many simultaneous instances of your code as 
necessary.

Alright, so Lambda can respond to events, and to start these were things like SNS triggers, S3 upload notification, 
Kinesis processing, but then it all changed.

#### Amazon API Gateway
Then in the middle of this year Amazon launched Amazon API Gateway. This service lets you offload much of the heavy 
lifting around making an API production ready (DDoS shedding, Environment Differentiation, etc.) sells it. However the 
most important thing API Gateway enables is the translation of HTTP Requests into an **event** and the translation of 
the Lambda response back into an HTTP Response.

Now you can standup your API endpoints using API Gateway and then use Lambda to perform the backend processing. Setting 
up a proof of concept around this is pretty straightforward, especially since the AWS Lambda console has a helper that 
creates an API Gateway endpoint in front of your service. However there are a lot of moving parts in this, and things 
start to get messy, fast.

#### [JAWS](https://github.com/jaws-framework/JAWS)
JAWS (standing for Just AWS Without Servers) is an open source project that automates the setup of Amazon API Gateway 
endpoints and Lambda functions. It handles environment variables, cross-region replication, and multiple stages. The 
patterns and best practices JAWS recommends also encourage writing your code in a testable, AWS independent manner.

## So What?
It's definitely interesting that you can hook up these services to make something that behaves the same as the servers 
we've used for years, but why should you care? Why should we bother learning this new way of doing things?

Running a service with moderate load, like 16,000 request/day at an average of 200ms would often require you to stand up 
at least two servers (more if you want truly high availability), if those servers are the common c3.large that's going 
to cost you $2.97/day (assuming 1 year up front payment). If you can run on an m3.medium that drops to $0.97. Running 
AWS Lambda will cost just $0.05/day. If you include the cost of API Gateway that goes up to about $1.80/day, but the raw 
EC2 instance calculations don't include the prices of an ELB ($0.60/day) or some service to handle DDoS shedding. So 
cost is definitely a reason to consider this new architecture.

However cost isn't the only reason. One of the beauties of this setup is that it scales to extremely large services as 
well. If you get a huge burst of traffic from a Hacker News or Reddit front page this setup isn't going to fall over. 
This setup isn't going to require minutes for more EC2 instances to turn on. Lambda handles scaling for you, and you 
continue running your setup at peak efficiency. Most architectures out there require you to choose developer 
productivity or scalability. With JAWS you no longer have to choose, you can easily get a service started and it will 
continue to handle your traffic as your site grows.
