---
title: 'State Based Behaviour I'

weight: 100
bookToC: true
bookSearchExclude: false

draft: true
---

## State Based Behaviour I

### Introduction

A large part of software behavior is based on the system existing in multiple distinct states over time. These states may indicate different user interface views being displayed, waiting for input from the user or another source, or error conditions that can occur. This state-based behavior includes ways for the system to move from one state to another via defined transitions which are made when the system receives specific events. The software will implement this state-based behavior whether it is explicitly defined or implicitly defined in the code. Using a state machine to define this behavior is the clearest mechanism for everyone on the team to have a common understanding of how the system is required to operate.

### Learning Outcomes

* Describe the basic elements and method of operation of a state-based system.
* Define state-based behavior using a finite-state machine documented with a UML statechart given a system description or code-base.

### Study Resources

For your study of this topic, use some of these resources.

#### *Video Lessons*

* [UML 2 State Machine Diagrams](https://www.youtube.com/watch?v=_6TFVzBW7oo) (beginning to 6:30 minutes), Derek Banas
* [UML Behavioral Diagrams: State Transition Diagram](https://www.youtube.com/watch?v=OsmWASXE2IM) (2:22), Georgia Tech - Software Development Process

#### *Web Articles and Blogs*

* [Why developers should be force-fed state machines](https://shopifyengineering.myshopify.com/blogs/engineering/17488160-why-developers-should-be-force-fed-state-machines)
* [Why developers never use state machines](http://www.skorks.com/2011/09/why-developers-never-use-state-machines/)

#### *Books*

{{% hint warning %}}

RIT ONLY has **one e-copy of this book** so just download Chapter 10 and then close the book on ProQuest so other students can afford the **same courtesy**.

{{% /hint %}}

* [Software Modeling and Design](https://ebookcentral.proquest.com/lib/rit/detail.action?docID=674652), Hassan Gomaa available on ProQuest through the RIT:
    * Chapter 10.1 - Finite State Machines and State Transitions
    * Chapter 10.2 - Examples of Statecharts
    * Chapter 10.3 - Events and Guard Conditions
    * Chapter 10.4 - Actions
    * Chapter 10.6 - Guidelines for Developing Statecharts

#### *Wikipedia*

* [UML State Machine (Statechart)](https://en.wikipedia.org/wiki/UML_state_machine)

### Class Lecture

* [State-based Behavior I](/slides/state-based-behavior-i.pdf)

### Exercises

#### *After Class Exercises*

* State-based behavior I - individual (2 exercise points)
    * Study the operation of the sample webapp. Create a state diagram that defines the operation of the web application interface. This will be in terms of what the user sees on the interface and which verb (GET, POST) - path (URL) pairs are valid as events to trigger transitions. For each transition, be sure to label the event (verb-URL path) that triggers the transition and any action that results from taking the transition. If there is any error checking done in the application, capture that in your state diagram also. The partial state diagram below shows the starting of the sample webapp web application interface state machine.

    ![State machine start]()

    This is an **individual assignment**. By the date indicated in your section's schedule, deposit an image or PDF of your state diagram into the *State-based behavior I - individual* dropbox in the dropbox **Exercises** area of myCourses.

#### *End of Day Exercises*

* State-Based Behavior - quiz
    * Read the sections of Chapter 10 of *Software Modeling and Design* indicated above, and then complete the 5-minute quiz, *State-Based Behavior - quiz*, which is in the myCourses Quiz area before the class session shown in the schedule for your section.