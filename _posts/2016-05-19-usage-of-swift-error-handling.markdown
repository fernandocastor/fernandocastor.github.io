---
layout: post
comments: true
title:  "How two apps use Swift error handling: a prefatory, cursory examination"
date:   2016-05-19 23:59:00
categories: general
---

Proceeding on the subject of [my previous post](http://fernandocastor.github.io/general/2016/04/29/the-many-flavors-of-swift-error-handling.html), I've manually examined the usage of error handling constructs in two apps written in Swift. The first one is the iOS version of [Brave](https://www.brave.com/), a Web browser based on Firefox that automatically blocks ads and trackers. The second one is [Yep](https://soyep.com/), a social network app aiming to enable users to "find experts or other learners of a particular domain". Both apps are medium-sized, though appear to be above the [average iOS app](http://www.informationisbeautiful.net/visualizations/million-lines-of-code/), in terms of lines of code. Furthermore, both employ Swift's error handling constructs.

The main reason for me to conduct this examination is to know what constructs developers use and in what ways. For example, in Swift, [any type can be made into an error](http://fernandocastor.github.io/general/2016/04/29/the-many-flavors-of-swift-error-handling.html), but do developers use this in awkward ways, e.g., to throw ``Int``s? Moreover, ``catch`` blocks can be general, i.e., capture any error, or more specific, capturing errors by type or value. Which approach do developers seem to use more? Do developers still use ``NSError`` to signal errors even though Swift has a more sophisticated error signaling and handling mechanism? This kind of information can aid tool developers in designing tools to detect code smells (*why to throw an ``Int``?*), providing better code visualizations (*which errors get to this ``catch`` block?*), and making code recommendations (*replace this use of ``NSError`` by this more specific error type or value*).

The second reason for conducting this examination is my interest in [understanding the adoption of Swift](https://sites.google.com/a/cin.ufpe.br/castor/saner_2016_Swift_final.pdf?attredirects=0&d=1) itself. Error handling has only been introduced in Swift on version 2.0 of the language, one year after its unveiling, and it differs considerably from the well-established approach to report and handle errors in Objective-C. Thus, the adoption of this feature also tells a story about the adoption of the language itself and how developers are handling differences between Swift and its predecessor.

My (informal) methodology was the following. I used the Multi-file Search feature of [TextWrangler](http://www.barebones.com/products/textwrangler/) to search for occurrences of ``catch``, ``try?``, ``try!``, and other constructs. Then I went through each result found by TextWrangler and manually examined it to ascertain that it is actually a piece of code, instead of a comment or a text string. Finally, I recorded the number of occurrences of the construct and went to the next one. Since the counts were manual, (small) measurement errors are a possibility. Overall, I computed the following metrics:

* \# of ``catch`` blocks
* \# of generic ``catch`` blocks (not specifying type, value, or protocol)
* \# of uses of ``try!``
* \# of uses of ``try?``
* \# of uses of ``throws`` (and ``rethrows``) clauses
* \# of places that ``throw`` errors


The following tables present the results I have obtained for Brave and Yep, respectively. Metrics names are shortened but hopefully remain clear. Moreover, they were obtained from commits [e1213c2e72e99afe38814ed648f90360a498fd74](https://github.com/brave/browser-ios/commit/e1213c2e72e99afe38814ed648f90360a498fd74) (for Brave) and [a56298a15df790c78f55f9e2c7536b86ea69478e](https://github.com/CatchChat/Yep/commit/a56298a15df790c78f55f9e2c7536b86ea69478e) (for Yep).

| Metric             | Brave | Yep   |
|--------------------|------:|------:|
| lines of code      | 92855 | 61198 |
| ``catch``          |   147 |    25 |
|  generic ``catch`` |   121 |    25 |
| ``try?``           |    15 |   246 |
| ``try!``           |    78 |    25 |
| ``throws``         |    97 |     0 |
| ``throws``         |    33 |     0 |

It is possible to make a number of observations by looking at these numbers and also at the code from which they were extracted. First of all, clearly the vast majority of the ``catch`` blocks are generic. When we say "generic", we mean ones like the following, not specifying the value or type of the error, nor the protocol to which the error's type conforms:

{% highlight swift %}
do { ... }
catch { ... } // this is what we call a generic catch block  
{% endhighlight %}

We also consider a ``catch`` block to be generic if it includes the ``_`` character right after the ``catch`` keyword. In Swift, it is reasonable to expect a large number of ``catch`` blocks to be generic, since every ``catch`` block must include a generic clause, one that captures ``NSError``, or one that captures errors of types conforming to ``ErrorType``. The three approaches are equivalent in that they are capable of capturing any kind of error. In Brave, 121 out of the 144 ``catch`` blocks we identified are generic. Out of these, 27 use ``catch _``. In Yep, 25 out of 25 are generic. Moreover, for Brave, out of the remaining 26, one uses the ``where`` clause to capture the error only in case it is of type ``NSError`` and additional conditions hold. For the other 25 ``catch`` blocks, 4 capture specific error types of the application and the remaining ones target ``NSError``. It is interesting to note that the approach traditionally employed to present Swift's error handling mechanism in tutorials, to use an ``enum`` to define the various error cases, did not appear in any of the 172 ``catch`` blocks we examined in the two apps.

Second, it is commonplace in Swift, as in [other languages](http://pmarques.dei.uc.pt/papers/bcabral2007_ecoop.pdf), to use empty ``catch`` blocks or ``catch`` blocks that just print a message on the console, without even logging it properly. In Brave, 40 ``catch`` blocks are either empty or have only a comment. Many others just print a message. Also related to empty ``catch`` blocks is the use of the ``try?`` construct. As pointed out in Apple's [Swift error handling documentation](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ErrorHandling.html), this construct is just syntactic sugar:

{% highlight swift %}
let x = try? someThrowingFunction()

// the line above is equivalent to the following:
let y: Int?
do {
    y = try someThrowingFunction()
} catch {
    y = nil
}
{% endhighlight %}

Thus, even though ``try?`` is not equivalent to an empty ``catch`` block, all it does when an error is thrown by the invoked function is to return ``nil``. Brave uses ``try?`` 15 times, mostly in IO operations targeting files, database accesses and JSON processing. Yep, on the other hand, employs ``try?`` **246 times**, mostly together with IO-performing operations. Apparently, Yep uses error handling mostly to deal with errors stemming from library and framework functions that might potentially throw them. In other words, it does not employ Swift error handling to signal and handle application-specific errors. This is underlined by the absence of ``throws`` clauses and ``throw`` statements from its source code. Thus, ``try?`` is arguably being employed in this context mostly to silence the Swift compiler when invoking potentially error-throwing functions, similarly to what [Java developers do](http://pmarques.dei.uc.pt/papers/bcabral2007_ecoop.pdf). This impression is reinforced by the 124 times ``try?`` is employed using the pattern ``let _ = try? doSomething()``, i.e., the result of ``doSomething()`` will be ignored independently of whether an error occurred or not. We did notice, though, that ``try?`` is employed 73 times together with ``guard`` blocks, which are useful to fail fast, e.g., when the preconditions of a function are not met. This goes beyond empty ``catch`` blocks.
<<<<<<< HEAD

Third, Brave clearly uses Swift's error handling mechanism to report errors. Errors are thrown from 33 places in the code and 97 functions include ``throws`` clauses. It even includes a function that uses the  [``rethrows``](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Declarations.html#//apple_ref/doc/uid/TP40014097-CH34-ID531) clause, a special kind of ``throws`` indicating that the function takes a closure as an argument and all the errors it throws actually stem from that closure. All the thrown errors are either instances of ``NSError`` or values of a struct type ``Error`` defined by the application. As discussed before, errors of the latter type are rarely explicitly capture by ``catch`` blocks, since most of them either are generic or target ``NSError``. Yep differs from Brave in that it does not use Swift error handling to report errors. It does not throw a single error, nor has any function that ``throws``.

Finally, both apps make use of ``try!``, mostly associated with IO operations involving file or database access, regular expression parsing, and JSON processing. This construct produces a runtime error that causes the app to crash when an operation produces an error. Analyzing what kinds of errors can be produced by operations executed with this construct is left for future work.
=======
>>>>>>> 12b59c829a28f5edf5d9f112b70e81418102e37f
