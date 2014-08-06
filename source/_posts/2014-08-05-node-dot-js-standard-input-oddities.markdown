---
layout: post
title: "Node.js Standard Input Oddities"
date: 2014-08-05 19:32:59 -0700
comments: true
categories: node
---

## Digging To Find the Problem
At work recently we were doing our best to automate some of the authoring workflow on our new site (that's based on 
the [Metalsmith](http://www.metalsmith.io/) static site generator). While doing this we were writing a simple Python 
script that automated the steps of creating a file, running the build and saving the files.

We ran into some odd behavior though after running our build and then asking for further input from stdin. After digging 
into the problem we eventually discovered that it only happened after running one of our tasks that uses 
[grunt-shell](https://github.com/sindresorhus/grunt-shell) although we discovered that setting the `stdin` option to 
`false` for the command configuration solved our problem.

From this my initial thought was that grunt-shell must not be cleaning something up when it uses `process.stdin`. So I 
dug into grunt-shell and discovered that as far as I could tell things were being done correctly. I tried adding some 
explicit calls to `process.stdin.pause();` since there was a call to `process.stdin.resume();` but to no avail. Alright, 
time to boil this down to a simpler repro.

## Boiling Down to the Minimal Case
First I found that if I ran similar commands to what grunt-shell used in a node script (outside of grunt) I could 
produce similar behavior. So then I got it down to just a couple lines in files, and finally a single command, no files 
necessary.

Alright, to get things started lets check out the node version:

```
bash-3.2$ node -v
v0.10.30
```

So the first step on this path is to show that node itself can work in a situation where stdin is needed just fine:

```
bash-3.2$ read -p "Works? (y/N) " yn && node -e "console.log('hi');" && read -p "Works? (y/N) " yn
Works? (y/N) y
hi
Works? (y/N) y
```

Yup, we were asked if things worked twice and I could respond "yes" both times. Great, so now lets do something similar 
to what grunt-shell was doing, using `process.stdin.resume()`.

```
bash-3.2$ read -p "Works? (y/N) " yn && node -e "process.stdin.resume(); process.stdin.pause();" && read -p "Works? (y/N) " yn
Works? (y/N) y
Works? (y/N) bash: read: read error: 0: Resource temporarily unavailable
```

Now that's some weird behavior. I got to say yes it was working the first time, but the second time bash is telling us that stdin
(file descriptor 0) is temporarily unavailable. Huh, so are there other things that cause this same behavior with stdin? 
Maybe if we use the [newer stream model](http://blog.nodejs.org/2012/12/20/streams2/):

```
bash-3.2$ read -p "Works? (y/N) " yn && node -e "process.stdin.on('readable', function () {}); process.stdin.removeAllListeners('readable');" && read -p "Works? (y/N) " yn
Works? (y/N) y
Works? (y/N) bash: read: read error: 0: Resource temporarily unavailable
```

Dang, still leaving stdin in a bad state. Now it's entirely possible I'm just not cleaning something up properly with 
these so I tried to narrow it down a bit further. Let's try something that shouldn't really be intrusive at all:

```
bash-3.2$ read -p "Works? (y/N) " yn && node -e "process.stdin.setEncoding('utf8')" && read -p "Works? (y/N) " yn
Works? (y/N) y
Works? (y/N) bash: read: read error: 0: Resource temporarily unavailable
```

Ug! Still busted. Interesting. In doing some searching I discovered
[an issue](https://github.com/joyent/node/issues/7481) that some people were seeing odd behaviors from just referencing 
`process.stdin`. I thought "There's no way just referencing stdin could cause this problem.", but hey it's worth a try:

```
bash-3.2$ read -p "Works? (y/N) " yn && node -e "process.stdin;" && read -p "Works? (y/N) " yn
Works? (y/N)
Works? (y/N) bash: read: read error: 0: Resource temporarily unavailable
```

For reals!? Just referencing `process.stdin` puts node into a state where stdin won't be released until things are well 
and done.

## Node.js Is Hanging Onto stdin
Alright, so if you touch `process.stdin` in any way node.js will hang onto that file descriptor until the process is 
completely finished. I can't seem to find a way to get node to clean things up once stdin has been used. I hope there's 
a workaround, but I haven't found it, if you have a workaround leave a comment. I also did some searching through the
open issues on the node repository, and couldn't find anything quite like this, so I opened a
[new issue](https://github.com/joyent/node/issues/8083).
