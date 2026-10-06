---
title: 'Course Planner'

weight: 400
bookToC: true
bookSearchExclude: false

draft: true
---

## Course Planner

### Overview

For this assignment, you will build a web application using HTML, CSS and Javascript for planning a semester schedule. Courses are grouped by academic department, and you will build up a schedule by picking courses from one or more departments. Your app will keep track of the total credit hours in the schedule, and warn the student when they are under a full-time load or overloaded.

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
* Add a dropdown list control with the following values (names of the departments)
    * Software Engineering
    * Computer Science
    * Mathematics
    * Physics
    * Humanities
    * Use the `<select>` tag with those options. There should also be a default selection with an empty value (blank). Make sure it is a dropdown list as shown in the sample below.
* Add a set of radio buttons with the following values (the term)
    * Fall
    * Spring
    * Summer
    * Use the `<input type="radio">` tag. Make sure the radio buttons operate as a group. i.e. only one item can be selected at a time!
* Add a box that displays the details of each department. Each time the dropdown selection changes, the details of the department will change in this box. The details will include:
    * Department Name (This will be the same name selected in the dropdown)
    * Department Code (e.g. SWEN, CSCI, MATH …)
    * Building (pick a building name or number … your choice)
    * The selected term from the radio buttons (e.g. “Courses offered for: Fall”)
* Also, provide a different background colour for each department, and make sure the font color is also adjusted to make the text readable with the background - perhaps Software Engineering is orange background with black text; Physics is navy background with white text.
* Add a small javascript function (place in `<HEAD>` ) that will control changing the information in the box based on the value of the selection of the SELECT tag, and the currently selected term. The box should update when *either* control changes.
    * HINT: Use the `onchange` event, `document.getElementById` and `document.getElementsByName`
* Use style attributes to adjust the position of controls (so everything is not at the edges or clustered together). Look at the sample page and try to get as close as possible to the centering and left/ right/ top/ bottom positioning shown in the screen-shot. NOTE: In part-0, we want you to put the styles in-line, and not use CSS. That will come later.

### Grading

Grading: 10 points total

Web Page and Actions:

* Title & Headings: 1 point
* Dropdown list: 1 points
* Radio buttons: 1 points
* Display box: 1 points
* Layout & Alignment: 2
* Javascript dynamic updates: 4

### Sample Output

None! This is a new project. Do your best to come up with a reasonable UI-- maybe yours will be featured here in the future (with your permission, of course).

## Client 1

### Setup

{{< snippet "/snippets/client-project/client1-setup.md" >}}

### Client-1 Initial UI

Your app maintains a list of courses for 5 departments. For each department, there is a list of 5 courses in that department. A user can select a course and add it to their schedule. As each course is added (or removed), the total credit hours for the schedule are summarized, and colour coded according to the student’s load.

The following are the behaviour expectations

* Set the default font to ‘Segoe UI’ Add a title using the `<h1>` tag, centred, to the top of the page with the following text ‘Course Planner’
* Add a sub-heading, `<h3>`, centred, with the following text `Pick courses from each department to build your semester schedule`
* Use a pull-down menu with a `<select>` with options for the following departments - “Software Engineering”, “Computer Science”, “Mathematics”, “Physics”, “Humanities”
* Provide a listbox that can hold 5 items, and as a department is selected, populate the listbox with the matching courses.
    * Each course also has a number of credit hours associated with it. The data is shown below. You will need to decide how to represent that data in your code. Remember, there is no database (yet), so you need to keep this all client side!

|Software Engineering|Credits|
|---|---|
|SWEN-601 Software Construction|3|
|SWEN-610 Foundations of Software Engineering|3|
|SWEN-640 Research Methods|3|
|SWEN-732 Collaborative Software Development|3|
|SWEN-790 Capstone|6|

|Computer Science|Credits|
|---|---|
|CSCI-605 Advanced OO Programming|3|
|CSCI-620 Data Management|3|
|CSCI-630 Artificial Intelligence|3|
|CSCI-661 Data Structures|3|
|CSCI-698 Seminar|1|

|Mathematics|Credits|
|---|---|
|MATH-181 Calculus I|4|
|MATH-182 Calculus II|4|
|MATH-190 Discrete Mathematics|3|
|MATH-241 Linear Algebra|3|
|MATH-251 Probability and Statistics|3|

|Physics|Credits|
|---|---|
|PHYS-211 University Physics I|4|
|PHYS-212 University Physics II|4|
|PHYS-206 Physics Lab|1|
|PHYS-220 Astronomy|3|
|PHYS-225 Modern Physics|3|

|Humanities|Credits|
|---|---|
|ENGL-150 Writing Seminar|3|
|PHIL-102 Ethics|3|
|HIST-160 World History|3|
|ARTH-135 Art History|3|
|MUSC-110 Music Appreciation|2|

There will be a second list box, for the student’s schedule. Between the two list boxes, there will be a button to add/ remove courses from the schedule. Once the courses for the department are populated, the user can click the button to add a course to the schedule.

* When you click a course in the first listbox (with the courses you can add), the button will be configured to **add** the selected course to your schedule.
* When you click a course in your schedule, the button will be configured to **remove** the course from the schedule.
* The course that is added will appear in the schedule. As the course is added, the total credit hours (total of all courses in the schedule) will be shown below the schedule.
* A course can be removed from the schedule, and the credit total will also update when a course is removed.
* The schedule can hold any number of courses, and can be a mix of courses from all departments. However, **a course can only appear in the schedule once**. If the user tries to add a course that is already scheduled, do not add it again (you may disable the button, or show a message - your choice).

