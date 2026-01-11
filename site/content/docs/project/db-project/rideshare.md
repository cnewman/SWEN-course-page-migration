---
title: 'Rideshare'

weight: 200
bookToC: true
bookSearchExclude: false

draft: true
---

## DB Project - Rideshare System

You are a tech company that provides a mobile app for booking taxi rides (e.g. Uber, Lyft). Both drivers and riders use this app and this is the database that stores the records of those trips. The app also has a reviewing system where both drivers and riders review each other.

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

The main stakeholders in this system are riders and drivers. Riders have a name, any special instructions, and a current average rating. Drivers also have those things, as well as a car make and model and the system knows how long they have been a driver. Also, drivers will likely need to provide their license number, but riders will not. Riders will need to provide their credit card number, but drivers will not! Use this thought process to design your tables and the relationships.

The core of your system is the concept of a rider and driver taking a ride. Make these tables with test data. Don’t worry about too many other columns than what you need for these tests. At this point, you can load your test data with any mechanism you choose. We’ll get to loading from files later (although you can do that here as well, if you choose)

### Key Decisions

* One table or multiple tables for drivers and riders? What if a driver also wants to take a ride? Should they have separate accounts?
* How should we handle return trips? Trips with multiple stops?

### Test Data Seeds

You will need to maintain a set of test data to seed your database before every test. This can take multiple forms, such as:

* An SQL file with many `INSERT` statements
* A hardcoded Python file calling SQL commands

How you do this is up to you. You are welcome to add more data than what we ask for any tests you write.

For DB1, you need the following data:

* Drivers “Tom Magliozzi” and “Ray Magliozzi”. Their average ratings are 3.2 and 3.4 respectively. Both of their special instructions are “Don’t drive like my brother.”
* A rider named “Mike Easter” whose average rating is 4.3. No special instructions.
* Tom gave a ride to Mike
* Ray gave a ride to Mike
* Tom gave a ride to Ray

### Test Case Sketches

* The database is seeded with a test data set without crashing
* When we list the rides that Tom gave, they include the rides to Mike and Ray
* When we list the rides that Mike took, they include the rides from Tom and Ray
* When we list the rides that Mike gave, we get no results
* When Tom checks his rating, it returns the correct value

{{% hint warning %}}

**Note:** make sure your tests are actually making calls to your API, not just directly to the database. Data will be seeded directly, but your tests will be testing the API. For example, there should be an API method for “When we list the rides…” and we call that.

{{% /hint %}}

### Not Necessary (yet)

For this iteration, you do NOT need to implement:

* An API call for creating new accounts
* Handling data related to location (e.g. GPS, region)
* An API call for creating a new ride

You may need to do these in the future, but for now just test against your pre-seeded test data.

## DB2: CRUD & Design Iteration

{{< snippet "snippets/db-project/db2-intro.md" >}}

### Grading DB2

{{< snippet "snippets/db-project/db2-grading.md" >}}

### DB2 Ride Sharing requirements

In this iteration, you will add:

* Create new accounts for a riders and drivers
* Riders and drivers should be able to modify their account information
* A rider and driver should be able to “remove” their account
    * Does this mean disabling or deleting? Consider this choice and implement accordingly.
* Record a new ride.
    * A ride will always have a driver and rider, a starting point, and a destination
    * Add information to rides: destination (in GPS coordinates, i.e. two numbers, latitude and longitude), special ride instructions, time information
    * Add the concept of reviews for a ride. Driver gets to review a rider on a particular ride, and vice versa.
* Availability.
    * A rider should be able to mark themselves as “available”. When this happens, assume that the client (e.g. phone) will automatically supply a zip code based on the rider’s location
    * A driver should be able to see all available riders in a given zip code, along with their GPS coordinates and average rating. (Presumably, a separate mapping service would translate those GPS coordinates into distances - we won’t do this)
* When a ride is arranged, but before pickup, both the rider and driver should be able to update their own location and see the location of the other.
* Load test data from the provided [csv file](/data/rideshare.csv) and add to the database.
    * This contains a set of information on riders and drivers
    * NOTE: You will need to read the data and figure out a way to make it fit according to your DB schema (or do you need to modify your schema?). There can be inconsistencies in data format, some stray characters etc. Think through how to import this data using code, in a consistent, logical way.

### Key Questions

* Should average rating be computed every time we make an API call? Or store it somewhere?
* Deactivate or delete accounts?
* What information is necessary for representing a “ride”?
* What is the flow of states for a ride? For example, what if a ride was booked but never happened?
* The availability feature will get a lot more traffic than profile editing - do we need to account for this?

{{< snippet "/snippets/db-project/timestamps-and-apis.md" >}}

### Test Case Sketches

