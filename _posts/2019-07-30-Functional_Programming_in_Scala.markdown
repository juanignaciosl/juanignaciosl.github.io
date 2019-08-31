---
layout: post
comments: true
title: 'Functional Programming in Scala'
date: 2019-07-30 07:18:57
categories: ['learning']
tags: [Software Development, Scala, courses, Coursera]
excerpt: |
    Notes from the Functional Programming in Scala specialization.
image:
  feature: progfun.jpg
---

6 years ago I took the magnificent courses "Functional Programming Principles in Scala" and "Principles of Reactive
Programming", both at Coursera. Now, there's a full 5-course certification,
[Functional Programming in Scala](https://www.coursera.org/specializations/scala),
including topics such as parallel programming or Big Data analysis with Spark, and it was a good moment for a refresher!

These are my notes.

# Courses

## Functional Programming Principles in Scala

This course hasn't changed too much. In fact, Coursera even displays some of the lecture videos as already seen :-).

### Week 6 - Other collections

Weeks 1-5 are pretty much the same content than before. Week 6 dives deeper into collections (which [have been
revamped](https://www.scala-lang.org/blog/2017/02/28/collections-rework.html)), so some notes are useful to me:

- Vector: more balanced access patterns than `List`. Implemented with a tree with 32 elements in the nodes, so
random access complexity is `log32(N)` (grows very slowly). 32 is convenient for current processors cache, making
processing in batches very efficient.

### References

- [Course Cheatsheet](https://github.com/lampepfl/progfun-wiki/blob/gh-pages/CheatSheet.md).
- [Scala Cheatsheet](https://docs.scala-lang.org/cheatsheets/).
- [Glossary](https://docs.scala-lang.org/glossary/).
- [Scala by Example](https://www.scala-lang.org/old/sites/default/files/linuxsoft_archives/docu/files/ScalaByExample.pdf).
- [Scala School!](http://twitter.github.io/scala_school/).
- [Tour of Scala](https://docs.scala-lang.org/tour/tour-of-scala.html).
- [Scala in SO](https://stackoverflow.com/tags/scala/info).
- Martin Odersky, "Working Hard to Keep It Simple" - OSCON Java 2011:
  - [talk](https://www.youtube.com/watch?v=3jg1AheF4n0).
  - [slides](https://www.slideshare.net/Odersky/oscon-keynote-working-hard-to-keep-it-simple).
- Books:
    - [Structure and Interpretation of Computer Programs](https://mitpress.mit.edu/sites/default/files/sicp/index.html).
    - Programming in Scala. Martin Odersky, Lex Spoon and Bill Venners. 3rd edition. Artima 2016.
    - Scala for the Impatient. Cay Horstmann. Addison-Wesley 2012.
    - Scala in Depth. Joshua D. Suereth. Manning 2012.
    - Programming Scala. Dean Wampler and Alex Payne. O’Reilly 2009.

## Functional Program Design in Scala

### Week 4 - Functional Reactive Programming

Ways to address concurrent access to global state:
- Synchronization. Cons:
  - it blocks threads
  - can be slow
  - can lead to deadlocks
  - imperative
- Replace global state with thread-local state (`scala.util.DynamicVariable`). Each thread accesses a separate copy. Cons:
  - imperative
  - implemented with a global hash table lookup (potential performance issue)
  - when the threads are multiplexed between tasks it won't work (as a worker can be moved among threads)
- Implicit parameter, value passed to the function. Cons:
  - Requires more boilerplate.

### References

- [Reactive Cheatsheet](https://github.com/sjuvekar/reactive-programming-scala/blob/master/ReactiveCheatSheet.md).
- [RxScala API](http://reactivex.io/rxscala/scaladoc/index.html#rx.lang.scala.Observable).
- [ScalaCheck](https://github.com/rickynils/scalacheck/blob/master/doc/UserGuide.md).
