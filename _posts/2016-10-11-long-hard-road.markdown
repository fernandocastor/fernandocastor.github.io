---
layout: post
comments: true
title:  "The Long Hard Road to Get Our Beautiful Paper Published"
date:   2016-10-11 17:10:00
categories: general
---
[CIn-UFPE](http://www.cin.ufpe.br) promotes every year an award ceremony to encourage its professors to publish papers in journals. This is important because, in Brazil, research-focused departments are evaluated mostly based on their output of published journal papers, even though that does not make a lot of sense for Computer Science departments. During the ceremony, some of the awarded faculty members make short presentations about papers they co-authored that were published in journals the previous year. This time around, I was invited to present one of mine. I immediately decided that I'd present the paper "A Large-scale Study on the Usage of Java's Concurrent Programming Constructs", but I would not go for the regular, technical kind of presentation. Instead, I'd talk about the long, hard road me and my coauthors had to tread from having an idea about an empirical study all the way until actually having the paper published.


# Setting up

Our story starts in the second semester of 2010. At that time, I had decided to change my research focus a bit and ride on the multicore wave. Concurrency is a subject that fascinated me since I was an undergrad, but up until that point I had not devoted much effort to studying it as a research topic. Being new to the area, the first question that came to my mind was: "What's the state of the practice in the development of concurrent software?". I had been reading a lot about high level concurrent and parallel programming abstractions and about alternatives to locking such as [software transactional memory](https://en.wikipedia.org/wiki/Software_transactional_memory). I had no idea, however, whether this kind of thing was actually used by developers and whether solutions proposed by researchers would be applicable to a wide range of real-world systems. In order to understand this, we had to look at real, complex software systems. I decided, together with some students of mine at the time, to take a look at [SourceForge](http://www.sourceforge.net). SourceForge has many end-user applications (instead of just developer tools and pet projects) and at the time [Github](http://www.github.com) still wasn't as popular as it currently is. The focus would be on Java, since it's a language with built-in support for concurrent programming from its initial releases. Moreover, we'd look at stable and mature projects (as indicated by the project owners) with more than 1000 lines of code.

Nowadays, that kind of study could be conducted much more easily with support from tools such as [Boa](http://boa.cs.iastate.edu/), [GHTorrent](http://ghtorrent.org/), and the [Github Archive](https://www.githubarchive.org/). That wasn't the case at the time. Obtaining the list of projects required crawling the SourceForge website. Moreover, since we wanted to analyze the source code of the projects, we did not want to run into the risk of using a broken or unfinished version. Therefore, we decided to aim just for releases, which could easily be identified because they are zipped files in project home pages at SourceForge. We built a simple infrastructure to perform all these tasks automatically. It took more than a week for it to crawl  through SourceForge, obtain the list of projects, download all the zip files, and unzip them. Even then, lots of manual filtering had to be performed, for example, to remove test files. After removing some projects, e.g., because we could not identify whether zip files corresponded to one or multiple versions, we ended up with **2227 projects**, amounting to more than **600 million lines of code**.


# Minor detail: summary of study results

Our approach consisted of collecting dozens of metrics pertaining to the usage of the concurrent programming constructs available in the Java language, .e.g, number of uses of ``synchronized`` blocks and number of classes implementing the ``Runnable``interface, and carrying out some simple statistical tests considering the most recent version of each project in isolation and also comparing the oldest version (from 2005 onwards, since 2005 was the year when the ``java.util.concurrent`` library was released) and the most recent one. Later we also considered some intermediate versions, when available. Since the goal of this post is not to discuss the results of the study, I'll just summarize a few of them below, considering the various iterations of the paper:

* More than 75% of the latest versions of the projects either explicitly create threads or employ some concurrency control mechanism;

* More than half of these projects exhibit at least 47 ``synchronized`` methods and 3 implementations of the ``Runnable`` interface per 100KLoC, which means that not only concurrent programming constructs are used often but they are also employed intensively;

* The adoption of the ``java.util.concurrent`` library is only moderate (approximately 23% of the concurrent projects employ it);

* Efficient and thread-safe data structures, such as ``ConcurrentHashMap``, are not yet widely used, despite the fact that they present numerous advantages.

* Usage of ``synchronized`` methods seems to be diminishing throughout the years whereas usage of ``synchronized`` blocks seem to be growing. This sounds particularly interesting because the latter are more flexible and support finer-grained concurrency control than the former.


# The first three rejections

We thought we already had enough material to write a paper about this study. These findings, albeit simple, were based on a large software population and, at the time, represented the best anyone knew at the time about the state of the practice of the usage of concurrent programming constructs in open source software.
We wrote a paper and submitted it to the 2011 edition of the [Brazilian Symposium on Programming Languages](http://cbsoft.org/sblp2016) (SBLP), an event I care deeply about in spite of its mostly local reach. I was fairly confident of the acceptance of the paper, considering the large amount of data and the (to me) obvious practical appeal. To our surprise, the paper was rejected. To this day, I still think this was an unfair rejection. I think even the PC chair thought the rejection was unfair, at least to some extent. So much so that he invited me to present the paper even though it would not be included in the proceedings.

Since we believed that this first rejection was **extremely unfair** and the **feedback not very good**, we prepared the paper for a new submission right after the SBLP result came out. We decided to submit to the [Symposium on Generative Programming and Component Engineering](http://2016.gpce.org/) (GPCE), even though it fell a little outside the scope of this symposium. We submitted and the paper, as expected, it got rejected, partly for not fitting within the scope. Some of the referee's comments hurt a little, like this one:

*"I think the effort is interesting, although the findings are generally weak, nothing deep or surprising."*

Overall, however, albeit hard, **the reviews were thoughtful, useful, and fair**. We did not think this submission was a waste of time, although it made us think that perhaps we still did not have a deep enough study to aim for a full-fledged symposium. Therefore, we decided to aim for a workshop. SPLASH 2011 had one called Workshop on [Transitioning to Multicore](http://splashcon.org/2011/program/workshops/148-tmc-transitioning-to-multicore) (TMC), where our paper would fit like a glove. We submitted, the paper got accepted and we presented it. With researchers such as Doug Lea as members of the audience, we left the workshop really enthusiastic. Each round of reviews made the paper stronger and the feedback from TMC was very positive.

We worked five more months on the paper and around March or April 2012 made a submission to the [IEEE International Conference on Software Maintenance](http://icsme2016.github.io/). Our rationale was that maybe we should give the paper a Software Engineering twist, highlighting its usefulness for reengineering efforts. Once again we had a rejection and it was not pretty. The first reviewer was fair, careful, and seemed to have an overall positive attitude towards the paper. The third one was hard to gauge, in terms of disposition. The second one, however, was infuriating. Just one sentence from the review:

*"Unfortunately, the discussion of actual implications of this research is generic and unconvincing."*

This comment came from a two-paragraph review that, IMO, was both generic and unconvincing. This is the kind of review that gets your paper rejected really fast. This result really hit our motivation for some time. By that time, more than one year had passed since our first submission of this work and we started to seriously doubt our results. Maybe the study was not as interesting as we initially thought. The thing is, the submission and peer-review process is hard for everyone. A researcher has to really believe his/her work in order to overcome the many challenges and I really believed in this work. Thus, after letting it rest for a few months, we decided to try again, this time aiming for a journal. The journal submission process, albeit requiring a lot more work, tends to be more rational since outright rejections based on unfair reviewers tend to be rarer. At least we hoped so.


# A journal submission

Our first submission for [Software: Practice & Experience](http://www.wiley.com/WileyCDA/WileyTitle/productCd-SPE.html) (SPE) was in the beginning of 2013. By that time, the paper had grown to be at least twice as big as our initial submission in 2011, to SBLP. Almost three months later, we received the result: **Accepted after Major Revision**. In other words, we'd have to work hard but, if we did so, there was a very high probability that the paper would actually be published. Two of the reviewers had a clearly positive attitude towards the paper. The third one was less so and required some changes that we considered were not within the scope of the study we presented. We focused strongly on source code and on performing analyses on a large scale. This would not be possible if we decided to study informal documentation, such as bug reports, although that was precisely what that reviewer asked us to do, among other things.

For months we worked on improving every aspect of the paper. Besides polishing, we conducted a survey with more than 100 developers to better contrast their expectations with what we actually found. We also made our analysis deeper by using multiple releases of the projects, instead of just the first and the last ones (between 2005 and 2012). The paper grew more than 30% when compared to the initial submission and we were confident and proud of the result. To our surprise, instead of the final paper acceptance, we were asked to perform another round of reviews. Two of the reviewers just accepted the paper but the third one actually changed his/her decision from Accept after Major Revision to **Reject** and emphasized the need for us to base part of our study on a 3-page extended abstract published on a doctoral symposium. After a few moments of outrage, we sent the editor an email informing that some of the comments of said reviewer were utterly and entirely unreasonable and we would **not** perform those modifications. The editor then asked us to work on the paper and submit it again and he would find a fourth reviewer. We resubmitted. In three months, the decision arrived by email: **Rejected**, due to the fourth reviewer's recommendation. Ironically, the adversarial reviewer who stopped the paper from being accepted had, at that point, changed his recommendation to **Accept**, which didn't make any difference.

![ANGRY!!!!](https://raw.githubusercontent.com/fernandocastor/fernandocastor.github.io/master/images/bambam.gif)


# A moment of reflection

Based on the SPE result, once again I had to revisit my assumptions. Was this work as interesting as I had initially envisioned? In 2010, when it all started, maybe due to my naivety, I was pretty sure. However, we had reached 2014 and the paper still hadn't been published. The landscape changes a lot in four years in the area of Computer Science and one has the obligation to ask him/herself whether a piece of work is still relevant and gauge its value to the scientific community. I reflected for a few days and, one again, reached the conclusion that we had a relevant piece of work in our hands. We still could not find any paper in the literature with a scope as broad as ours an a specific focus on concurrent programming constructs. This led me to once more rally my students and a colleague at UFPE to make a new submission.


# Acceptance

We submitted to [The Journal of Systems and Software](http://www.journals.elsevier.com/journal-of-systems-and-software/) in the first months of 2014. In two months, the result arrived: **Revision** (it was not clear whether it was a major or a minor one). We were so oversensitive to reviewer criticism that we took every comment as a potential threat to the acceptance of the paper. My colleague at the time commented that, based on his experience, the reviewers seemed to have liked the paper very much. Nonetheless, I told him we would not take any chances. *Six months* of hard work addressing every reviewer comment from every possible angle later, we re-submitted. In less than two months, the result arrived: **Accepted**


![We like to party!](http://i.makeagif.com/media/7-21-2015/6IKtMa.gif)

We were obviously delighted with the results. It was the crowning of years of hard work, frustration, and an unwavering belief we were working towards a worthy goal. Ok, our belief sometimes wavered, but not for long.


# Epilogue

Now that the paper is published for more than a year, I can still say that I am proud of the results and happy that they have finally reached the academic community at large. The experience was humbling and a great learning opportunity, although at the cost of a large load of (at least partially unnecessary) frustration. Beyond the overall positive result and the obvious hurt feelings, we also lost the timing of the publication. A couple of papers published while we were fighting to get ours published actually reduced (though didn't eliminate) the relevance of our work. Timing is very important in science.

I wish I had interesting and thoughtful takeaway messages about our experience, but I don't have any. Believing in your work is a basic assumption if you want to follow a career in science, since there are so many hurdles to make your scientific results get published. We learn from the very early stages that **if we don't believe in our own work, no one will**. Also, being critical of your work, besides being a given, will not save you from adversarial, overworked, or lazy reviewers. There's a **disturbing percentage of luck involved in getting your paper accepted**. Even though we had very bad luck with our first journal submission, conferences emphasize this luck factor even more strongly. One thing I would have done differently, however, is to **publish the non-peer-reviewed version of the paper in arXiv or PeerJ Preprints**. At least in this manner the paper would be accessible to interested researchers earlier.

But hey, I'm not complaining. **Our paper is beautiful** and you should definitely **read it** ([here](http://www.sciencedirect.com/science/article/pii/S0164121215000849) or [here](https://sites.google.com/a/cin.ufpe.br/castor/Pinto_2015_LSS_JSS.pdf?attredirects=0&d=1), preprint, without the paywall).
