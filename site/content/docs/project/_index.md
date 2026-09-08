---
title: 'Project'

weight: 4
bookToC: true
bookSearchExclude: false

draft: true
---

## Term Project

This course has a project that you will work on through the term. You will choose 1 team mate. Teams will only be adjusted if need-be (as determined by the instructor). Refer to the [Vision Document](/docs/project/vision) for more information.

### Individual Team Member Evaluation

Everyone on your team will receive a team grade based on the overall performance of the team. Individual adjustments to the team grade will be made based on the level of participation of each team member. Your instructor will assess individual team member participation using the following evidence:

* Trello sprint planning board logs
* Team Discord presence with meaningful contributions
* GitHub contributions
    * Commits performed
    * Issues opened/closed
    * Pull requests issued/reviewed

## Inception Phase / Sprint 0

{{% hint %}}

"As the first of the four phases in the project lifecycle, Inception is about understanding the project scope and objectives and getting enough information to confirm that the project should proceed - or to convince you that it should not."

[OpenUP Inception Phase](https://www.utm.mx/~caff/doc/OpenUPWeb/openup/guidances/concepts/inception_phase_C4456871.html?nodeId=44eabb44)

{{% /hint %}}

All of the information you need about the project for Sprint 0 is in the [Vision document](/docs/project/vision).

### Project Activities

* Project submissions
    * [Domain model](/topics/domain-analysis#exercises)
    * Create a repository for your TaskMaster project at the root of your group on Gitlab.
    * Pin a link to your **Trello** and to your **TaskMaster** repository in your Discord group chat.
* Other grading dimensions
    * Individual team member visibility in Discord workspace
    * Team activity on Gitlab and Trello
    * Each team member has completed DB1 and DB2

### Submission Instructions

* Create a consolidated [Domain model](/topics/domain-analysis#exercises) that incorporates the best elements from each student's domain model. Submit your team's domain model as a PDF file in a directory called 'domain model' at the root of your repo.
* Your instructor will look at your team communications participation directly in your Discord workspace channels.

## Elaboration Phase / Sprint 1

{{% hint %}}

"This is the second of four phases in the project lifecycle, when architecturally significant risks are addressed."

[OpenUP Elaboration Phase](https://www.utm.mx/~caff/doc/OpenUPWeb/openup/guidances/concepts/elaboration_phase_BE880435.html?nodeId=ec376027)

{{% /hint %}}

### Project Activities

* Project submissions
    * [Sprint 1 release](/docs/project/git-releases)
    * Create your Product Backlog by populating your Trello planning board with a consolidated set of User and Spike Stories, and Epics following the [Product backlog instructions](/assignments/trello-initiation).
    * Add more stories based on the vision document [Vision document](/docs/project/vision). You should create at least 1 more story per MVP feature (e.g., user management, task management, etc)
    * Format the stories correctly, adding accpetance criteria, acceptance tests, and solution tasks
    * Create an initial Definition of Done
    * Add PEP8 to your CI via [Ruff](https://github.com/astral-sh/ruff)
    * Application implementation
        * Basic Functionality - some basic REST routes and a basic DB schema (for example, create a basic schema for tasks and a route to create/read the tasks in the db)
        * Adherence to Model View View-Model (MVVM) Architecture (just follow course instructions)
* Other dimensions
    * [Planning and managing of activities in Trello board](/docs/project/sprint-planning#sprint_1_planning) **throughout** and in support of **Sprint 1**
    * Gitlab use for version control
    * Discord workspace use for team communication
    * [Individual team member visibility in Trello board, Discord, and Gitlab activities](#individual-team-member-evaluation)
    * Each team member completes their individual assignments and shows competency in the programming required by the project

### Team Exercises

* [Gitlab repository](/topics/version-control-concepts)

### Submission Instructions

* Create a Sprint 1 release using the instructions for creating a Gitlab code release provided [here](/docs/project/git-releases).
* Create the **Sprint 2 Done** list in Trello. Put it to the left of the **Sprint 1 Done** list.
* Your instructor will observe your demo to gauge the functionality of the product, and may also do independent testing of it for the Functionality dimension. The instructor will do this privately, you will not be making presentations. Make sure you have a working prototype (i.e., make sure it works on Gitlab's pipeline)
* Your instructor will review your code to determine how well you are adhering to the architecture and design principles that were covered in class.
* Your instructor will look at your [planning activity](/docs/project/sprint-planning) directly in your Trello planning board. Your planning activity should show consistent updating of the story cards as stories progress through the sprint activities.
* Your instructor will look at your version control use, and team communications directly in your Gitlab repository, and Discord workspace, respectively.
* Your instructor will determine [individual contributions](#individual-team-member-evaluation) to the project directly from the evidence provided in your team's Trello board, Gitlab repository, and Discord workspace.

## Construction Phase / Sprint 2

{{% hint %}}

"As the third of the four phases in the project lifecycle, Construction focuses on design, implementation, and testing of functions to develop a complete system."

[OpenUP Contruction Phase](https://www.utm.mx/~caff/doc/OpenUPWeb/openup/guidances/concepts/construction_phase_873B6559.html?nodeId=3a9fdb2f)

{{% /hint %}}

### Project Activities (7.5% in final grade)

* Project submissions
    * [Sprint 2 release](/docs/project/git-releases)
    * Design documentation - Updated Domain Model, a Readme.md that describes how to install and run your project, any other design artifacts you have created (like ER Diagram)
    * Application implementation
        * Basic Functionality -- Code needs to work on Gitlab, code should be tested, you should be using Continuous Integration
        * You are keeping track of code coverage. Submit this by zipping the output (make sure it shows everything) from the Coverage tool and adding it to the documentation directory in your project repository.
        * Your server implementation (rest routes) should be mostly complete by this point, and your focus should be leaning toward the web interface
* Other dimensions
    * Planning activities in Trello board
    * GitHub use for version control
    * Discord workspace use for team communication
    * [Individual team member visibility in Trello board, Discord workspace, and GitHub activities](#individual-team-member-evaluation)

### Submission Instructions
* Create a Sprint 2 release using the instructions for creating a GitHub code release provided [here](/docs/project/git-releases).
* Create the Sprint 3 Done list in Trello. Put it to the left of the Sprint 2 Done list.
* Your instructor will observe your demo (ON GITLAB AND/OR LOCAL MACHINE) to gauge the functionality of the product, and may also do independent testing of it for the Functionality dimension.
* Make sure your README is at the root of the repo and your updated domain model is in the domain model directory
* Your team should submit code coverate statistics (if it shows in the CI, that's fine. Otherwise, you can add it as part of your commit).
* Your instructor will evaluate your Planning Activities and Version Control Activities directly in your Trello planning board and GitLab repository, respectively.
* Your instructor will determine [individual contributions](#individual-team-member-evaluation) to the project directly from the evidence provided in your team's Trello board, GitHub repository, and Discord workspace.

## Construction Phase / Sprint 3

{{% hint %}}

"As the third of the four phases in the project lifecycle, Construction focuses on design, implementation, and testing of functions to develop a complete system."

[OpenUP Construction Phase](https://www.utm.mx/~caff/doc/OpenUPWeb/openup/guidances/concepts/construction_phase_873B6559.html?nodeId=3a9fdb2f)

{{% /hint %}}

### Project Activities

* Project submissions
    * [Sprint 3 release](/docs/project/git-releases)
    * Application implementation
        * Basic Functionality -- Code needs to work on Gitlab, code should be tested, you should be using Continuous Integration
        * A finalized prototype -- The implemention of core functionality should be mostly done by the end of Sprint 3, minus a few bugs or small features. You can still be working on it for Sprint 4, but you want to be sure you can get it deployed by the end of Sprint 4!
        * Unit tests and code coverage
        * Adherence to prescribed architecture and design principles
        * Code reviews (in your Gitlab instance)
        * Code communication (through Discord)
        * Code Metric(s), of your choosing, through a static or dynamic analysis tool (of your choosing). This can be anything except the code coverage tool.
        * Code Documentation -- Your code should have some basic documentation (can be improved for Sprint 4). In particular, your functions should have some comments, you should have a README at the root of your project that describes how to install/use your project
* Other dimensions
    * Planning activities in Trello board
    * GitHub use for version control
    * Discord workspace use for team communication
    * [Individual team member visibility in Trello board, Discord workspace, and GitHub activities](#individual-team-member-evaluation)

## Transition Phase / Sprint 4

{{% hint %}}

"Fourth and final phase in the project lifecycle, Transition focuses on deploying software to users and ensuring that their expectations about the software were met."

[OpenUP Transition Phase](https://www.utm.mx/~caff/doc/OpenUPWeb/openup/guidances/concepts/transition_phase_DD5986E5.html?nodeId=902e4d0d)

{{% /hint %}}

### Project Activities (5% in final grade)

* Project submissions
    * Final Gitlab release labeled 'Sprint 4 release'
    * All requirements for prior sprints (0, 1, 2, 3) should be complete
    * All required core functionality has been implmented
    * 10% additional functionality has been fully implemented
    * Code is documented -- non-trivial Functions are commented, there is a README discussing how to install and run your project in the root of your gitlab repository
* Other dimensions
    * [Individual team member visibility in Trello board, Discord workspace, and GitHub activities](#individual-team-member-evaluation)
