---
layout: post
comments: true
title:  "A Tipsy, Unscientific, and Biased Survey on the Adoption of Refactoring Tools Available in IDEs by Software Engineering Researchers"
date:   2023-06-05 16:21:00
categories: research
---

Last Friday I participated in my first [SEN symposium](https://www.sen-symposium.nl/), which gathers Software Engineering researchers from all across the Netherlands. I was one of the organizers, together with [Gemma Catalino](https://www.gemmacatolino.com/), [Sebastian Junges](https://sjunges.github.io/), [Jurgen Vinju](https://www.cwi.nl/en/people/jurgen-vinju/), [Tijs van der Storm](https://www.rug.nl/staff/t.van.der.storm/), and [Negar Alizadeh](https://www.uu.nl/staff/NSAlizadeh). The symposium had [two keynotes and three invited speakers](https://www.sen-symposium.nl/speakers/) in diverse topics such as refactoring, code comprehension, security, academic (and industry) career, and modeling and construction of robots, in addition to a dozen lightning talks. I really enjoyed the experience; I found the community very welcoming and interested in discussing diverse research topics. 

After the symposium there was a reception with some drinks that was attended by quite a few participants. In that reception I talked to one of the invited speakers, [Bin Lin](https://binlin.info/), who gave a very interesting presentation on the landscape of research on refactoring. During the conversation, we discussed the abundance of research on this topic and the apparent contradiction that developers don't think too much about it. They do perform refactoring, but usually as part of some other software development activity that takes the front stage. In particular I was under the impression that developers don't care too much about the automated refactoring functionality that contemporary IDEs make available, e.g., the *"Refactor"* menu is tools such as Eclipse and VS Code. He agreed with me, which somewhat surprised me since he is a very active researcher in this area. This then prompted me to ask him whether he employs any automated refactoring tools in his day-to-day coding activities, to which he responded that no, he doesn't. 

Personally I have been using automated refactoring tools for more than 20 years, at least for statically typed languages. I started in 2001, when Martin Fowler wrote an article called [*"Crossing Refactoring's Rubicon"*] (https://martinfowler.com/articles/refactoringRubicon.html). It describes two tools, X-ref and jFactor. At the time I was working with program transformations and particularly interested in describing refectories in a generalized manner. Motivated by that, I ran some tests with jFactor to study how well it preserved behavior and noticed that it committed some mistakes with corner cases, although it worked fine most of the time. Nowadays, when writing Java or Swift code, I often employ automated refactoring although, for dynamically typed languages, I don't trust the tools very much because there is so little information available for them to guarantee behavior preservation.

![Is there a Ballmer peak for research?](https://raw.githubusercontent.com/fernandocastor/fernandocastor.github.io/master/images/ballmer_peak.png)

Bin's response surprised me and, after a couple of glasses of wine, my researcher curiosity got activated and I decided to gather a bit more data about whether it is a common viewpoint. My sample would be the completely biased group of people present in that reception, which consisted almost entirely of Software Engineering researchers, many of whom conducted work on refactoring (myself included). I talked to 33 people overall, asking them if they made use of refactorings such as renaming and method extraction made available by existing IDEs. I also asked them about languages in which they performed refactorings and which refactorings they used. The data was unreliably recorded by me on the note-taking app on my phone.

Out of the 33 individuals with whom I talked, 19 said unequivocally that they do not use automated refactoring, 13 do, and one stayed on the fence, i.e., they sometimes do but wouldn't say they are regular users. Among the folks who use automated refactoring, 10 use only renaming. When asked about why, the reason **all** of them raised is that **they don't trust the tools to perform other, more complicated refactorings**. The 19 who do not use these tools also do not trust them to guarantee behavior preservation. Furthermore, only a couple use automated refactoring in dynamically-typed languages (one for Python, the other for PHP). One of the respondents, a well-known researcher in the area of metaprogramming, gave me an elaborate answer about why they use refactoring for PHP (they know how the refactorings are implemented and thus that they mostly work) but not so much for Java (existing robust implementations are very inefficient and the efficient ones are unreliable). More specifically, existing implementations of automated refactoring fail to take concurrency and the particularities of the Java Memory Model into account. I also heard from a few (two or three) Software Engineering researchers who seem themselves more as data scientists than developers that they don't care about code quality and therefore **don't care about refactoring at all**. 

A potential threat to the validity of this already completely unreliable survey is that researchers are not developers and don't write a lot of code. That is potentially true, but in our sample most do, at least occasionally. Furthermore, some of the respondents actually do write code as one of their main activities, as they are affiliated with a research institute that provides service to many large companies. 

Refactoring is a pretty popular research topic in our community. Every year it is possible to find some papers on the topic in conferences such as ICSE, FSE, SANER, ICSME, and ASE. There are papers on very diverse topics such as recommending refactorings [1,2], how developers use refactoring [3,4], what they think about refactoring [10], refactoring to improve quality attributes other than maintainability [2,6], code smells [5], and many more. When I was a young researcher, the implementation of refactoring was also a topic of great interest for researchers [7,8]. Nowadays, though, I think this topic has become less sexy and I don't see a lot of papers trying to get refactoring working efficiently, as if the problem had already been solved. However, the results of this survey suggest that it hasn't and and there is room for improvement. There is debate about whether the efficiency arguably promoted by refactoring tools is more important than behavior preservation or not [9]. I'd like to hear what researchers and developers think about this and, if behavior preservation is impossible to achieve in a generalized manner, whether there is an acceptable trade-off. 


## References

[1] Gabriele Bavota, Andrea De Lucia, Andrian Marcus, Rocco Oliveto: Recommending Refactoring Operations in Large Software Systems. Recommendation Systems in Software Engineering 2014: 387-419

[2] Wellington Oliveira, Renato Oliveira, Fernando Castor, Gustavo Pinto, João Paulo Fernandes: Improving energy-efficiency by recommending Java collections. Empir. Softw. Eng. 26(3): 55 (2021)

[3] Emerson R. Murphy-Hill, Chris Parnin, Andrew P. Black: How We Refactor, and How We Know It. IEEE Trans. Software Eng. 38(1): 5-18 (2012)

[4] Jevgenija Pantiuchina, Bin Lin, Fiorella Zampetti, Massimiliano Di Penta, Michele Lanza, Gabriele Bavota: Why Do Developers Reject Refactorings in Open-Source Projects? ACM Trans. Softw. Eng. Methodol. 31(2): 23:1-23:23 (2022)

[5] Willian Nalepa Oizumi, Alessandro F. Garcia, Leonardo da Silva Sousa, Bruno Barbieri Pontes Cafeo, Yixue Zhao:
Code anomalies flock together: exploring code anomaly agglomerations for locating design problems. ICSE 2016: 440-451

[6] Yu Lin, Cosmin Radoi, Danny Dig: Retrofitting concurrency for Android applications through refactoring. SIGSOFT FSE 2014: 341-352

[7] Don Roberts, John Brant, Ralph E. Johnson: A Refactoring Tool for Smalltalk. Theory Pract. Object Syst. 3(4): 253-263 (1997)

[8] Hannes Kegel, Friedrich Steimann: Systematically refactoring inheritance to delegation in java. ICSE 2008: 431-440

[9] John Brant, Friedrich Steimann: Refactoring Tools are Trustworthy Enough and Trust Must be Earned. IEEE Softw. 32(6): 80-83 (2015)

[10] Miryung Kim, Thomas Zimmermann, Nachiappan Nagappan: An Empirical Study of RefactoringChallenges and Benefits at Microsoft. IEEE Trans. Software Eng. 40(7): 633-649 (2014)