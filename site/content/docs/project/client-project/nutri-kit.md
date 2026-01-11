---
title: 'Nutri-Kit'

weight: 300
bookToC: true
bookSearchExclude: false

draft: true
---

## Nutri-Kit

### Overview

For this assignment, you will build a web applications using HTML, CSS and Javascript for managing food items and viewing/ tracking nutriional information. Food items will be in groups according to the nutritional pyramid, and you will build up a menu using one or more items from the various food groups.

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
* Add a pull down menu (drop-down list) control with the following values
    * Protein
    * Carbs
    * Fruits
    * Vegetables
    * Sweets
    * Use the `<select>` tag with those options. There should also be a default selection with an empty value (blank). Make sure it is a dropdown list as shown in the sample below.
* Add a set of radio buttons with the following values
    * Black
    * Red
    * Green
    * Blue
    * Purple
    * Use the `<input type="radio">` tag. Make sure the radio buttons operate as a group. i.e. only one item can be selected at a time!
* Add a text box (textarea) control with the following initial content `Food Type...`
    * Use the `<textarea>` tag. Set the control to be non-expandable. Set the rows and columns properties to your liking. Position the control in the center.
* Add a small javascript function (place in `<HEAD>` ) that will reflect the value of the selection of the SELECT tag (e.g. Protein, Fruit, Vegetables, …) as soon as the value changes. Use the value of the current selection in the radio button group to set the colour of the font in the text area.
    * HINT: Use the `onchange` event and `document.getElementByName` or get clever with onclick and pass in the object instance
* Use style attributes to adjust the position of controls (so everything is not at the edges or clustered together). Look at the sample page and try to get as close as possible to the centering and left/ right/ top/ bottom positioning shown in the screen-shot. NOTE: In part-0, we want you to put the styles in-line, and not use CSS. That will come later.

### Grading

Grading: 10 points total

Web Page and Actions:

* Title & Headings: 1 point
* Listbox creation: 1 points
* Radio button: 1 points
* Textarea creation (with resizing disabled): 1
* Layout & Alignment: 2
* Javascript dynamic updates: 4

### Sample Output

UI Specifications are often page mockups. Your page should be similar to this…

#### Sample Page 1

![Sample Page 1](/images/client-project/nutrikit-0.0.JPG)

#### Sample Page 2

![Sample Page 2](/images/client-project/nutrikit-0.1.JPG)

#### Sample Page 3

![Sample Page 3](/images/client-project/nutrikit-0.2.JPG)

## Client 1

### Setup

{{< snippet "/snippets/client-project/client1-setup.md" >}}

### Client-1 Initial UI

Your app maintains a list of grocery items for basic food groups [proteins, fruits, vegetables, grains, dairy]. For each category, there is a list of 5 items in that category. A user can select an item an add to there selection list/ menu. As each item is added (or removed), the total calories fo the selection list (menu) is summarized.

The following are the behaviour expectations
* Set the default font to ‘Segoe UI’ Add a title using the `<h1>` tag, centred, to the top of the page with the following text ‘NutriKit Food Planner’
* Add a sub-heading, `<h3>`, centred, with the following text `NutriKit allows you to select your groceries, and track your nutritional progress (good or bad)`
* Use a pull-down menu with a `<select>` with options for the following food categories - “proteins”, “fruits”, “vegetables”, “dairy”, “grain”
* Provide a listbox that can hold 5 items, and as a food category is selected, populate the listbox with the matching food items. 
    * Each food item also has a calorie count associated with it. The data is shown below. You will need to decide how to represent that data in your code. Remember, there is no database (yet), so you need to keep this all client side!

|Proteins|Calories|
|---|---|
|steak|300|
|ground beef|200|
|chicken|100|
|fish|80|
|soy|50|

|Fruits|Calories|
|---|---|
|orange|300|
|banana|200|
|pineapple|100|
|grapes|80|
|blueberries|50|

|Vegetables|Calories|
|---|---|
|romaine|30|
|green beans|40|
|squash|100|
|spinach|50|
|kale|10|

|Dairy|Calories|
|---|---|
|milk|300|
|yoghurt|200|
|cheddar cheese|200|
|skim milk|100|
|cottage cheese|80|

|Grains|Calories|
|---|---|
|bread|200|
|bagel|300|
|pita|250|
|naan|210|
|tortilla|120|

There will be a second list box, for the user selections. Between the two list boxes, there will be a button to add/ remove items from the selection list. Once the items for the category are populated, the user can click the button to add an item to the selection list.

