---
title: 'Backlog Refinement and Estimation'

weight: 100
bookToC: true
bookSearchExclude: false

draft: true
---

## Backlog Refinement and Estimation

### Introduction

*Backlog Refinement*, also called *Backlog Grooming* in some circles, is the process of refining a user story in preparation for sprint planning which includes estimation of the effort required to complete user stories. During backlog refinement *acceptance *criteria for the story are defined and a sketch of the solution design is specified. Both of those items need to be detailed in a user story for you to be able to estimate the effort.

Software development work is notoriously difficult to estimate. Agile methods recommend *relative estimation* techniques such as Planning Poker. After a story has been analyzed and designed it can be estimated. The team must come to a consensus on the estimate or discuss and vote again until consensus is reached.

### Learning Outcomes

* Write effective *acceptance criteria* statements
* Specify the necessary design changes and additions to implement of a user story
* Explain the process of story estimation
* Apply Planning poker for story estimation

### Study Resources

For your study of this topic, use these resources

#### *Video Lessons*

* [Agile at Work: Refining the backlog (2:57)](https://www.linkedin.com/learning/agile-requirements-foundations/refining-the-backlog)
* [Agile Estimating and Planning: Planning Poker - Mike Cohn (5:31)](https://www.youtube.com/watch?v=gE7srp2BzoM)

#### *Web Articles and Blogs*

* These web articles are required reading
    * [Cheat Sheet for Backlog Refinement](https://www.leadingagile.com/2013/11/cheat-sheet-backlog-refinement/)
    * [Acceptance Criteria](https://www.leadingagile.com/2014/09/acceptance-criteria/)
    * [INVEST Criteria](https://www.leadingagile.com/2011/03/product-owner-team-invest-criteria/)
    * [Planning poker](https://www.mountaingoatsoftware.com/agile/planning-poker)
    * [8 Rules for Scrum Planning Poker](http://blog.agilistic.nl/using-scrum-poker-to-estimate-user-stories/)
* Some additional resources
    * [Product Backlog Refinement (Grooming)](https://www.mountaingoatsoftware.com/blog/product-backlog-refinement-grooming)
    * [Product Backlog Refinement](https://www.youtube.com/watch?v=pSguy2FuC2c)

#### *Wikipedia*

* [Backlog refinement](https://en.wikipedia.org/wiki/Scrum_(software_development)#Backlog_refinement)
* [Planning poker](https://en.wikipedia.org/wiki/Planning_poker)
* [Wideband delphi](https://en.wikipedia.org/wiki/Wideband_delphi)

### Class Lecture

* [Backlog Refinement and Estimation](/slides/backlog-refinement-and-estimation.pptx)

### Example Acceptance Criteria and Solution Tasks

#### User Story

As a User, I want to add a new task with a title, description, and due date so that I can keep track of my responsibilities.

#### Acceptance Criteria

1. The user can access a "Create New Task" form from their dashboard.
2. The form includes fields for title, description, and due date.
3. The title field is required and limited to 100 characters.
4. The description field is optional and limited to 500 characters.
5. The due date field is required and must be a valid future date.
6. The user receives an error message if they try to submit the form with invalid data.
7. Upon successful submission, the new task appears in the user's task list.
8. The user receives a confirmation message after successfully adding a task.

#### Solution Tasks:

1. Database:
    * Design and create a 'tasks' table in the PostgreSQL database with appropriate fields (id, user_id, title, description, due_date, created_at, updated_at).
    * Create necessary indexes for efficient querying.
2. Backend (Flask):
    * Implement a new API endpoint for task creation (e.g., POST /api/tasks).
    * Create a database model for tasks.
    * Implement input validation for the task creation form data.
    * Write logic to insert the new task into the database.
    * Implement error handling for invalid inputs.
    * Write unit tests for the task creation functionality.
3. Frontend (React):
    * Design and implement a "Create New Task" form component.
    * Add form validation on the client-side (e.g., required fields, character limits).
    * Implement a date picker for the due date field.
    * Create a function to send the form data to the backend API.
    * Implement error message display for invalid inputs.
    * Add a success message component for task creation confirmation.
    * Update the task list component to include the newly created task.
    * Write unit tests for the new React components.
4. Integration and Testing:
    * Integrate the frontend form with the backend API.
    * Perform end-to-end testing of the task creation process.
    * Test edge cases (e.g., submitting empty forms, invalid dates).
    * Ensure proper error handling and display of error messages.
5. Documentation:
    * Update API documentation to include the new task creation endpoint.
    * Add user guide information about creating new tasks.

### Exercises

#### Homework Exercises

* Sprint 2/3 plan - team
    * For each potential Sprint 2/3 user story:
        1. Refine the story by defining acceptance criteria and solution tasks at an appropriate level of detail.
        2. As you create acceptance criteria, update your acceptance test plan.
        3. As a team perform planning poker on that story.
        4. Modify the Trello card with the points for this story; put it in the title of the card, such as "Register for a League (8)" means the *Register for a League* user story was assigned 8 story points

![example screenshot of trello](/images/Planning_Poker_example_screenshot.png)