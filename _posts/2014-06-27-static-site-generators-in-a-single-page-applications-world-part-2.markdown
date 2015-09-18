---
layout: post
title: "Static Site Generators in a Single Page Applications World Part 2"
date: 2014-06-27 20:14:16 -0700
comments: true
categories:
    - js
    - node
    - metalsmith
---
In [part one](http://blog.boushley.net/blog/2014/06/18/static-site-generators-in-a-single-page-applications-world/) of
this series I talked about the benefits of Usemin and Uncss, but getting things rolling took some work. Most of the
problems we were having came from interactions between the plugins we were using, and not necessarily as flaws with the
tools generally.

##Usemin
The first problem I ran into wasn't a blocker, but it slowed down our build significantly. Usemin duplicated work when 
it found multiple blocks defining the same destination file. For most applications this wasn't a problem. Many 
applications are single page applications today so this wouldn't affect them. Many others use a single dynamic page 
to serve up their pages, again lowering the impact of this issue. However with a static site generator it's possible to 
end up with many html files that define the same script include block (in our case we were including this block via a 
template partial on essentially every page). Luckily a [bug](https://github.com/yeoman/grunt-usemin/issues/289) already
existed for this and a [pull request](https://github.com/yeoman/grunt-usemin/pull/324) was even already open to resolve
it. After reviewing the PR though I decided to create my own [PR](https://github.com/yeoman/grunt-usemin/pull/382)
solving the problem in a slightly different way. The solution was to do some duplication checking to ensure we only 
added each unique destination file once. My PR landed and the fix is now part of usemin 2.3.0.

##Uncss + Usemin
The harder problem to figure out was due to an interaction between Uncss and Usemin. Uncss tries to parse the 
stylesheets it should process off of your page, but because I wanted this to run before usemin ran I needed to supply my 
own stylesheet via the `stylesheets` option. I had something like this:

####Broken Code, functioning code below
```js
grunt.initConfig({
    uncss: {
        options: {
            stylesheets: ['site.css'],
            htmlroot: 'build/public',
            csspath: 'css/'
        },
        file: [{ html: ['**/*.html'] }]
    }
    // Usemin declaration left out for brevity
});
```

The problem I was having was that the path to my css files was always different based on where the html file being processed
was located. I assumed that the `stylesheets` option would be resolved relative to the `htmlroot` option. For some 
I kept banging my head against the `htmlroot` option and `csspath` option trying to figure out how I could make this 
work when the html files were in different directories. I knew that `stylesheets` was being evaluated from where the 
html file was located, but since `csspath` was static the relative path was different per file... And that's when it hit 
me, I was making this way harder than it was. All I had to do was make my reference in `stylesheets` absolute! So I 
changed to something like this:

```js
grunt.initConfig({
    uncss: {
        options: {
            stylesheets: ['/css/site.css'],
            htmlroot: 'build/public',
        },
        file: [{ html: ['**/*.html'] }]
    }
    // Usemin declaration left out for brevity
});
```

With an absolute path to my stylesheet it was now evaluated with `htmlroot` as the base and everything was working.

##Filerev
After working through those problems I wasn't excited to get the last piece of the build working, but the benefits 
weren't something I could pass up. So I started on setting up [filerev](https://github.com/yeoman/grunt-filerev). 
Filerev is a great plugin that does resource versioning, you point it at a folder and it will update each of the files 
that match the given patterns to contain a part of a hash for the files contents. This makes the files unique meaning 
that they can be cached forever. I was estatic to find that when I dropped filerev into the build with usemin it just 
worked. I'm pretty sure I spent more time ensuring that it was working how I expected that it actually took to get 
setup.

## Conclusion
There are some great tools for the node build pipeline. They may not fit your usecase exactly to start, but they seem to 
cover most usecases out of the box while being flexible enough to fit unexpected needs. Until next time.
