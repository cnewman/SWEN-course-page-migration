---
title: 'Design and Code Communication and Code Reviews'

weight: 100
bookToC: true
bookSearchExclude: false

draft: true
---

## Design and Code Communication and Code Reviews

### Introduction

You communicate about your design and code in different ways. UML models are used to communicate your design information. These include: domain model, class model, statecharts, and sequence diagrams. Code is not just for machines to understand; humans have to read your code as well. Not just for the newbie that just joined the team, but also for you or rather your future self when you have to fix a bug in your "old" code. This lesson describes the purpose of each of the UML models that you will create through this term, and teaches good coding ettiquete.

{{% hint %}}

"Peer review – an activity in which people other than the author of a software deliverable examine it for defects and improvement opportunities – is one of the most powerful software quality tools available. Peer review methods include inspections, walkthroughs, peer deskchecks, and other similar activities. After experiencing the benefits of peer reviews for nearly fifteen years, I would never work in a team that did not perform them."

-From [Humanizing Peer Reviews](http://www.processimpact.com/articles/humanizing_reviews.html) by Karl E. Wiegers

{{% /hint %}}

Code reviews serve a dual purpose. Reviews improve the quality of the product. Simultaneously, reviews increase the overall skill of the team. Code reviews can be informal (one member with another) or formal (with the whole team). Modern source repository tools support online, virtual asynchronous reviews; git's pull request feature is one such tool.

### Learning Outcomes

* Describe the purpose of the UML domain model, class model, statechart, and sequence diagram.
* Use meaningful names in code (class, attribute, methods)
* Establish and adhere to a team coding style
* Document your API using javadocs
* Document your algorithms with in-line comments
* Explain how code reviews improve the quality of the product.
* Conduct a virtual and in-person code review.

### Study Resources

For your study of the UML models refer to resources in the lessons [Domain analysis](/topics/domain-analysis), [Review OO concepts](/topics/fundamental-oo-concepts), [State-based behavior I](/topics/state-based-behaviour-i), and [Sequence diagrams](/topics/sequence-diagrams). (Note: the Sequence diagrams lesson is later in the term.)

For your study of communication, use these resources.

#### *Video Lessons*

* Documenting code with Javadoc available on LinkedIn.com through the RIT library.(5m 27s)

#### *Web Articles and Blogs*

* [Robert's Rules of Coders: #3 Use Good, Meaningful Names](https://csharpdeveloper.wordpress.com/2015/08/06/roberts-rules-of-coders-3-use-good-meaningful-names/)
* [Meaningful Name](http://wiki.c2.com/?MeaningfulName)
* [Oracle's Javadoc tool documentation](http://docs.oracle.com/javase/8/docs/technotes/tools/unix/javadoc.html)
* [HOW TO JAVADOC (EFFICIENT AND MAINTAINABLE) - 6 STEPS](http://www.adam-bien.com/roller/abien/entry/how_to_javadoc_efficient_and)
* [More Effective Javadocs](http://www.javaworld.com/article/2072490/more-effective-javadoc.html)
* [A Seamless Way to Keep Track of Technical Debt in Your Source Code](http://philippe.bourgau.net/a-seamless-way-to-keep-track-of-technical-debt-in-your-source-code/?imm_mid=0f0e65&cmp=em-prog-na-na-newsltr_20170415)
* Kevin London: [Code Review Best Practices](https://www.kevinlondon.com/2015/05/05/code-review-best-practices.html)
* DZone: [Don't Waste Time on Code Reviews](https://dzone.com/articles/dont-waste-time-code-reviews)

#### *Wikipedia*

* [Javadoc](https://en.wikipedia.org/wiki/Javadoc)
* [Software peer review](https://en.wikipedia.org/wiki/Software_peer_review)
* [Code review](https://en.wikipedia.org/wiki/Code_review)

### Class Lecture

* [Design and Code Communication & Code Reviews](/slides/design-and-code-communication-and-code-review.pptx)

### Exercises

None