* When you click an item in the first listbox (with the items you can add), the button will be configured to **add** the selected item to your selection list.
* When you click an item in your selection list, the button will be configured to **remove** an item from the selection list.
* The item that is added will appear in the selection list. As the item is added, the total amount of calories (total of all items in the selected items list) will be shown below the selection list.
* An item can be removed from the selected items list, and the calorie count will also update when an item is removed.
* The selected items list can hold any number of items, and can be a mix of items from all categories.

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

#### Sample Page 1

![nutrikit 1 sample 1](/images/client-project/nutrikit-1.0.jpg)

#### Sample Page 2: Add Item

![nutrikit 1 sample 2](/images/client-project/nutrikit-1.1.jpg)

#### Sample Page 3: Remove Item

![nutrikit 1 sample 3](/images/client-project/nutrikit-1.2.png)

## Client-2: Port to React

{{< snippet "/snippets/client-project/client2-overview.md" >}}

### Getting Started with React

{{< snippet "/snippets/client-project/client2-getting-started.md" >}}

### Porting to React

{{< snippet "/snippets/client-project/client2-porting.md" >}}

### Nutrikit Specifics

* Your state will likely be a collection of what the menu items are and which ones are selected.
* Your state would also likely need to store which category is currently selected. Perhaps that needs to be in the top-level state, or perhaps that’s a state variable for the component representing the menu

### Grading Client-2

{{< snippet "/snippets/client-project/client2-grading.md" >}}

## Client-3: Responsive Design, New Features

{{< snippet "/snippets/client-project/client3-overview.md" >}}

### Client 3 setup

{{< snippet "/snippets/client-project/client3-setup.md" >}}

### Responsive Design

{{< snippet "/snippets/client-project/client3-responsive-design.md" >}}

### Nutrikit Specifics

* Make the **add** and **remove** two separate buttons for “Selected Items”
* Add **additional nutrition information** to menu items, including total fat, saturated fat, trans fat, protein, and total carbohydrate. Assume this data is pre-loaded, as before. Look up reasonable examples for your built-in data. You can also use this for [starter data](/data/foods.json) if you wish. Adapt for your code as needed.
* Add a **simple visual representation** of both a single food and the total, similar to a [standard nutrition label](https://www.fda.gov/food/nutrition-facts-label/how-understand-and-use-nutrition-facts-label). Add visual indicators (e.g. colors, icons, visual cues) for when the value is considered “high” (according to the FDA guidelines we just linked to).
* Provide a way to edit a food item’s values.
* Use pop-up Reactstrap Modal dialogs to make data-entry easy
* Add a **total calorie goal** and show the progress toward that. We recommend the [Progress Bar](https://reactstrap.github.io/?path=/docs/components-progress--progress) as a simple way to represent this. Default to 2,000 calories but allow people to change this number for them.
* Improve the UI according keeping in mind UI design guidlines, simplifying as best you can.

### Grading Client-3

Points: 60 points total

* (5 points) Builds on the CI by Lab Day
* (5 points) Quality feedback given
* (5 points) 2 buttons for add/ remove
* (5 points) Additional nutrition information loaded
* (15 points) Responsive features and guidelines
* (15 points) Aggregated nutrition label; single food item **and** total with visual indicators
* (10 points) Total calorie goal progress bar, with edit capability for goal

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

### Nutrikit Specifics

* Keep the initial nutritional data in a DB on the ‘server side’. Include all information in a table/ tables of your own design. When your page loads, it should get the data from the server. Provide a `GET` API to retrive that data. Provide any filters necessary for your design for retrieving data.
* Allow the ability to modify nutrition data for existing food items, update the DB accordingly (`PUT` API)
* Add the ability to create new food items in an existing category (`POST` API)
* Add the ability to delete a particular food item (`DELETE` API)
* Add the ability to have goals for each nutritional value.
* Provide a progress bar to show status vs. the goal for each nutritional value
* Keep the functionality for moving items between the category items list and selected items list, but you do not need to update the DB with that information, since it is transitory information.
* In all cases, the web page should be displaying the data it retrieves from the DB using the REST API
    * Don’t leave modified data that should be committed to the DB cached in browser/ client data
    * When the user is filling in a form for new or modified data, don’t commit the data to the DB until the user confirms the changes. Make sure the user can cancel the action if they choose to do so.

### Grading Client-4

Points: 60 points total

* (5 points) Builds on the CI by Lab Day
* (5 points) Quality feedback given
* (10 points) All prior functionality still works!
* (5 points) Flask server and DB properly setup and initialized
* (15 points) API to load initial data on client (GET)
* (20 points) Dialog and API to update/ create data (PUT/ POST)