Credit load display behaviour: (Show the total with the indicated colour, and the corresponding message)

* If the total credit hours are < 12, the total will be yellow: `Part-time`
* If the total credit hours are >= 12 and <= 18, the total will be green: `Full-time`
* If the total credit hours are > 18, the total will be red: `Overload - advisor approval required!`

Place all the styles for the different display elements in your `.css` file. Place all your javascript code in your `.js` file.

Use the screenshots below as an additional reference for your web page design requirements.

### Grading

Points: 40 points total

Web Page and Actions:

* Pulldown menu selection: 5 points
* Populating listboxes: 5
* Headings, Layout & Alignment: 5
* Good coding style of css: 5
* Good coding style of javascript: 5
* Following all requirements: 15

### Sample Output

UI Specifications are often page mockups. Your page should be similar to this…

<!-- TODO: add sample screenshots (course-1.0.JPG add course, course-1.1.JPG remove course, course-1.2.JPG overload) -->

## Client-2: Port to React

{{< snippet "/snippets/client-project/client2-overview.md" >}}

### Getting Started with React

{{< snippet "/snippets/client-project/client2-getting-started.md" >}}

### Porting to React

{{< snippet "/snippets/client-project/client2-porting.md" >}}

### Course Planner Specifics

* Your state will likely be a collection of the courses and which ones are in the schedule.
* Your state would also likely need to store which department is currently selected. Perhaps that needs to be in the top-level state, or perhaps that’s a state variable for the component representing the course list.
* You DON’T need to store the total credits or the load colour - those react to the state and are computed when rendering.

### Grading Client-2

{{< snippet "/snippets/client-project/client2-grading.md" >}}

## Client-3: Responsive Design, New Features

{{< snippet "/snippets/client-project/client3-overview.md" >}}

### Client 3 setup

{{< snippet "/snippets/client-project/client3-setup.md" >}}

### Responsive Design

{{< snippet "/snippets/client-project/client3-responsive-design.md" >}}

### Course Planner Specifics

* Make the **add** and **remove** two separate buttons for the schedule.
* Add **additional course information**, including the instructor, meeting days (e.g. Mon/ Wed), start time, end time, and seats available. Assume this data is pre-loaded, as before. Make up reasonable values for your built-in data. Adapt for your code as needed.
* Add a **simple weekly calendar view** of the schedule (days across the top, times down the side), showing each scheduled course in its time slot(s).
* Detect **time conflicts**. If two scheduled courses meet on the same day at overlapping times, show a visual indicator (e.g. colors, icons, visual cues) on both courses, in both the schedule list and the calendar view.
* Provide a way to edit a course’s information. Populate the existing values in the edit dialog so it is easy to modify.
* Use pop-up Reactstrap Modal dialogs to make data-entry easy
* Add a **degree credit goal** and show the progress toward that, counting the scheduled credits plus credits already completed. We recommend the [Progress Bar](https://reactstrap.github.io/?path=/docs/components-progress--progress) as a simple way to represent this. Default to 30 credits required and 0 completed, but allow people to change both numbers.
* Improve the UI according keeping in mind UI design guidlines, simplifying as best you can.

### Grading Client-3

Points: 60 points total

* (5 points) Builds on the CI by Lab Day
* (5 points) Quality feedback given
* (5 points) 2 buttons for add/ remove
* (5 points) Additional course information loaded, with edit capability
* (15 points) Responsive features and guidelines
* (15 points) Weekly calendar view with time conflict indicators
* (10 points) Degree credit goal progress bar, with edit capability for goal

## Client-4: Full Stack

{{< snippet "/snippets/client-project/client4-overview.md" >}}

### Setup

{{< snippet "/snippets/client-project/client4-setup.md" >}}

### Items to Note

{{< snippet "/snippets/client-project/client4-note.md" >}}

### Running the FastAPI server

{{< snippet "/snippets/client-project/client4-fastapi-server.md" >}}

### Running your React Client code

{{< snippet "/snippets/client-project/client4-react-client.md" >}}

### CI/ gitlab pages

{{< snippet "/snippets/client-project/client4-gitlab.md" >}}

### Course Planner Specifics

* Keep the initial department and course data in a DB on the ‘server side’. Include all information in a table/ tables of your own design. When your page loads, it should get the data from the server. Provide a `GET` API to retrieve that data. Provide any filters necessary for your design for retrieving data (e.g. courses by department).
* Allow the ability to modify information for existing courses, update the DB accordingly (`PUT` API)
* Add the ability to create new courses in an existing department (`POST` API)
* Add the ability to delete a particular course (`DELETE` API). If the course is in the current schedule, remove it from the schedule as well.
* Keep the degree credit goal and completed credits in the DB, so they persist between visits. Update them with a `PUT` API.
* Keep the functionality for moving courses between the department list and the schedule, but you do not need to update the DB with that information, since it is transitory information.
* In all cases, the web page should be displaying the data it retrieves from the DB using the REST API
    * Don’t leave modified data that should be committed to the DB cached in browser/ client data
    * When the user is filling in a form for new or modified data, don’t commit the data to the DB until the user confirms the changes. Make sure the user can cancel the action if they choose to do so.

### Grading Client-4

Points: 60 points total

* (5 points) Builds on the CI by Lab Day
* (5 points) Quality feedback given
* (10 points) FastAPI server and DB properly setup and initialized
* (10 points) API to load initial data on client (GET)
* (20 points) API to add/ modify data (POST/ PUT)
* (10 points) API to delete data (DELETE)
