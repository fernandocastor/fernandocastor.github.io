---
layout: post
comments: true
title:  "Good Software Engineering research venues"
date:   2016-07-22 14:10:06
categories: general
---
A week ago, [Google](http://www.google.com) released its [2016 Google Scholar Metrics](https://googlescholar.blogspot.com.br/2016/07/2016-scholar-metrics-released_14.html) report. This report is sort of an interactive best-of of scientific publication venues. If you go to its [**Top Venues** page](https://scholar.google.com/citations?view_op=top_venues), you'll see publication venues that are know to almost everyone, not just scientists, such as [Nature](http://www.nature.com), [The Lancet](http://www.thelancet.com), [Science](http://www.sciencemag.com), etc. Publications are ranked based on their H5-index. The [H-index](https://en.wikipedia.org/wiki/H-index) is a well-known measure of productivity and scientific impact that can be applied to both individuals and publication venues. Basically, the H-index *N* of an individual or publication venue is its number *N* of publications that have at least *N* citations. The H5-index is thus the H-index applied only to the last 5 years and it is a measure of **recent impact**.

In my opinion, one of the most interesting feature of Scholar Metrics for scientists is that it is searchable in a simple, flexible way. In this manner, it becomes possible to narrow down search to a very specific area, in order to discover the most relevant venues for that area. I decided to do just that for my research area, Software Engineering. I started out with a simple string, "Software Engineering". The problem with that string is that it excludes venues that do not include "Engineering" in their names, such as [IEEE Software](https://www.computer.org/software-magazine/) and the [Journal of Systems and Software](http://www.journals.elsevier.com/journal-of-systems-and-software/). I decided to be more liberal and use just "Software". This worked out better, but brought a number of spurious results on subjects such as "Statistical Software", "Mathematical Software", and "Software Encryption". These topics are not really Software Engineering and needed to be excluded manually. In the end, I used the following search string:

{% highlight c %}
software -encryption -mathematical -statistical -interface -advances -performance -environmental
{% endhighlight %}

Which yielded the following list of conferences and journals:

[![Top 20 Software Engineering venues](https://raw.githubusercontent.com/fernandocastor/fernandocastor.github.io/master/images/vanues.png "https://scholar.google.com.br/citations?hl=en&view_op=search_venues&vq=software+-encryption+-mathematical+-statistical+-interface+-advances+-performance+-environmental")](https://scholar.google.com.br/citations?hl=en&view_op=search_venues&vq=software+-encryption+-mathematical+-statistical+-interface+-advances+-performance+-environmental)

This is a good list of publication venues in the area of software engineering. It is not perfect, but it is very useful, particularly for new researchers who are wondering about where to publish their work. Its most evident problem is that it does not account for programming language venues. This may be a problem or not, depending on one's line of work. If we assume that most good PL venues include the work "Programming" on their titles (POPL, OOPSLA, PLDI, PPoPP, ASPLOS, ICFP meet this criterium), it is only a matter of modifying our search string slightly:

{% highlight c %}
(software|programming) -encryption -mathematical -statistical -interface -advances -performance -environmental
{% endhighlight %}

You can check the list produced by this search string [here](https://scholar.google.com.br/citations?hl=en&view_op=search_venues&vq=%28software%7Cprogramming%29+-encryption+-mathematical+-statistical+-interface+-advances+-performance+-environmental). It includes most of the usual suspects, with a few notable omissions (*O ECOOP, where art thou?*).
