---
title: 'Resources'

weight: 3
bookToC: true
bookSearchExclude: false

draft: true
---

## Course Resources

This page provides a number of resources with information on the tools that you will use in the course, and some technologies that you will need to become familiar with. Feel free to use this page throughout the semester to help locate resources for the different technologies we will be using.

### Java

You will use Java 11 to implement the term project. This course has a prerequisite of GCIS-124, CSCI-142, or an equivalent course that provides the fundamentals of object-oriented programming. If you need to refresh your knowledge of Java, these resources will help. If your prerequisite course did not use Java as the object-oriented language, it will be your responsibility to learn the language early on in the course so that you will be able to contribute to your project team's implementation work.

* [Java 11 API Documentation](https://docs.oracle.com/en/java/javase/11/docs/api/index.html)
* For this class, you are encouraged to conform to Google's [Java style guide](https://google.github.io/styleguide/javaguide.html) when writing your code.
* Tutorialspoint offers a great [series of tutorials](https://www.tutorialspoint.com/java11/index.htm) for learning the new features of Java 11.
* LinkedIn (via [RIT login](https://www.linkedin.com/learning-login/)) offers a comprehensive ["Java Essential Training"](https://www.linkedin.com/learning/java-11-plus-essential-training/explore-the-history-of-java?) series for those new to Java programming.

### Web technology

Your term project will be a web-based application, and you will use current web application technologies to implement it. These resources can provide you with additional information about working with these technologies.

#### Angular

Angular is a web application platform and framework for building single-page applications. These are some resources for more information about the framework. There are many others that you may find through your research.

* [Angular Documentation](https://angular.io/docs)
* [Angular Tour of Heroes Tutorial](https://angular.io/tutorial)

#### TypeScript

Typescript is a strongly typed object oriented programming language built on JavaScript. TypeScript is the primary language for Angular application development.

* [TypeScript Documentation](https://www.typescriptlang.org/docs/)

#### Basic HTML/CSS

You will not need to know a lot of HTML/CSS to build this application, but you will need to know some. If you have not done any webpage development before, these resources will be helpful to you.

* w3schools has a plethora of information on both topics:
    * [HTML](http://www.w3schools.com/html/html_intro.asp)
    * [CSS](http://www.w3schools.com/css/css_intro.asp)
* [CodeAcademy interactive tutorial](https://www.codecademy.com/en/tracks/htmlcss)
* Londinium's ["HTML Essential Training"](https://www.lynda.com/HTML-tutorials/HTML-Essential-Training/170427-2.html?org=rit.edu) course

#### Spring Framework

You will use the Spring framework for creating your REST API service. The Spring framework provides the scaffolding to quickly create an application that responds to HTTP requests for the purposes of managing resources.

* [Spring Guides](https://spring.io/guides)
* [Spring Initializr](https://start.spring.io/): a wizard for for creating a Spring project

#### HTTP Protocol

One of your project tasks will be to design the applications REST API. To do that design work, you will need a small amount of knowledge about the HTTP protocol. These resources should give you the introduction that you need.

* [HTTP Overview](https://www.tutorialspoint.com/http/index.htm) - understand requests and responses
* [HTTP Methods](http://www.restapitutorial.com/lessons/httpmethods.html) - we will be using GET, POST, DELETE, and PUT
* [HTTP Status Codes](https://www.restapitutorial.com/httpstatuscodes.html)

### Team and Project Management

The term project is not a very large project, and your team is relatively small. Even so, one of your challenges will be managing the team and project. The project requires your team to use some online tools to assist with, though not be a silver bullet solution for, these challenges.

#### Facilitating project planning with Trello

* [Trello Main Site](https://trello.com/)
* [Getting Started with Trello](https://trello.com/guide)
* [Using Trello for Online Project Management](https://www.youtube.com/watch?v=fe5ei52usQ8)

#### Facilitating team communication with a Slack workspace

Slack is a tool, and should not be used as the silver bullet of team communication. Here are some guidelines and best practices.

* [Slack Main Site](https://slack.com/)
* [How can I use my team's Slack workspace?](https://smallbiztrends.com/2015/12/slack-use-team.html)
* [Tips and Tricks](https://slackhq.com/11-useful-tips-for-getting-the-most-of-slack-5dfb3d1af77#.4dlu8m4ze)
* Add-on tools to support virtual standup meetings and other activities: https://geekbot.io/, https://standuply.com/, [Slackbot](https://get.slack.help/hc/en-us/articles/202026038-An-introduction-to-Slackbot)

#### Tools to facilitate scheduling team meetings

* [When2Meet](http://www.when2meet.com/)
* [Doodle](http://doodle.com/)

### Code Management

Your team will be required to manage the project code and other artifacts using the Git version control system. Students who took GCIS-124 or CSCI-142 as their prerequisite course have some experience using Git in that course. You will host your repository (repo) on GitHub and make use of its issue tracking and pull request review mechanism. These are resources for information on using these tools.

* Git Basics - [Video Tutorials](https://git-scm.com/videos)
* [try.github.io](https://try.github.io/) - An interactive, 15-minute tutorial covering the basics of command-line Git.
* Sick of the command line? Check out these GUI tools
    * [GitHub Desktop](https://desktop.github.com/)
    * [TortoiseGit](https://tortoisegit.org/) - Windows Shell interface
    * [SourceTree](https://www.sourcetreeapp.com/)
    * [GitKracken](https://www.gitkraken.com/)

### Code Development and Debugging

A large percentage of your project work time will be doing code development, debugging, and testing. If you become very familiar with the development tools that you will use, it will save you a lot of time and make you much more productive.

#### Visual Studio Code

The students coming through GCIS-124 will have experience doing Java development using the VS Code tool.

* [VS Code Documentation](https://code.visualstudio.com/docs)
* Visual Studio code has many extensions that you will find useful in your project
    * [Extension Pack for Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
    * [HTML Preview](https://marketplace.visualstudio.com/items?itemName=tht13.html-preview-vscode)
    * [Javadoc Tools](https://marketplace.visualstudio.com/items?itemName=madhavd1.javadoc-tools)
    * [Language Support for Java(TM) by Red Hat](https://marketplace.visualstudio.com/items?itemName=redhat.java)
    * [Markdown PDF](https://marketplace.visualstudio.com/items?itemName=yzane.markdown-pdf)

#### Unit Testing

Testing is done in multiple stages of a software development project. The software developer is responsible for unit testing at the class level. Whenever code is developed in a class, the developer will create unit tests to verify the functionality of the code for both correct scenarios and error checks. These tests should be implemented with the support of a testing framework, such as JUnit. The testing framework provides mechanisms to run the tests and to verify the results. Each class must be tested in isolation which requires the tests to control all dependencies that the class under test has on other objects. Object mocking is a technique for creating objects that the tested class will use during a test. A mocking framework, such as Mockito, supports the definition of mock objects and the responses that should occur when methods are called. The mocking framework also allows the test to check if methods were called the expected number of times during a test. Your team will be required to write unit tests for all non-trivial class in your project.

* [JUnit](http://junit.org/)
    * JUnit is a popular unit testing framework for Java.
    * The official JUnit wiki has a guide for [getting started](https://github.com/junit-team/junit4/wiki/Getting-started).
    * TutorialsPoint offers an in-depth [tutorial](https://www.tutorialspoint.com/junit/index.htm) that can help you understand how to use JUnit with your project.
* [Mockito](http://site.mockito.org/)
    * A mocking framework for unit tests in Java
    * The official Mockito project [Wiki](https://github.com/mockito/mockito/wiki) has a ton of information to get you started.
    * TutorialsPoint has a number of [tutorials](https://www.tutorialspoint.com/mockito/), including one on using [Mockito with JUnit](https://www.tutorialspoint.com/mockito/mockito_junit_integration.htm).

#### Code Coverage

You should design your program's test suite to test all aspects of functionality of the application. A code coverage tool, such as JaCoCo, can compute execution at the class, method, line, and down to the Java byte code level. Your team should aim to have 100% coverage of your code base.

* [JaCoCo](http://www.jacoco.org/)
    * JaCoCo is a code coverage library for Java.
    * You will run JaCoCo code coverage using Maven. [This page](http://www.eclemma.org/jacoco/trunk/doc/maven.html) provides helpful information on what you need to include in your POM.xml file to install the JaCoCo Maven plugin.
    * The second bulleted element in the Examples list gives an example POM.xml file showing how you can run JUnit tests under code coverage.
    * You may also want to explore running code coverage [directly within IntelliJ](https://www.jetbrains.com/help/idea/2016.3/code-coverage.html). For our purposes, you should be able to run these using Maven from the command line.

#### HTTP message debugging

For the term project, you should not need to do a lot of client side debugging, but it will be helpful to have a way to view the HTTP protocol exchanges. Browsers come with debugging tools for seeing the request and response messages.

* Chrome - [DevTools](https://developers.google.com/web/tools/chrome-devtools/)
* Firefox - [Developer Tools](https://developer.mozilla.org/en-US/docs/Tools)
* Safari - [Developer Tools](https://developer.apple.com/library/content/documentation/AppleApplications/Conceptual/Safari_Developer_Guide/GettingStarted/GettingStarted.html)
* IE/Edge - [f12 DevTools](https://developer.microsoft.com/en-us/microsoft-edge/platform/documentation/f12-devtools-guide/)

#### Maven build tool

You will use the Apache maven tool to build and execute your project. You will not need to do a lot with the pom.xml (program object model) declarations to maven except adding new elements along the way to do unit testing, code coverage, etc. Some knowledge of how maven works will take away the idea that it is all just magic.

* Installation Instructions
    * [Windows](https://www.mkyong.com/maven/how-to-install-maven-in-windows/)
    * [Mac/Unix](https://www.mkyong.com/maven/install-maven-on-mac-osx/)
* [Maven in 5 minutes](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html)

### Documentation and Communication

#### UML drawing tools

You will be asked to describe your design with class structure, sequence, and statechart diagrams using the UML notations. Here are a few tools you might use to do this.

* [Lucidchart](https://www.lucidchart.com/) - Free online tool. Allows real-time collaboration using Google Drive
* [Draw.io](https://www.draw.io/) - Free online tool. Integrates with Google Drive as well.

####  Markdown Formatting

Markdown is a simple formatting notation that you will use for creating your design documentation. All of the documentation including image files for UML models are included in your project's repository. Here are some resources for working with Markdown formatting.

* Markdown guides
    * [Markdown cheat sheet](https://guides.github.com/pdfs/markdown-cheatsheet-online.pdf)
    * [Markdown quick reference](https://en.support.wordpress.com/markdown-quick-reference/)
* Editing markdown files
    * [StackEdit](https://stackedit.io/)
    * [Dillinger](https://dillinger.io/)
    * [Markable](https://markable.in/)
    * [The Best Markdown Editor for Windows](https://www.sitepoint.com/best-markdown-editors-windows/)
    * [The Best Markdown Editor for Mac](https://www.sitepoint.com/the-best-markdown-editors-for-mac/)
* Converting to PDF
    * VSCode extension [Markdown PDF](https://marketplace.visualstudio.com/items?itemName=yzane.markdown-pdf)
    * [pandoc](https://pandoc.org/) document conversion
    * [MiKTeX](https://miktex.org/) open source Latex implementation
    * The template design document for your project includes an initial YAML header section which sets the margins to a reasonable 1inch from the Latex default wide margins. From the command line, you can set the margins using `pandoc DesignDoc.md -V geometry:margin=1in -o DesignDoc.pdf`
