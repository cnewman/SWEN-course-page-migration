---
title: 'Library'

weight: 300
bookToC: true
bookSearchExclude: false

draft: true
---

## DB Project - Library System

You are a tech company that provides an application for managing a library system. The application has the basic concepts of users, items in the library (books mainly) and checking out any of the items to borrow for a period of time. The app also has a system where you can track late fees (for those inconsiderate people who are late returning things!).

## DB Design & Style Expectations

{{< snippet "snippets/db-project/db-expectations.md" >}}

## DB0: Setup

{{< snippet "snippets/db-project/db0-setup.md" >}}

### Grading DB0

{{< snippet "snippets/db-project/db0-grading.md" >}}

## DB1: Initial Schema, Test Data Set

{{< snippet "snippets/db-project/db1-intro.md" >}}

### Grading DB1

{{< snippet "snippets/db-project/db1-grading.md" >}}

### Feature Overview

The main stakeholders in this system are library users.

* Users have a name, contact info.

There is also an inventory of items (typically books) in the library.

* Items have attributes such as: title, type, publish date, author.
* You will also need to track status of the item (available or checked out) as well as how many of each item are in inventory.
* The library will need to track who has checked out each item.

Use this thought process to design your tables and the relationships.

The core of your system is the concept of a managing the inventory and users. Create the appropriate tables with some test data. Don’t worry about too many other columns than what you need for these tests. At this point, you can load your test data with any mechanism you choose. We’ll get to loading from files later (although you can do that here as well, if you choose)

### Key Decisions

* How many tables?
* What info is needed for users? What info is needed for books?
* How to track checked out items?
* How to deal with having multiple copies of a book?

### Test Data Seeds

You will need to maintain a set of test data to seed your database before every test. This can take multiple forms, such as:

* An SQL file with `CREATE` and `INSERT` statements etc.
* A hardcoded Python file calling SQL commands

How you do this is up to you. You are welcome to add more data than what we ask for any tests you write.

For DB1, you need **at least** the following data (you can add more):

* Ada Lovelace, Mary Shelley, Jackie Gleason and Art Garfunkel are library users.
* Each of those users (except Art) has at least 1 book currently checked out
* The library has only fiction and non-fiction books (as types).
* At least 3 books of each type (*fiction, nonfiction*) has between 3 and 10 copies in total inventory

### Test Case Sketches

* The database is seeded with a test data set without crashing and the correct count of rows for each table is verified
* When we list the books that Art has checked out, it returns an empty list.
* When we list the books that Jackie Gleason has checked out, it correctly lists them in alphabetical order.
* When a librarian lists checked out books, it lists everyone’s checked out books ordered by user name
* The librarian can list all the non-fiction books in inventory, along with the quantity.

{{% hint warning %}}

**Note:** make sure your tests are actually making calls to your API, not just directly to the database. Data will be seeded directly, but your tests will be testing the API. For example, there should be an API method for “List Checkouts for user…” (that’s a description, not the actual API!) and we call that.

{{% /hint %}}

### Not Necessary (yet)

For this iteration, you do NOT need to implement:

* An API call for creating new accounts for users
* Handling charges related to overdue items
* Checkout and return dates
* An API call for adding items

You may need to do these in the future, but for now just test against your pre-seeded test data.

## DB2: CRUD & Design iteration

{{< snippet "/snippets/db-project/db2-intro.md" >}}

### Grading DB2

{{< snippet "/snippets/db-project/db2-grading.md" >}}

### DB2 Library - Requirements

In this iteration, you will add:

* The ability to create new accounts for users (on the fly with an API, not just pre-loaded accounts)
* Users can also be removed
    * Does this mean disabling or deleting? Consider this choice and implement accordingly.
* There is now a one line summary of each book available.
* We can already checkout a book, but now we can also return a book (so it will no longer show as **currently** checked out, but will have a history, and include the checked out date and the returned date)
* **Reserving books**
    * A book should be able to be reserved. This can only be done if all copies of the book are currently checked out.