* Tom and Ray Magliozzi updated their profiles (perform an update, and then verify the data is updated)
* Hoke Colburn and Ms. Daisy signed up for accounts
* Ms. Daisy marks herself as available in zip code 30301 on December 13, 11:55am
* Hoke Colburn gets a listing of available fares and can see Ms. Daisy in his availability in zip code 30301
* Ms. Daisy is able to see all drivers in zip code 30301.
* Hoke Colburn drove Ms. Daisy to a location on December 13, 1989 at 12:00pm.
* Both Ms. Daisy and Hoke rated each other for the ride
* Tom Magliozzi drove Hoke Colburn to a location on December 14, 1989 at 4:00pm
* Ms. Daisy, after all of this, is able to remove her account.

### Not Necessary

PostgreSQL has some functionality for geo-spatial information systems (GIS), called [PostGIS](https://postgis.net/) You don’t need to use this extension. But, it is worth your time to look into the features they offer.

## DB3: Expanding Your Schema

{{< snippet "/snippets/db-project/db3-intro.md" >}}

### Grading DB3

{{< snippet "/snippets/db-project/db3-grading.md" >}}

### DB3 Rideshare: New Features

These features likely involve revising your existing implementation.

* **Carpooling to a single destination.** A ride can have one driver, but multiple passengers now. A driver can keep themselves marked as “available” to pick up other riders. A ride will always have a single destination. Billing is split evenly across all riders.
* **Responding to Reviews.** A driver and a rider can write a single written review for a given ride (as before in DB2), and now can also write one response to each other’s reviews. That is, a rider can respond to a driver’s review, and a driver can respond to a rider’s review. When reviews are obtained from the API, all relevant responses are returned too.
* **Receipts.** A rider should be able to get a listing of their finished rides for a given date range, along with an accurate total of the money they have spent.

### Test Data

* Add users Alex, Bobby, Louie, Elaine, and Tony. They are all drivers.

### Test Case Sketches

In each test scenario, peform the action(s), then validate that the DB is correctly updated after the action(s)

* Godot (driver) marks himself available. Vladimir (rider) accepts the ride. Godot never shows up. No receipt is generated for either user.
* Alex picks up Bobby to go to the airport for $12.
    * On the way, he picks up Louie, then Elaine, then Tony.
    * The receipts for each of the four riders is now $3.
    * Louie gives the ride a 2 and writes a bad review. Bobby reviews the ride and gives it a 5 with a good review. Alex responds to Louie’s review, but not Bobby’s.
* Tony gives a ride to Alex. Elaine joins the ride. Tony then marks himself as unavailable. A search for available drivers shows does not include Tony.

### Not Necessary

* In carpooling, a rider does not need to be able to respond to the review of another rider, just to the driver’s review.

## DB4: Analytics

### Grading DB4

{{< snippet "/snippets/db-project/db4-grading.md" >}}

### DB4 Rideshare: New Features

* **Full Ride Info.** Aggregate rider information for all rides within 1 day of a given date. This includes the ride, and then a list of riders. (Tip: you may find functions like `array_agg` might help to create an array from a group). Provide an average rating of the ride across all riders.
* **Fare Times.** We want to be able to study the fares charged at different times of day. Provide a summary of hours and average fares. Hint: To get the hour part from a timestamp called `metime` in Postgresql, the syntax looks like `EXTRACT(HOUR FROM metime)`. If an hour had no rides, then it does not need a row.
* **Future Plan.** Create a file called FUTURE.md in the root of your repository. Using [Markdown syntax](https://www.markdownguide.org/basic-syntax/), provide written answers to the following:
    * In the future, if we were to add the ability to have `surge pricing` to your system, what would need to change? For example, maybe we want to surge the prices when there’s a lot of demand in one area at one time
        * What tables need changing and/or adding?
        * What API methods would you provide?
        * How might existing API methods change?
    * In the future, if we were to add the ability to have `future scheduling` to your system, what would need to change? For example, a user would want to book a fare several days in advance
        * What tables need changing and/or adding?
        * What API methods would you provide?
        * How might existing API methods change?

### Test Case Sketches

1. Return and print the results of the Full Ride Info API A Full Ride Info call might look something like this. Your test data may vary.

|`driver`|`dest_lat`|`dest_long`|`riders`|`avg_rating`|
|---|---|---|---|---|
|Alex|123.0|456.0|`['Bobby', 'Louie', 'Elaine', 'Tony']`|3.5|
|Godot|1234.0|3456.0|`['Vladimir']`|0|

In Python, that would look like:

```python
[

  { 'driver': 'Alex', 'dest_lat': 123.0, 'dest_long': 456.0, 'riders': ['Bobby', 'Louie', 'Elaine', 'Tony'], 'avg_rating': 3.5 },
  { 'driver': 'Godot', 'dest_lat': 1234.0, 'dest_long': 3456.0, 'riders': ['Vladimir'], 'avg_rating': 0 },

]
```

2. Return and print the results of the Fare Times API. For Fare Times, say you only had rides at 3:30pm for $5, 4:55pm for $10, and 4:59pm for $20, then your output would look like this:

|`Hour`|`Ab. Fare`|
|---|---|
|3|5.0|
|4|15.0|

```python
[[3, 5.0],
 [4, 15.0]]
```