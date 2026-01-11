---
title: 'Cross Team Acceptance Testing'

weight: 100
bookToC: true
bookSearchExclude: false

draft: true
---

## Cross-Team Acceptance Testing

### Preparing for Cross-team Acceptance Testing

To prepare for cross-team acceptance testing each team will need to do two steps.

1. Ensure that your [acceptance test plan spreadsheet](/docs/project/acceptance-test-plan) **IS UP-TO-DATE** and identifies all of the user stories for Sprints 2 and 3 and includes the Acceptance Criteria for all of those stories. You should also indicate which stories you know are not yet implemented and should not be tested. You can identify the stories not to test by greying or in some way highlighting them. Make sure that the spreadsheet has a file name starting with `Acceptance Test Plan`.
2. Validate that another computer on the same network can connect to your application with the IP Address and Port. It is recommended that both the application and the tests be run on lab machines.

{{% hint warning %}}

Your instructor may follow a different approach on how the cross-team testing is executed. **Make sure to get clarification on this PRIOR to the session where cross-team testing will take place**

{{% /hint %}}

### Exercises

#### *Before-class Exercises*

* Acceptance testing plan - team (2 exercise points)
    * Perform the steps specified above. Place your zipfile on a USB drive or in a place where you can quickly access it to give to your tester when the tester is logged onto an SE lab machine or their own laptop.
    * Deposit your Acceptance Test Plan spreadsheet file to *Acceptance Test Plan - team*, to the Assignments folder in myCourses under *Team Exercises* area.

#### *In-class Exercises*

* *Cross-team acceptance testing:* for these in-class exercises, the team will have two roles: as developers of a product being tested, and as testers of another team's product.
    * As the product developers
        * `(Unless otherwise instructed)` Provide your testers with a copy of the Acceptance Test Plan and the IP Address and Port that your application is running on.
        * Answer any questions that the testers have while they are performing the tests.
    * As the product testers
        * Validate that you can connect to the application on the given IP Address and Port.
        * Perform the acceptance testing by executing tests to determine if the product meets each acceptance criterion in the Acceptance Test Plan that has been identified as implemented. For each test, record the results of the test. Especially for tests that fail, be specific in the comments that you provide to give the product developers as much information as possible describing the nature of the failure.
        * Aggregate all test results and comments into one Acceptance Test Plan spreadsheet and make sure to `give that back to the product developer(s)`.
* *Acceptance test results - team* (2 exercise points)
    * When you have completed your testing as the **product testers**, rename the file using the same `ssqr` designation from the zipfile to now conform with `Acceptance Test plan-ssteamX-testing-ssteamY.xls` for example `Acceptance Test plan-09s1a-testing-of-09s1b.xls`. Deposit the Acceptance Test Plan spreadsheet with the results of all of the tests that you performed into the Assigments folder in myCourses, *Acceptance test results - team* in the Team Exercises.
    
    {{% hint warning %}}
    
    Note: this is the file of results for the testing that your team performed on another team's product and you which you should have also given to the product developers for them to use your feedback.

    {{% /hint %}}