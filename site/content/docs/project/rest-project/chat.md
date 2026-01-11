---
title: 'Chat'

weight: 200
bookToC: true
bookSearchExclude: false

draft: true
---

## RESTful API Project - Chat

### REST1: Basic Resources and Resource Methods

{{< snippet "/snippets/rest-project/rest1-intro.md" >}}

In addition, we’ll be looking at getting a few types of resource methods working, to perform the following functions

* List All Users
* List All Communities and Channels
* Search for messages
* List all messages in a particular channel

These are not the actual APIs, they are the descriptive names of the functionality provided by the APIs. Remember that RESTful APIs operate on resources. In the case of Chat data, the resource is the one of the tables in your Chat DB. The APIs, will be various operations on the table(s).

For example, **List All Users** could look like `/users` and would return all the the rows and columsn from the users table.

**List All Communities and Channels** could look like `/communities` and would return all data related to communities, including channels that are part of a community. This could be from one table, or multiple tables (depending on how you set up your DB). It is up to your work figure out the appropriate way. You may even decide to modify your schema as you investigate how to implement the API.

**Search messages** should take a few (at least two) different search parameters. One for a particular string, and one for size (length of message), or perhaps by date (range). The endpoint will need to handle a variable set of parameters, since you could have zero (return all messages), or 1 or 2 or even 3.

Finally, **List all messages in a particular channel** would return details on one specific channel. The API could either be `/channel/{id}`, where `id` would be the primary key of the desired row and is part of the URL, or could be `/channel?id={id}`, where id is the primary key of the channel you want, but the information is sent as a query string for the API. Keep in mind that this is dependent on the schema and table relationships you have set up, so your API might look quite different.

To implement the functionality, use the above information to name your resources, and then create endpoints and resource methods for those resources. All responses must be in JSON format and follow the [RESTful guidelines](https://restfulapi.net/).

### Test Case Sketches

* Implement tests that cover at least the following scenarios and match the stated results (Note that you do not have to replicate all the old tests from the DB project):
* When calling the API for **List All Users**, you should get an array with N different entries in it. Pre-calculate (based on your data) how many rows should exist and make sure the API returns that many rows.
* Similarly, when calling the API for **List All Communities and Channels** you should get a fixed (correct) number of rows.
* If you call the **List all messages in a particular channel** API, your unit test should confirm that the data returned is what you would expect (again, you will need to work that out ahead of time).
* You call the **Search messages** API to search for messages containing a particular string, and then you search for messages with a string but also with a certain date-range.
* If you call **List all messages in a particular channel** with a channel that does not exist, you should get an empty list back.

### Grading REST1 (40 points)

{{< snippet "/snippets/rest-project/rest1-grading.md" >}}

## REST2: CRUD and Authentication

Create a branch `rest2-dev`. Please tag your code as `rest2` on the master branch when it is merged.

This iteration, you will need to implement basic CRUD operations to your data. Add those actions to your RESTful API using those conventions.

You will create RESTful APIs for the following functions

1. Add a user (with all their information). This would be `POST`. Parameters should be in the BODY
2. Edit a users information. This would be a `PUT` to do a SQL `UPDATE`. Again, parameters in the BODY
3. Remove a user. This is a `DELETE`. Consider how to identify the user
4. List all DMs for a given user and indicate how many you want (i.e. max returned). This is a `GET`. Your choice on parameters
5. A user can login (see below). Note that the login functionality is needed for the add, edit, remove and list functions.
6. A logged in user can send a DM. Consider where to place parameters.
7. All relevant APIs use a session key for authentication (once logged in)

However, we can’t let just anyone come into the system and make changes, so we need to also provide **authentication**. You’ll implement just the core functionality: logging in and logging out - but with thorough testing of ‘happy path’ and ‘problem’ scenarios. From the client side, this will look like this:

1. Send a `POST` request to your login endpoint with a username and password.
    * The userID and password should be parameters in the **BODY** of the `POST` request
2. System returns a message that your login was successful and gives you a **session key**, which is a large random number that nobody should be able to guess.
3. When attempting to do a CRUD operation, the operation will only work if your request also has the session key. The session key should be sent in the HEADER of each subsequent API call. Review the CRUD methods above and consider how that sequence of operations would work.
4. If you send a request to the logout endpoint, then subsequent CRUD operations won’t work. *Note that you must be logged in to trigger a logout, otherwise anyone can log you out!*

You will need a `users` table with a `username`, `userID`, `password`, and `session_key` field in your database. If you have one already, add any fields necessary. If you don’t have such a table, you need to rethink your DB design. Your test data should have a user already registered. However, *we must store our passwords securely*. This means using a one-way encryption function called a **hash digest**. You can read more about hash digests and salts over on the [Common Weakness Enumeration CWE-759](https://cwe.mitre.org/data/definitions/759.html). In pseudocode, the way you would compute the hash digest of a password would be: `hashed_password = hash(password)`

For this project, we’d like you to use the SHA-512 hash digest algorithm. Find a secure way to generate a session key within your technology choice as well, not just the default random number generator for your language.

From the server side, authentication would look like this:

1. Client sends a request with their userID and password to the login endpoint
2. Server computes the proper hash digest of the password
3. Server checks if that digest exists in the database table with that username
4. If so, generate a secure session key. (Otherwise, return a message that login was not successful)
5. Save the session key to the user’s record in the database
6. Return a success message with the session key

For Python, these resources will be helpful:

* [Python’s hashlib](https://docs.python.org/3.12/library/hashlib.html) for SHA-512 digest function
* [Python’s secrets](https://docs.python.org/3.12/library/secrets.html) for generating a session key
* [Online SHA512 calculator](https://abunchofutils.com/u/computing/sha512-hash-calculator/) for sanity testing

### Test Cases

In each test case, print out (clearly), the test being run and the result of the test. If there are multiple steps in a test case, list the steps/ results as they occur. Make sure you handle the results without crashing!

* You can add a new user with a password and any other user information
* If a user already exists, the add user fails
* You can login successfully with the right userID and password; hashing is performed as described, and incorrect passwords fail login
* You can edit a users information; if you try to edit a non-existent user, the API fails
* You can remove a user; again, if the user doesn’t exist, the API fails
* If you try to remove a user (who exists), and don’t have the correct authentication session key, the API fails
* You can list DMs, and specify a maximum number to return. Make sure you have enough messages in the DB to test this properly
* You can add a new DM
* In all cases of failure, the API must return a reasonable error, and your unit test must display a human readable message for the error.

### Key Decisions

* What are the conventions for CRUD operations based on your existing resource?
* Does it make sense to refactor the meaning of your resource to better fit RESTful conventions?
* What will be the structure of the APIs? How will you pass the arguments?

### Grading REST2 (70 points)
* (20pts) CRUD operations work
* (10pts) Authentication feature works
* (30pts) Test cases implemented and pass
* (05pts) RESTful API standards followed
* (05pts) Good code maintainability and quality