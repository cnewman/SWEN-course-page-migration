---
title: 'Nightclub'

weight: 200
bookToC: true
bookSearchExclude: false

draft: true
---

## Nightclub Capacity App

### Overview

For this assignment, you will build a web applications using HTML, CSS and Javascript for monitoring people entering and leaving your chain of nightclubs. You’ve been very successful in attracting crowds, but the fire-marshal has warned you about overcrowding. You are strictly monitoring people entering/ leaving, and bouncers at the door will track and control entry using the capacity app.

## Client-0 Startup: Your first web page

### Overview and Setup

See the [client project](/docs/project/client-project) introduction page for the Client-0 overview and setup instructions.

### Requirements

You will implement the following controls and behaviour for your webpage (`index.html`)

* Set the default font to ‘Segoe UI’
* Add a title using the `<h1>` tag to the top of the page with the following text ‘swen-610 Web Assignment 0’
    * Set the font to Bold, Verdana, with a size of 36px (Use the style property) and centre the title.
* Add a sub-heading with the `<h2>` tag.
    * The text content should be ‘Section xx’, where ‘xx’ is your section number. Set the font to “Impact”
    * Center the text.
* Add a dropdown list control with the following values (names of the nightclubs)
    * Club Arcane
    * Club Underground
    * Club Soda
    * Studio 52
    * Ciro’s
    * Paradisio Garage
    * Use the `<select>` tag with those options. There should also be a default selection with an empty value (blank). Make sure it is a dropdown list as shown in the sample below.
* Add a box that displays the details of each club. Each time the listbox selection changes, the details of the club will change in this box. The details will include:
    * Club Name (This will be the same name selected in the listbox)
    * Club Location (Which city) Capacity (pick a variety of capacities … your choice)
    * Musical Theme (Rock, Pop, Metal, Grunge …)
* Also, provide a different background colour for each Club, and make sure the font color is also adjusted to make the text readable with the background - perhaps Club Arcane is black background with white font; Club Soda is white background with black text.
* Add a small javascript function (place in `<HEAD>` ) that will control changing the information in the box based on the value of the selection of the SELECT tag (e.g. Club Arcane, Club Underground, …). 
    * HINT: Use the `onchange` event and `document.getElementById`
* Use style attributes to adjust the position of controls (so everything is not at the edges or clustered together). Look at the sample page and try to get as close as possible to the centering and left/ right/ top/ bottom positioning shown in the screen-shot. NOTE: In part-0, we want you to put the styles in-line, and not use CSS. That will come later.

### Grading

Grading: 10 points total

Web Page and Actions:

* Title & Headings: 1 point
* Dropdown list: 1 points
* Display box: 1 points
* Layout & Alignment: 2
* Javascript dynamic updates: 5

### Sample Output

UI Specifications are often page mockups. Your page should be similar to this…

#### Sample Page 1

![nightclub 0 sample 1](/images/client-project/club-0.0.JPG)

#### Sample Page 2

![nightclub 0 sample 2](/images/client-project/club-0.1.JPG)

#### Sample Page 3

![nightclub 0 sample 3](/images/client-project/club-0.2.JPG)

## Client 1

### Setup

{{< snippet "/snippets/client-project/client1-setup.md" >}}

### Initial UI

Your app tracks 4 clubs. Each club starts at occupancy of zero, and each time someone enters or leaves a club, the occupancy is updated. There are 3 levels of occupancy, and a colour coding (shown below) that corresponds to each level.

* Normal
* Yellow Threshold/ Caution
* Max Club Capacity

The following are the behaviour expectations: 

* Set the default font to ‘Segoe UI’ Add a title using the `<h1>` tag, centred, to the top of the page with the following text ‘Nightclub Capacity’
* Add a sub-heading, `<h3>`, centred, with the following text: `Each time someone enters/ leaves the club, select the correct club and click the appropriate button`

Use an appropriate HTML element to display each of the 4 clubs

* Each “club” will have rounded corners (HINT: use `border-radius`)
* Below each club will be the count for the respective club

Below the element displaying the club(s) and occupancy count there will be a set of radio buttons for each of the clubs, labelled with the club name/ location. Because of club layout, the capacities for each club vary. See below for the data-set describing that relationship. Remember, there is no database (yet), so you need to decide how to represent this data in your web code.

|Club number|Club name|Max Capacity|Yellow Threshold|
|---|---|---|---|
|1|Club Arcane|100|70|
|2|Club Underground|50|30|
|3|Club Soda|20|12|
|4|Studio 52|52|32|

Below the radio buttons, there will be two buttons, labelled + and -.

* When + is clicked, the count for the selected club will increment.
* When - is clicked, the count for the selected club will decrement.
* For each change in count, the colour of the club will change as described below.
* For each change in count/ color, there will be a message in the club display element as stated below

Club occupancy display behaviour:

* If the club occupancy count is < Yellow threshold, the club display will be green
* If the club occupancy count is >= Yellow Threshold and < Max capacity, the club display will be yellow
* If the club occupancy count is >= Max Capacity, the club display will be red.

Messaging behaviour: (Show the corresponding message with the indicated colour)

