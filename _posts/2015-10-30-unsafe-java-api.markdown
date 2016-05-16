---
layout: post
comments: true
title:  "Two very interesting empirical studies presented at SPLASH'2015"
date:   2015-10-30 14:54:06
categories: general
---


Today at [SPLASH](http://www.splashcon.com), there was an OOPSLA session on empirical studies. All the four papers of the session were very interesting and well-presented. Two of them caught my attention because they are more related to work I have conducted in the past [[1](https://sites.google.com/a/cin.ufpe.br/castor/Pinto_2015_LSS_JSS.pdf?attredirects=0&d=1),[2](https://sites.google.com/a/cin.ufpe.br/castor/msr_2015_submitted.pdf?attredirects=0&d=1),[3](https://sites.google.com/a/cin.ufpe.br/castor/msr_2014_cr.pdf?attredirects=0&d=1),[4](https://sites.google.com/a/cin.ufpe.br/castor/Ebert_2015_ESE_JSS.pdf?attredirects=0&d=1)]. I discuss them below.

[![SPLASH'2015 logo](http://www.dynamic-languages-symposium.org/dls-15/media/splash15_logo.png "2015.splashcon.org")](http://2015.splashcon.org)


####[How Scale Affects Structure in Java Programs](http://dl.acm.org/citation.cfm?id=2814300&CFID=555774425&CFTOKEN=10047354) by Crista Lopes, Joel Ossher

I was already familiar with this paper, having discussed it with some students in [class](http://twiki.cin.ufpe.br/twiki/bin/view/TAES/TAES2201502) a couple of weeks ago. It is a very interesting piece of work showing how the size of a software system is correlated with certain characteristics (nothing new there) but also showing that **this relationship changes as the size of a system grows** (previously an **unverified** hypothesis, at least on a large scale study). This is strongly related to the 1975 [paper](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=0CCgQFjABahUKEwjAmfvx8-rIAhWF1h4KHZCdAvY&url=http%3A%2F%2Fwww.cs.umd.edu%2Fclass%2Fspring2005%2Fcmsc838p%2FGeneral%2Fpitl.pdf&usg=AFQjCNH4ihfX6lE2HK9tVhJUm9-7lIEG4g&sig2=A_ORzeazyaS54QTxN63rbg) by DeRemer and Kron. The first sentence of this classifical paper summarizes its ideas very well: *"We distinguish the activity of writing large programs from that of writing small ones"*. Lopes and Ossher's study provides a considerable amount of data showing that this idea is verifiable.

I also like how the paper emphasizes the importance of **studying non-linear relationships** between variables pertaining to programs. For example, the study reports that the number of lines of code (LoC) in a program grows exponentially with respect to the number of modules (classes + interfaces), even though the exponent is small (1.0939). Thus as the paper itself explains, if a program with 10 modules has 400 LoC, we can expect a program with 100 modules to have 4965 LoC, instead of just 4000. Of course, this is a general trend and not something that will be observed for every program. Nonetheless, the observed data comprises more than 30.000 Java projects. This number suggests that the findings are robust and not likely to stem from chance.

It is important to point out that the paper only investigated one **software population**, that of Java projects hosted on Google Code until 2012. This does not necessarily reflect the characteristics of populations from other forges or based on different programming languages. Moreover, the study did not investigate the temporal dimension of these relationships. These are great news for researchers interested in this topic!


####[Use at Your Own Risk: The Java Unsafe API in the Wild](http://dl.acm.org/citation.cfm?id=2814313&CFID=555774425&CFTOKEN=10047354) by Luis Mastrangelo, Luca Ponzanelli, Andrea Mocci, Michele Lanza, Matthias Hauswirth, Nathaniel Nystrom

This paper investigates one of those not-well-known, dirty little secrets. Java is type safe (and [type sound](http://research.microsoft.com/apps/pubs/default.aspx?id=68466)) and memory safe. This means that problems that stop an application from proceeding at runtime should be signaled as ``Exception``s or, in the worst case scenario (e.g., due to lack of heap space), as an ``Error``. Thus, in theory, one should never be able to crash the JVM. However, Java has this API, ``sun.misc.Unsafe`` which, as the name implies, is not safe. This means that it is capable of performing operations that violate Java's safety guarantees. Operations that allow one, for example, to affect memory alignment and to allocate memory without initializing an object.

The study of Mastrangelo and colleagues was exploratory in nature. It aimed to assess the extent to which ``Unsafe`` is used and how it is used. For that end, they used Apache's Maven Central Repository as the data source. This repository has more than 950,000 artifacts (the results of builds). Considering only the latest versions of the artifacts and filtering out the undesired ones, they ended up with a corpus of 86479 artifacts amounting to 74GB of data. Among these artifacts, 817 use ``Unsafe``, less than 1%. This low number led the authors to investigate whether these artifacts are widely used or if they are isolated pieces of software. As it turns out, large-scale, well-known projects employ them. Notable examples include Scala, Ruby, Spring, Hibernate, Grails, Stream, Cassandra, Hadoop, Akka, Storm, and Neo4J. In their corpus, 21,297 artifacts indirectly use ``Unsafe``, almost 25% of all the analyzed artifacts. It is not clear to me whether "indirectly" means transitively or with just one level of indirection. I'll have to check that out in the paper later.

Proceeding with the analysis, they investigated which features of ``Unsafe`` are often used and why. Examples include instructions to (i) query the memory layout; (ii) put and get elements in specific memory locations, e.g., in the heap; and (iii) compare-and-set instructions, which are very important for concurrent applications. These instructions are often used to improve performance. The authors documented 14 usage patterns, including processing of byte arrays in block, the aforementioned atomic operations and, amazingly, **updates to ``final`` fields**! The authors also analyzed questions that developers ask about ``Unsafe`` at [StackOverflow](http://www.stackoverflow.com). There don't seem to be many questions on the topic, which is not surprising. The authors found out that lack of documentation is an important problem, though.

This left me (and at least another member of the audience, it seems) wondering about which parts of ``Unsafe`` are unsafe-r. The authors did not go that far, however. I'm not sure there is enough data to conduct that study yet.

####The bottom line

Both studies raise new avenues for research, as I have discussed previously. In particular, for the first study, it would be interesting to (i) replicate it with different populations; (ii) examine the temporal dimension and how the discovered relationships change throughout system versions; (iii) examine different attributes, e.g., usage of constructs related to concurrency or testing. For the second one, I can imagine a number of controlled experiments focusing on the usage of ``Unsafe`` features and examination of bug reports (for a large number of systems) in an attempt to identify the unsafe-st parts of the API.