* We will add the ability to load data from a file. You will load test data from the provided [csv file](/data/Library.csv) and add to the database.
    * This file contains a set of information on books, but will (obviously) not match your own schema.
    * NOTE: You will need to read in the data and figure out a way to make it fit according to your DB schema (or do you need to modify your schema?). There can be inconsistencies in data format, some stray characters etc. Think through how to import this data using code, in a consistent, logical and efficient way.

### Key Questions

* Deactivate or delete accounts?
* When a book is checked out, how should you track how many copies are still available?
* What is the flow of states for a reserving a book?
    * What if you try reserve a book that has available copies?
* How do you make the csv file data fit into your schema? Do you need to update your schema?

### Test Case Sketches

In each situation, the minimum expectation in the unittest is that you perform the operation and then write the appropriate code to confirm the operation was successful. You may need to adjust some older tests to keep them working as your schema changes.

* All .csv books loaded successfully into database (your unittest should check for the count of items)
* Jackie Gleason wants to reserve a copy of a book (make sure you follow the rules for reserving a book)
    * The request fails when reserving incorrectly and succeeds when doing it correctly.
* Christopher Marlowe and Francis Bacon each sign up for a new account
* Art Garfunkel returns a copy of “Frankenstein” three days after he borrowed it.
* Mary Shelley searches for “The Last Man” and after finding no copies in the library, deletes her account.
* The librarian gets a list of all books checked out (sorted by book type/ author); they need to see at least the details of:
    * Who checked it out and when; returned date (or if it’s not returned); remaining copies

## DB3: Expanding Your Schema

{{< snippet "snippets/db-project/db3-intro.md" >}}

### Grading DB3

{{< snippet "snippets/db-project/db3-grading.md" >}}

### DB3: Library - New Features

These features likely involve revising your existing implementation.

* **Due dates for books.** When books are checked out, they will have a pre-assigned maximum lending time of two weeks. Also, if a user is overdue on a book, the user account will be locked, and no further checkouts will be allowed.
* **Multi-Library System.** The system is now covering all libraries in Monroe county. This means books can be at any of the libraries in the County System. Each library tracks it’s own count of each book and available books.
* **Lending History.** A user should be able to get a listing of their own lending history *including* their late history. Also, a librarian can get a comprehensive list of **all** late books and histories (eventually fees as well).

### Test Data

* Add Libraries for the Towns of Penfield, Fairport, Henrietta and Pittsford. Adjust your Schema to add the libraries and distribute books between the libararies.
* Checked out and returned books – a mix of data that will eventually generate late fees, including late fees beyond one week. Some specifics are provided in the test cases; add more as needed

### Key Questions

* How to track late books
* How to handle due date and returned date (changes to the schema, logic flow etc. )
* How to split up books (including tracking total and availablity at each ) between libraries but still have an overall report for ALL libraries

### Test Case Sketches
In each test scenario, peform the action(s), then validate that the DB is correctly updated after the action(s)

* A new book - the long awaited Game of Thrones installment, “The Winds of Winter”, by `George R.R. Martin` - is added to inventory with each library only having 1 copy. The follow checkouts occur @ Fairport.
    * Mary checks it out on Jan. 2nd and returns in in 8 days.
    * Ada checks it out on Jan 13th and returns it in 18 days.
        * Ada tries to check out another book 15 days after checking out “The Winds of Winter”, but her request is rejected due to the late status of her currently checked out book.
    * Jackie checks it out on March 1st and returns it in 30 days.
    * The Fairport Librarian runs a report listing overdue books per user.
* A good samaritan donates 3 additional copies of the ‘The Winds of Winter’ to Fairport.
* Another (attempted) good samaritan donates 2 copies of “The Wines of Winter” by `WineExpress` to Pittsford and Henrietta. (So many disappointed users!)
* The county librarian runs a report listing all books in all books in all libraries, organized by library location and book title with the count of books at each location

### Not Necessary (yet)

* Tracking late fees or payments on late fees

## DB4: Analytics

### Grading DB4

{{< snippet "/snippets/db-project/db4-grading.md" >}}

### DB4: Library - New Features

