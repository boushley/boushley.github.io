---
layout: post
title: "Origins of a Programming Language"
date: 2014-05-24 20:48:59 -0700
comments: true
categories: programming languages, Go
---
I've spent a couple of weeks now exploring [Go](http://golang.org/) and I'm really happy. The language has lots of 
interesting features, including a great standard library. That got me thinking about some of the other languages I've
experimented with and the impact of a language's origin. 

I've experimented with a few languages that were created by programming language connoisseurs, and these usually have 
some really fascinating features and seem to solve many of the problems you face in development, but they usually lack 
any sort of community, standard library or tooling. They appear to have the necessary ingredients, but can't seem to get 
the traction necessary to get going.

Another interesting language I've experimented with is [Kotlin](http://kotlin.jetbrains.org/). The language has some 
interesting features (compiling to JVM bytecode and JavaScript is pretty cool), but the most interesting thing to me was 
the amazing tooling the language had when it came out. Now it makes sense, [JetBrains](http://www.jetbrains.com/) makes 
the best IDEs, so when they make a language you know the tooling is going to be solid.

Then you have languages like Go that are developed by a powerhouse technology company with the intent to use it 
internally. The language does a great job of solving problems faced by large or diverse teams, and teams that are trying 
to get stuff done quickly. One example of problems Go solves is code formatting. Code formatting is always a tension 
point in teams, and often there is no standard because it's too contentious to arrive at one. Instead Go has
[gofmt](http://blog.golang.org/go-fmt-your-code) which should be used to ensure that code formatting is consistent, and
not just across a single team but across the entire language.

These characteristics of languages make sense when you know the origin of the language, but I hadn't realized until now
how important a languages creation myth was to the language itself.
