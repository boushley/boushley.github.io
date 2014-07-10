---
layout: post
title: "IE Bug with Pseudo Elements and Nested Visibility"
date: 2014-07-09 17:22:59 -0700
comments: true
categories: css, IE, bugs
---
Recently I was working on adding some styling in an app and I was happy to discover that the
[CSS spec for visibility](http://www.w3.org/TR/CSS2/visufx.html#visibility) allows for nested elements to declare 
themselves visible.

    The generated box is invisible (fully transparent, nothing is drawn), but still affects layout. Furthermore,
    descendants of the element will be visible if they have 'visibility: visible'.

This came in handy, allowing me to make a container hidden until a specific element was focused, but then allowing me to 
make an icon within that container visible. As I was cleaning this up further I decided the icon really didn't have any 
semantic meaning so I shifted it into a CSS after pseudo element.

Things continued working and I was excited at how cleanly things had turned out. I had nice looking functionality 
without any javascript, and the html had stayed remarkably clean. That's when IE struck!

Turns out that IE supports pseudo elements fine, and it supports nested visibility just fine, but it doesn't support 
setting a pseudo element to visible when the element it is being added to is hidden. As far as I can tell from the 
[multiple](http://www.w3.org/TR/css3-selectors/#pseudo-elements)
[places](http://www.w3.org/TR/2009/CR-CSS2-20090908/selector.html#before-and-after)
[pseudo elements](http://www.w3.org/TR/2009/CR-CSS2-20090908/generate.html) are discussed in the specs they should be 
treated the same as any other child, and it appears that that is how Chrome, FireFox and Opera have interpretted the 
spec as well. However IE, even through IE 11, does not render this the same. I created
[this jsfiddle](http://jsfiddle.net/boushley/3d97K/4/) which demonstrates the behavior. In Chrome and FireFox you see 
the BEFORE and AFTER content, but in IE you only see the nested child content. I have some screenshots of this
[on browserstack](http://www.browserstack.com/screenshots/61b827cbc479d2c792e148e2065456abcf1a7f0d). There do appear to 
be
[some other people](http://stackoverflow.com/questions/17530947/ie10-visibilityvisible-on-before-pseudo-element-of-visibilityhidden-eleme)
that have run into this issue as well, but apparently not too many.

In the end I was able to work around this by making all of the children of my parent container `visibility: hidden;` and 
then specifically making the pseudo element visible. This didn't clutter my markup since I was working on a `<ul>` 
element already. So in the end I had a reasonable workaround with hiding all the children, but that wouldn't work in the 
scenario my jsfiddle outlines, since the text that is a direct child of the container element would not be hidden.

Oh IE, will you ever be consistent?