* Green: Welcome!
* Yellow: Warn the bouncers…
* Red: No one allowed in!

Place all the styles for the different display elements in your `.css` file. Place all your javascript code in your `.js` file.

Use the screenshots below as an additional reference for your web page design requirements.

### Grading Client-1

Points: 40 points total

Web Page and Actions:

* Radio buttons: 5 points
* Clubs: 5
* Headings, Layout & Alignment: 5
* Good coding style of css: 5
* Good coding style of javascript: 5
* Following all requirements: 15

### Sample Output

UI Specifications are often page mockups. Your page should be similar to this…

#### Sample Page 1

![nightclub 1 sample 1](/images/client-project/club-1.0.JPG)

#### Sample Page 2

![nightclub 1 sample 2](/images/client-project/club-1.1.JPG)

## Client-2: Port to React

{{< snippet "/snippets/client-project/client2-overview.md" >}}

### Getting Started with React

{{< snippet "/snippets/client-project/client2-getting-started.md" >}}

### Porting to React

{{< snippet "/snippets/client-project/client2-porting.md" >}}

### Nightclub Specifics

* Your state will likely be a mapping of clubs and counts.
* You DON’T need to store the color information - it reacts to the state and renders a color based on the counts.

### Grading Client-2

{{< snippet "/snippets/client-project/client2-grading.md" >}}

## Client-3: Responsive Design, New Features

{{< snippet "/snippets/client-project/client3-overview.md" >}}

### Client 3 setup

{{< snippet "/snippets/client-project/client3-setup.md" >}}

### Responsive Design

{{< snippet "/snippets/client-project/client3-responsive-design.md" >}}

### Nightclub Specifics

* Let’s change the UI to not have radio buttons anymore. Instead, each club will have it’s own increment/ decrement buttons.
* Make the button to **decrement** and button to **increment**, are well separated (increment in the left corner, decrement in the right corner). Also, make the colours for each distinct, for easy user recognition (you can pick the colours)
    * Once the max capacity has been reached, disable the `increment` button. When the count goes below max capacity, re-enable the `increment` button.
    * Disable the `decrement` button if the count is zero; enable it when it is higher.
* Add information for the club. Name, Location (city), music genre (rock, pop, synth, metal, …). Show this in the display for each club.
* Add the ability to **change the information** of a given club. This includes the name, location, etc. and thresholds (all valid fields, basically). Popupate the existing values in the edit dialog so it is easy to modify.
* Add the ability to **add new clubs and remove any clubs**, including all of the information above.
    * Start off with a few initial clubs, and then allow add/ remove operations.
    * **Provide defaults** for the threshholds when adding a new club. Make the values realistic e.g. - If Max Capacity is 100, Yellow threshold is 80% of the capacity. Allow the user to override the defaults when they add a club. Obviously, the user will need to enter all other values.
    * Provide a simple way to delete a given club i.e. don’t force the user to type!
* Use pop-up Reactstrap Modal dialogs to make data-entry easy
* **Filter** which clubs are shown by their location city, e.g. “NYC or London …".

### Grading Client-3

Points: 60 points total

* (5 points) Builds on the CI by Lab Day
* (5 points) Quality feedback given
* (10 points) Decrement and increment buttons
* (15 points) Responsive features and guidelines
* (15 points) Club information and CRUD operations
* (10 points) Filter clubs

## Client-4: Full Stack

{{< snippet"/snippets/client-project/client4-overview.md" >}}

### Setup

{{< snippet"/snippets/client-project/client4-setup.md" >}}

### Items to Note

{{< snippet"/snippets/client-project/client4-note.md" >}}

### Running the Flask server

{{< snippet"/snippets/client-project/client4-flask-server.md" >}}

### Running your React Client code

{{< snippet"/snippets/client-project/client4-react-client.md" >}}

### CI/ gitlab pages

{{< snippet"/snippets/client-project/client4-gitlab.md" >}}

### Nightclub Specifics

* Keep the initial data in a DB on the ‘server side’. Include all information in a table/ tables of your own design. When your page loads, it should get the data from the server. Provide a `GET` API to retrieve that data.
* When the button changes the current count, the code will update the data on the server (`PUT`), and the client code will retrieve the updated data from the server to refresh the display. Provide an appropriate API(s).
* When you change the club information (name, location … ), use `PUT` to update the data on the server.
* Provide a `POST` method to create a new club, and a `DELETE` method to remove a club. Update the DB accordingly.
* In all cases, the web page should be displaying the data it retrieves from the DB using the REST API
    * Don’t leave modified data that should be committed to the DB cached in browser/ client data
    * When the user is filling in a form for new or modified data, don’t commit the data to the DB until the user confirms the changes. Make sure the user can cancel the action if they choose to do so.

### Grading Client-4

Points: 60 points total

* (5 points) Builds on the CI by Lab Day
* (5 points) Quality feedback given
* (10 points) Flask server and DB properly setup and initialized
* (10 points) API to load initial data on client (GET)
* (20 points) API to add/ modify data (POST/ PUT)
* (10 points) API to delete data (DELETE)