---
layout: post
title: "Experimenting with ECMAScript 6 Default Function Parameters"
date: 2015-09-19 19:03:52 -0700
comments: true
categories:
    - ES6
    - js
    - Experiments
    - Spec Diving
---

## Where We're At Now

ECMAScript 6 (ES6) added the ability to specify default parameters to functions, but we've
got a lot of code out there that already handles defaults, heck we even have a colloquially
named *default operator* `||`;

Here's the way I used to handle default parameters.

{% highlight js %}
function foo(a) {
    a = a || 5;
    console.log(a);
}
{% endhighlight %}

This works in most cases but gets problematic when [falsy values](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)
are acceptable inputs. Then you need to explicitly check for `undefined` or check `arguments.length`;

## Things Get Better

The new ES6 syntax looks like this:

{% highlight js %}
function foo(a=5) {
    console.log(a);
}
{% endhighlight %}

Much cleaner, especially as you add more parameters with their own defaults. However this just left me wondering about
how far you could take these default parameters. Are they bound when the function is defined, or when it is called? What
kind of expressions can you set as the value?

## ES6 Today

So the first place I looked was the [Babel](http://babeljs.io/) [REPL](http://babeljs.io/repl/). This is a great tool
that lets you see how the ES6 version of some code would be written in ES5. This is great for helping you understand how
things work, especially since the Babel team works extremely hard to produce javascript that follows the specification
exactly.

If we drop our simple function into the REPL we get this as the output:

{% highlight js %}
function foo() {
    var a = arguments.length <= 0 || arguments[0] === undefined ? 5 : arguments[0];

    console.log(a);
}
{% endhighlight %}

That looks promising. This answered my first question, parameters are bound when the function is called, not when it is
defined. That's super handy. This also handles falsy values porperly, good stuff.

The way `5` is just sitting in there made me think we might be able to do a fairly complex expression here. So I tried
popping a function call or variable assignment in there. Things worked great. You  can call a function, you can set the
parameter to another variable, heck you can set a later parameter to the value of an earlier parameter:

{% highlight js %}
function foo(a=5, b=a) {
    console.log(a, b);
}
{% endhighlight %}

### Side Note
> Babel has an additional mode called `High Compliancy`. This adds some extra noise to the above example to handle 
> temporal dead zones. Luckily we don't have to worry about that with these examples.


I also noticed that FireFox already has default parameters implemented. So I opened up FireFox's developer console and
did similar tests. Everything worked just as you would expect based on the Babel code.

## The Spec

Just to be sure there wasn't a bug in here I went ahead and dove into the [ES6 Spec for Function
Definitions](http://www.ecma-international.org/ecma-262/6.0/#sec-function-definitions). It looks like FireFox and
Babel got it right. Default parameters are late binding and allow for basically any expression.

I'm glad we have Babel so we can use these great features now.
