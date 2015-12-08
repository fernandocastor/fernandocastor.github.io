---
layout: post
title:  "The most popular questions about concurrency on StackOverflow"
date:   2015-10-28 11:39:06
categories: concurrency
---
We (mainly [Gustavo Pinto](http://www.gustavopinto.org), with a little help from [myself](https://sites.google.com/a/cin.ufpe.br/castor/) and [Weslley Torres](https://twitter.com/weslleytorres)) have conducted a study on the most popular questions about concurrent programming on [StackOverflow](http://www.stackoverflow.com). Our goal with this study was to understand the practical problems faced by software developers when using concurrent programming abstractions. These are the 10 most popular questions:

1. [Q588866](http://stackoverflow.com/questions/588866/) "What's the difference between the atomic and nonatomic attributes?"

2. [Q541487](http://stackoverflow.com/questions/541487/) "'implements Runnable' vs. 'extends Thread'"

3. [Q6319146](http://stackoverflow.com/questions/6319146/) "C++11 introduced a standardized memory model. What does it mean? And how is it going to affect C++ programming?"

4. [Q200469](http://stackoverflow.com/questions/200469/) "What is the difference between a process and a thread"

5. [Q661561](http://stackoverflow.com/questions/661561/) "How to update the GUI from another thread in C#?"

6. [Q1036754](http://stackoverflow.com/questions/1036754/) "Difference between wait() and sleep()"

7. [Q62814](http://stackoverflow.com/questions/62814/) "Difference between binary semaphore and mutex"

8. [Q817856](http://stackoverflow.com/questions/817856/) "When and how should I use a ThreadLocal variable?"

9. [Q510632](http://stackoverflow.com/questions/510632/) "What's the difference between ConcurrentHashMap and Collections.synchronizedMap(Map)?"

10. [Q323972](http://stackoverflow.com/questions/323972/) "Is there any way to kill a Thread in Python?"

Among the top 10, we have questions specific to Objective-C (1), Java (2,6,9), C++ (3), C# (5), and Python (10). There are also a couple of questions asking about general concepts (3,7) and one that may be thought of as language specific or not (8). 


What are concurrency-related questions?
---------------------------------------
We used the same keywords as the well-known [study](http://dl.acm.org/citation.cfm?id=1346323) by Shan Lu and colleagues to select the questions based on the tags associated to them: "race(s)", "deadlock(s)", "livelock(s)", "concurrency", "lock(s)", "mutex(es)", "atomic", "compete(s)", and "multithreading".

This produced a list with more than 22000 questions. We then ranked them by popularity, extracted only the 250 most popular, and manually inspected those to ascertain that they were actually concurrency-related. We ended up with 231 questions.


How did we measure popularity?
------------------------------
To calculate the popularity of each question, we obtained, for each one: (i) number of up-votes; (ii) number of times the question is marked as favorite; (iii) number of views; (iv) number of comments; and (v) and number of answers. Each of these metrics was then normalized with respect to the average value considering all the questions in StackOverflow. The **P** popularity measure is the result of calculating the geometric mean of these normalized metrics.


Summary
-------
Most of the questions pertained to either Java (77) or C# (60). Also, 28 pertained to mobile computing, focusing on one of the most popular platforms: iOS (6), Android (7), and Windows Phone (15).

We classified the questions in terms of 7 categories:  

+ Theoretical Concepts (59)
  + Q34510 "What is a race condition?"
  + Q588866 "What’s the difference between the atomic and nonatomic attributes?"
  + Q200469 "What is the difference between a process and a thread?"

+ Practical Concepts  (51)
  + Q817856 "When and how should I use a ThreadLocal variable?"
  + Q154551 "Which [synchronization] approach should be used [to increment an int]?"
  + Q251391 "Why is lock(this) {. . . } bad?"

+ First Steps (29)
  + Q2846653 "Trying to find a simple example that clearly shows a single task being divided for multi-threading."
  + Q3360555 "How to pass parameters to ThreadStart method in Thread?"
  + Q2734025 "Is javascript guaranteed to be single-threaded?"

+ Thread Life-Cycle (18)
  + Q1520887 "How to pause / sleep thread or process in Android?"
  + Q323972 "Is there any way to kill a Thread in Python?"
  + Q289434 "How to make a Java thread wait for another thread’s output?"

+ Technical Problems (53)
  + Q661561 "How to update the GUI from another thread in C#?"
  + Q530211 "[How to] create a blocking Queue<T> in .NET?"
  + Q16159203 "Why does this Java program terminate despite that apparently it shouldn’t (and didn’t)?"

+ Concurrent Lib/Framework (15 Q)
  + Q3629784 "How is Node.js inherently faster when it still relies on Threads internally?"
  + Q6916385 "Is there a concurrent List in Java’s JDK?"
  + Q3847108 "What is the Haskell response to Node.js?

+ Correctness (10 Q)
  + Q9666 "Is accessing a variable in C# an atomic operation?"
  + Q7095 "Is the C# constructor thread safe?"
  + Q680097 "I’ve heard i++ isn’t thread safe, is ++i thread-safe?"

Among all the questions, only one pertained to GPUs and not a single one asked specifically about improving performance, which is surprising. I could say that we learned three main things from this study: 

* Developers do not understand the problems that existing tools report about concurrency errors, because they often do not understand the errors themselves. Error reports that provide actual code examples have a stronger potential to be useful (see the third point below)
* Tools that help developers to build the software right, instead of trying to right the system after it's built are useful. For example, a simple tool that indicates to developers which parts of the code execute atomically is straightforward to build and could be easily integrated into existing IDEs
* Developers want examples, both minimal examples of things that work correctly and minimal examples of code that help them understand concurrency problems. 

For more information, take a look at our [paper](http://gustavopinto.org/lost+found/plateau2015.pdf).