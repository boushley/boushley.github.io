---
layout: post
title: "Static Site Generators in a Single Page Applications World (Part 1)"
date: 2014-06-18 20:24:56 -0700
comments: true
categories: js, node, metalsmith
---
At [work](http://www.extrahop.com) I've been working to get us migrated to a static site generator and have come across some interesting issues with a few of the standard [Grunt](http://gruntjs.com/) tools out there. We chose to go with [Metalsmith](http://www.metalsmith.io/) since it had all the features we needed, and was simple enough to integrate into many workflows. Some people use Metalsmith for their entire site build, but we wanted to use it for building out the core content while leveraging to great Grunt tools available for building JavaScript and SCSS.

## Usemin
One of the great tools in place with Grunt is [usemin](https://github.com/yeoman/grunt-usemin). This tool essentially does a search and replace inside of your html files for a special html comment syntax. You put something like this in your html:

``` html
<!-- build:js /js/site.js -->
<script type="text/javascript" src="/js/components/buttons.js"></script>
<script type="text/javascript" src="/js/foo/bar.js"></script>
<!-- endbuild -->
```

In development usemin doesn't do anything, letting you debug and write your code based on the original files. Then as part of your staging or production build you run the usemin (and hopefully useminPrepare) tasks and you end up with just this:

``` html
<script type="text/javascript" src="/js/site.js"></script>
```

The usemin task on its own will just do the search and replace, but useminPrepare is what makes the combo far more powerful. To make this clear without useminPrepare you would have to define some build steps that would result in `/js/site.js` being created and put into the right location. However useminPrepare will dynamically generate configuration for a [concat](https://github.com/gruntjs/grunt-contrib-concat) and [uglify](https://github.com/gruntjs/grunt-contrib-uglify) build step that result in the defined file (/js/site.js in this case) being created from the sources listed.

## Uncss
Another great tool is [uncss](https://github.com/giakki/uncss) which can be used as a [grunt task](https://github.com/addyosmani/grunt-uncss) too. This tool will generate a css stylesheet that only contains the styles used on a given set of pages. This is great, because it means I can have my cake and eat it too! I like using frameworks like [Bootstrap](http://getbootstrap.com/) or [Foundation](http://foundation.zurb.com/) as they're a great starting point. The downside to using these is that you pull in a lot of unnecessary bulk. This is where uncss shines. If you run uncss over your site you'll end up with a stylesheet that contains only the styles you've used. So in development everything is available to you, and then when you're ready to deploy anything you haven't used is pulled out.

## To Be Continued
These are some of the tools that have made the biggest impact on our build, but they didn't always get along. Keep your eyes out for Part 2 where I'll talk about some of the tricks we had to do to make these work together and how the filerev plugin fit into the mix.

## Update
[Part 2](http://blog.boushley.net/blog/2014/06/27/static-site-generators-in-a-single-page-applications-world-part-2/) is now out.
