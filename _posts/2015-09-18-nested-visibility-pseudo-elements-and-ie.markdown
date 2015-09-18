---
layout: post
title: "Fun with visibility and pseudo elements, and then IE"
date: 2015-09-18 19:03:52 -0700
comments: true
categories:
    - IE
    - CSS
---

## Fun with Visbility

What happens with nesting and visibility?

By default when you make an element `visible: hidden;` it becomes *hidden* along with all of
the elements children. It doesn't have to stop there though:

### Hidden Parent and Visible Child

If you make an element explicitly visible it shows up, regardless of an ancestor elements visibility setting.

The [specification for visbility](http://www.w3.org/TR/CSS2/visufx.html#visibility) explicitly calls out that (emphasis
added):

> The generated box is invisible (fully transparent, nothing is drawn), but still affects layout.
> __Furthermore, descendants of the element will be visible if they have 'visibility: visible'.__

So, lets see the basic setup:

{% highlight html %}
<div class="hidden-parent">
  <div class="visible-child">
  </div>
</div>
{% endhighlight %}

{% highlight css %}
.hidden-parent {
  visibility: hidden;

  background-color: red;
  height: 200px;
  width: 200px;
}

.visible-child {
  visibility: initial;

  background-color: blue;
  height: 100px;
  width: 100px;
}
{% endhighlight %}

You can see the *glorious* result here:

<p data-height="200" data-theme-id="0" data-slug-hash="QjNoZj" data-default-tab="result" data-user="boushley" class='codepen'>See the Pen <a href='http://codepen.io/boushley/pen/QjNoZj/'>QjNoZj</a> by Aaron Boushley (<a href='http://codepen.io/boushley'>@boushley</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

This works in any browser you're likely to work with. It is a cool tool, not something
I use all that often, but it is a nice tool to have around.

### Hidden Parent and Pseudo Elements

Alright, so what happens if we want to use a [pseudo element](http://www.w3.org/TR/2011/REC-CSS2-20110607/generate.html#before-after-content)
like `::before`.

{% highlight html %}
<div class="hidden-parent">
</div>
{% endhighlight %}

{% highlight css %}
.hidden-parent {
  visibility: hidden;

  background-color: red;
  height: 200px;
  width: 200px;
}

.hidden-parent::before {
  visibility: visible;

  content: " ";
  display: block;
  background-color: blue;
  height: 100px;
  width: 100px;
}
{% endhighlight %}

And again, the result:

<p data-height="200" data-theme-id="0" data-slug-hash="WQwmBr" data-default-tab="result" data-user="boushley" class='codepen'>See the Pen <a href='http://codepen.io/boushley/pen/WQwmBr/'>WQwmBr</a> by Aaron Boushley (<a href='http://codepen.io/boushley'>@boushley</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

Works great, well for most of you it worked great. Unfortunately if you're in IE it doesn't work. I would show you a
screenshot, but it's just blank. This holds for all IE versions I've been able to test, all the way up through IE 11.

This one is a bummer, I was really excited when it worked so well in Chrome and FireFox, but IE dashed my hopes.

### Who Cares?
This may all seem really theoretical, but I discovered this as I was trying to build out an error display. There was an
error icon, the before pseudo element, and the error contents, the content of the `.hidden-parent`. The ask from the
designers was to have the messages hidden until certain interactions happened. I was able to get this working using just
html and css without the need for an additional element for the error display.

You can see a rough version of it here. Works great, but to get IE support you need to avoid using the pseudo element.

<p data-height="268" data-theme-id="0" data-slug-hash="ZbWPgy" data-default-tab="result" data-user="boushley" class='codepen'>See the Pen <a href='http://codepen.io/boushley/pen/ZbWPgy/'>ZbWPgy</a> by Aaron Boushley (<a href='http://codepen.io/boushley'>@boushley</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>
