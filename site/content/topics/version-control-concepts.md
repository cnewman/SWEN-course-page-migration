---
title: 'Version Control Concepts'

weight: 100
bookToC: true
bookSearchExclude: false

draft: true
---

## Version Control Concepts

### Introduction

*Change Management* is the discipline to track the changes in a project's source code. Version Control System tools support a team of developers working on the same repository each with their own working copy. There are many aspects to version control. This lesson will cover some basics of `git`.

Feature Branching is a release management technique for isolating user stories into their own source repository branch. This permits user stories to be integrated into the master branch at the time of the team's choosing.

In any team project, there will be times when multiple developers make changes to the same files. In most cases, the changes do not overlap and so they can be automatically merged. However, there will be cases in which the changes do overlap. This lesson finishes with a discussion of merge conflicts resolution.

### Learning Outcomes

* Describe what change management is and why it's important
* Describe the basic workflow of a distributed version control system
* Use fundamental `git` commands to perform version control
* Use feature branching to facilitate release management of individual user stories
* Identify types of merge conflicts, and prevention and resolution strategies

### Study Resources

For your study of this topic, use these resources.

#### *Web Articles and Blogs*

* Git project
    * [Git Home Page](https://git-scm.com/)
    * [Git Documentation](https://git-scm.com/doc)
* [Git Cheat Sheet](https://www.jrebel.com/blog/git-cheat-sheet)
* [Getting Started With Git](https://dzone.com/refcardz/getting-started-git)
* [How to Write a Git Commit Message](https://cbea.ms/git-commit/)
* [Feature branching your way to greatness](https://www.atlassian.com/agile/software-development/branching)
* [Feature Branch](https://martinfowler.com/bliki/FeatureBranch.html)
* [Resolve Merge Conflicts](https://support.atlassian.com/bitbucket-cloud/docs/resolve-merge-conflicts/)

#### *Wikipedia*

* [Software configuration management](https://en.wikipedia.org/wiki/Software_configuration_management)
* [Version control](https://en.wikipedia.org/wiki/Version_control)
* [Branching (version control)](https://en.wikipedia.org/wiki/Branching_(version_control))
* [Revision tag](https://en.wikipedia.org/wiki/Revision_tag)

### Class Lecture

* [Version Control Concepts](/slides/version-control-concepts.pdf)

### Exercises

* Ensure that you have completed the set up tasks for DB0
* Ensure your development branch is created and configured correctly and begin [DB1 - chat](/docs/project/db-project/chat-system#db1-initial-schema-test-data-set), [DB1 - rideshare](/docs/project/db-project/rideshare#db1-initial-schema-test-data-set), or [DB1 - library](/docs/project/db-project/library#db1-initial-schema-test-data-set)