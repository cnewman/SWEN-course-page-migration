---
title: 'Vision'

weight: 10
bookToC: true
bookSearchExclude: false

draft: true
---

## Purpose

The goal of this semester-long project is to give you a thorough understanding and appreciation for building a large software system within a team. Throughout the semester, you will be faced with many decisions - many of which have no "right" answer, just consequences for your work later on.

## Project Goals

The primary goal of this project is to show how the individualized act of programming can be transformed into a large product maintained by a team of engineers. We'll walk you through the whole software development life cycle, which means looking deeper at each step.

That said, you'll also learn a lot of web development along the way. There's no better way to learn something than to practice it.

We're asking you to work with some industrial-strength web application frameworks. We believe that this will give you a very valuable experience as a full-stack developer using tools and technologies that you are likely to experience in a professional environment.

Think of the technical documentation as another one of your textbooks. For example, the Flask-restful guides and the React docs. These sources will have a lot more information than you need, but you're expected to consult them first whenever you need something. Thus, beyond our startup tutorials, we will not hold your hand. We will give you broad overviews and wisdom, but the project itself will involve learning on the fly - which is a skill in and of itself.

## Project Overview

TaskMaster is a comprehensive task management application designed to help users organize, track, and complete their tasks efficiently. It allows users to create, categorize, and prioritize tasks, set due dates, and monitor their progress. The application supports multiple users, enabling collaboration and task sharing within teams or groups. TaskMaster provides a user-friendly interface accessible via web browsers, offering features like task filtering, sorting, and basic analytics to help users manage their time and productivity effectively.

### MVP Features

The Product Owner desires a minimal viable product (MVP) which includes these features:

1. User Management:
    * User registration and authentication
    * Basic user profiles
2. Task Management:
    * Create, read, update, and delete tasks
    * Assign task properties: title, description, due date, status (e.g., pending, in progress, completed), and priority
3. Categorization:
    * Create and manage task categories or tags
    * Assign categories/tags to tasks
4. Task Viewing and Organization:
    * List view of tasks with basic sorting (e.g., by due date, priority)
    * Simple filtering options (e.g., by status, category)
5. Basic Reporting:
    * View overdue tasks
    * Task completion statistics
6. User Interface:
    * Responsive web design for desktop and mobile browsers
    * Intuitive forms for task creation and editing
    * Simple dashboard showing task overview
7. Data Persistence:
    * Store all data in a PostgreSQL database
    * Implement basic data validation
8. API:
    * RESTful API endpoints for all core functionalities
    * Basic error handling and status codes
9. Security:
    * Secure user authentication
    * Basic authorization to ensure users can only access their own tasks
    * Basic Distrustful Decomposition (input validation at both ends of your Architecture)
10. Administration and Management:
    * Administrative Console for user management (ban, promote, suspend users)
    * Principle of least privilege for user access and administration (privilege granted only as needed)

## Architecture

Teams will use the following technologies for development of the app

* [Postgres](https://www.postgresql.org/) for your database
* [Flask-restful](https://flask-restful.readthedocs.io/en/latest/) for your server/middle layer
* [React](https://react.dev/) for your front-end

Since many of the lectures touch upon these technologies,There will be ***no deviations*** from this technology stack. You are welcome to add dependencies to your application with the approval of the Product Owner. This decision is one that the team unanimously must agree upon.

## Process

The team will use the [OpenUP](https://www.utm.mx/~caff/doc/OpenUPWeb/) methodology for strategic planning activities across the life span of the project. The team will use the [Scrum process](https://www.scrumalliance.org/) for tactical day-to-day operations.