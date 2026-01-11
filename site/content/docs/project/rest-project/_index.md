---
title: 'Rest Project'

weight: 50
bookToC: true
bookSearchExclude: false

draft: true
---

## The Project

We will now be using a proper back-end API to access our data. We will use the RESTful architectural style to accomplish this. You will have a new group, and a new project.

### Teams

You will find your teams in gitlab, and project assignments are the same as for the DB project(s)

### Language Choice

The technology stack for this project will be:

* Python 3.12
* [Flask web framework](https://flask.palletsprojects.com/en/stable/)
* [Flask-RESTful library](https://flask-restful.readthedocs.io/en/latest/)
* [Requests library for HTTP testing](https://realpython.com/python-requests/)
* PostgreSQL 17 database
* psycopg2 for PostgreSQL interaction
* You need to make sure this runs **on the GitLab CI**. The instructor and TA are well-versed in Docker and can help you on this, but ultimately this is on you.
* You must use **HTTP-based automated tests** on the CI. Our default setup instructions use Python and the Requests library. If you use other libraries with Python, that is ok, but you are responsible for making sure everything works in the environment.
* All DB work must follow expected DB practices and good use of SQL commands.

### Setup

Follow our [RESTful setup instructions](/docs/project/rest-project/restful-setup).

### Tips and Tools

* Python’s `unittest` library is built-in, but there are other compatible libraries that do more. One other is `pytest` as an alternative. Your tests are exactly the same, just install it and run `pytest` instead of `python -m unittest`. A particularly helpful option is `pytest --pdb` which invokes a debugger on failed unit test.
* Speaking of debuggers, the `breakpoint()` method is available in Python everywhere. Just run it near your broken code and when Python encounters it, your code will drop into a debugging console.
* The output of our RESTful APIs will be in JSON. You can install a browser extension that will automatically format JSON for you so that you can see and traverse the output easier.