* **Late Returns & Fees.** There will be a late-fee charge if the book is past due. A per day penalty of $0.25 for the first week, and $2.00 per day after that. You will need to add logic to manage and calculate those fees appropriately. You will need to be able to return a report of book late fees.
* **Book Checkout Table.** Present a table style listing of each book and who has checked out the book. Make the output user friendly by have all the book info (title/ author) in ONE column in the output. e.g `The Winds of Winter by George R.R. Martin`. HINT: using the PostgreSQL command `array_agg` might help.
* **Full User Info.** User information for all activity. This includes the user name, books checked out, due dates, return dates and late fees. Organize by library, user, books and date
* **Future Plan.** Create a file called FUTURE.md in the root of your repository. Using [Markdown syntax](https://www.markdownguide.org/basic-syntax/), provide written answers to the following:
    * In the future, if we were to add the ability to have `popularity tiers` to your system, what would need to change? For example, we want more popular books to have earlier due dates (perhaps only checkout for one week). For those books, the late fees would be higher
        * What tables need changing and/or adding?
        * What API methods would you provide?
        * How might existing API methods change?
    * In the future, if we were to add the ability to have `overdue warnings` to your system, what would need to change? For example, books that are overdue by more than one week have an automatic notification sent out to the user
        * What tables need changing and/or adding?
        * What API methods would you provide?
        * How might existing API methods change?
        * What extra workflow logic would you need?

### Test Case Sketches

Use the DB-3 test scenarios for checkouts, due dates and late returns to feed the late fees data. This is the *minimum data you need, but you might want to add more!

1. When a book is returned, The “Return API” will return number of days late, and the late fee. IF there is a late fee, you will print out information as below: e.g.

'You returned your book `N` days late. You have a late fee of `$xx.yy`.' If there is no late fee, nothing prints out

2. Return and print the results of the Late Fees API, which would summarize the results for all users with checkout/ return data and late fees.

The output would be similar to the following. Your test data may vary.

```
                     book                      |      name      | checkout_date | returned_date | late_fees
-----------------------------------------------+----------------+---------------+---------------+-----------
 {"The Winds of Winter by George R.R. Martin"} | Mary Shelley   | 2022-01-02    | 2022-01-10    |       0.0
 {"The Winds of Winter by George R.R. Martin"} | Jackie Gleason | 2022-03-01    | 2022-03-31    |      21.5
 {"A Passage To India by E.M. Forster"}        | Ada Lovelace   | 2022-03-04    |               |       0.0
 {"The Winds of Winter by George R.R. Martin"} | Ada Lovelace   | 2022-01-13    | 2022-01-31    |       2.0
 {"The Famous Five by Enid Blyton"}            | Jackie Gleason | 2021-02-03    |               |       0.0
 {"Death on The Nile by Agatha Christie"}      | Mary Shelley   | 2002-01-02    |               |       0.0
```

In Python, return data from SQL would look something like this:

```python
[
  (['The Winds of Winter by George R.R. Martin'], 'Mary Shelley', '2022-01-02', '2022-01-10', Decimal('0.0')), (['The Winds of Winter by George R.R. Martin'], 'Jackie Gleason', '2022-03-01', '2022-03-31', Decimal('21.5')), (['A Passage To India by E.M. Forster'], 'Ada Lovelace', '2022-03-04', None, Decimal('0.0')), (['The Winds of Winter by George R.R. Martin'], 'Ada Lovelace', '2022-01-13', '2022-01-31', Decimal('2.0')), (['The Famous Five by Enid Blyton'], 'Jackie Gleason', '2021-02-03', None, Decimal('0.0')), (['Death on The Nile by Agatha Christie'], 'Mary Shelley', '2002-01-02', None, Decimal('0.0'))
]
```

3. Generate a report that lists each book that has been checked out, the number of days for which it was checked out, and at the end, prints the average number of days it takes for a book to be returned. Your date will vary, but the output in Python should look something like this:

```
Title                  User            Checkout        Return       Days borrowed
A Passage To India     Ada Lovelace    2022-03-04      None         None
The Winds of Winter    Ada Lovelace    2022-01-13      2022-01-31   18
The Famous Five        Jackie Gleason  2021-02-03      None         None
The Winds of Winter    Jackie Gleason  2022-03-01      2022-03-31   30
Death on The Nile      Mary Shelley    2002-01-02      None         None
The Winds of Winter    Mary Shelley    2022-01-02      2022-01-10   8

Average return time = 18.67 days
```