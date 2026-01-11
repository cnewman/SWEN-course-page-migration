---
title: 'Code Review'

weight: 20
bookToC: true
bookSearchExclude: false

draft: true
---

## Code Review Checklist

This document is meant to guide your code review process. Before we give you a list of what to look for, here are a few tips for being an effective code reviewer:

1. **Be positive.** Everyone makes mistakes and even you as the reviewer may make a mistake during your review. When you find something that looks like a mistake, point out what the mistake is and give your opinion and/or ask for clarification but do not denigrate or otherwise talk down to the person you are reviewing.

2. **Take your time.** In industry, 500 loc per hour is on the fast side for a review. A good reviewer does not rush through the code.

3. **Do not review for extended periods of time.** At most, review for an hour and then take a break. This helps keep your thoughts fresh and helps avoid rushing.

4. **Feel free to compliment.** If you see something that looks good/impressive, feel free to let your partner know.

We will now list a set of things you should be looking for during your review. These are general code quality checkpoints and are not necessarily exhaustive; if you spot something that is not on this list, feel free to comment on it/bring it up with your partner. This checklist should be applied function-by-function. That is, for each function, look for the following:

### Functional Requirements

* Does the code work?
* Are there any warnings or errors when the code is run?
* Is the output correct?

### Code Formatting

* Are the identifier names clear and do they consistently follow naming conventions?
* Is the code correctly indented and is this indentation used consistently throughout the code?
* Are there any excessively long statements or expressions? As a rule of thumb, you should not need to scroll horizontally to view the code assuming it takes up 14 inches of a 21-inch screen.
* Is there an excessive amount of commented code? Some code commenting is okay if the commented code serves a purpose (e.g., the developer is referring to it as they refactor). However, commented code must have a purpose. Otherwise, it should be removed.

### Non-functional Requirements

* Do you understand what the added code is doing?
* Does the way functions interact within the system make sense to you?
* Do you see reasonable ways to test the code that has been added?
* Is there a comment above the function? Very simple functions may not need a comment (i.e., get/set methods, HTTP protocol methods; though sometimes they do need a comment). Err on the side of writing a comment to explain expected inputs/outputs and any “clever” code being used to implement the required functionality.
* Is there any code that is written in a more complex way than it needs to be? “Clever” code is often bad code; code should be readable and understandable by other developers maintaining it.

### Common Mistakes

* Are there any “magic” numbers? That is, a number that has no explanation (e.g., int array\[5\], where ‘5’ is not explained). Instead, use a constant (or a local variable) whose name explains the purpose of the number
* Don’t repeat yourself (DRY). If the code in more than two places is very similar (i.e., you have code clones), it is likely that it should be made into a function instead of being copied verbatim.
* Avoid global variables
* Use provided library implementations of functionality instead of writing new code when applicable. This is called software reuse.