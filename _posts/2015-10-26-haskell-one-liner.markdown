---
layout: post
title:  "Haskell one-liner"
date:   2015-10-26 11:39:06
categories: general
---
One of these days, [@1HaskellADay](http://www.twitter.com/1haskelladay) posted the following programming challenge: 

*#1Liner You're given either fst or snd, but don't know which. Define a function that returns its dual: dual :: ((a,a) -> a) -> ((a,a) -> a)*

My answer to the challenge was fairly straightforward: 

{% highlight haskell %}
dual f = \(x,y) -> f (y, x) 
{% endhighlight %}

It's not bad, but nothing compared to [Francisco Soares](http://www.twitter.com/frsoares)' 

{% highlight haskell %}
 dual = uncurry.flip.curry
{% endhighlight %}

This is how real Haskell programmers do it. Those that [do not eat quiche](http://www.bernstein-plus-sons.com/RPDEQ.html "Real programmers don't eat quiche").