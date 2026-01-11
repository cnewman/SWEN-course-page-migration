---
title: 'Rideshare'

weight: 300
bookToC: true
bookSearchExclude: false

draft: true
---

## RESTful API Project - Rideshare

### REST1: Basic Resources and Resource Methods

{{< snippet "/snippets/rest-project/rest1-intro.md" >}}

In addition, we’ll be looking at getting a few types of resource methods working, to perform the following functions

* List All Account Information
* List All Ride Information
* Show details about a specific account

These are not the actual APIs, they are the descriptive names of the functionality provided by the APIs. Remember that RESTful APIs operate on resources. In the case of Rideshare data, the resource is the one of the tables in your Rideshare DB. The APIs, will be various operations on the table(s).

For example, **List All Account Information** could look like `/account` and would return all the the rows and columsn from the account table.

**List All Ride Information** could look like `/rides` and would return all data related to rides. This could be from one table, or multiple tables (depending on how you set up your DB). It is up to your work figure out the appropriate way. You may even decide to modify your schema as you investigate how to implement the API.

Finally, **Show details about a specific account** would return details on one specific row. The API could either be `/account/{id}`, where `id` would be the primary key of the desired row and is part of the URL, or could be `/account?id={id}`, where `id` is the primary key or the account you want, but the information is sent as a query string for the API.

To implement the functionality, use the above information to name your resources, and then create endpoints and resource methods for those resources. All responses must be in JSON format and follow the [RESTful guidelines](https://restfulapi.net/).

### Test Case Sketches

* Implement tests that cover at least the following scenarios and match the stated results:
* When calling the API for **List All Account Information**, you should get an array with N different entries in it. Pre-calculate (based on your data) how many rows should exist and make sure the API returns that many rows.
* Similarly, when calling the API for **List All Ride Information** you should get a fixed (correct) number of rows.
* If you call the **Show details about a specific account** API, your unit test should confirm that the data returned is what you would expect (again, you will need to work that out ahead of time).
* If you call **Show details about a specific account** with an id that does not exist, you should get an empty list back.

### Grading REST1 (40 points)

{{< snippet "/snippets/rest-project/rest1-grading.md" >}}

## REST2: CRUD

Create a branch `rest2-dev`. Please tag your code as `rest2` on the master branch when it is merged.

This iteration, you will need to implement basic CRUD operations to your data. Add those actions to your RESTful API using those conventions.

You will create RESTful APIs for the following functions

1. Add a user (with all their information). This would be `POST`. Parameters should be in the BODY (i.e. not part of the URL)
2. Edit a users information. This would be a `PUT` to do a SQL `UPDATE`. Again, parameters in the BODY
3. Remove a user. This is a `DELETE`. Consider how to identify the user
4. List receipts and indicate how many you want (i.e. max returned). This is a `GET`. Your choice on parameters

### Test Cases

In each test case, print out (clearly), the test being run and the result of the test. If there are multiple steps in a test case, list the steps / results as they occur. Make sure you handle the results without crashing!

* You can add a new user with a password and any other user information
* If a user already exists, the add user fails
* You can edit a users information; if you try to edit a non-existent user, the API fails
* You can remove a user; again, if the user doesn’t exist, the API fails
* You can list receipts, and specify a maximum number to return. Make sure you have enough receipts in the DB to test this properly
* You can add a ride. Consider placement of parameters.
* In all cases of failure, the API must return a reasonable error, and your unit test must display a human readable message for the error.

### Key Decisions

* What are the conventions for CRUD operations based on your existing resource?
* Does it make sense to refactor the meaning of your resource to better fit RESTful conventions?
* What will be the structure of the APIs? How will you pass the arguments?

### Grading REST2 (60 points)

* (20pts) CRUD operations work
* (30pts) Test cases implemented and pass
* (05pts) RESTful API standards followed
* (05pts) Good code maintainability and quality