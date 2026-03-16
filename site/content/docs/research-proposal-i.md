---
title: 'Research Proposal (RP)'

weight: 2
bookToC: true
bookSearchExclude: false

draft: true
---
# RP: Research Proposal & Topic Selection

**Goal:** Transition from executing a provided pipeline to designing an original empirical study. You will identify a problem, select target repositories (or whever you are getting your data from), and formulate measurable Research Questions (RQs).

---

## Deliverables

Submit a Markdown file (e.g., `PROPOSAL.md`) containing the following four sections:

### 1. The "Problem"
Identify a specific area of interest in Software Engineering (e.g., code quality, developer productivity, bug prediction, program comprehension, etc). 
* **The Gap:** Briefly explain why this is interesting to you-- what you do not understand (and thus, seek to understand).

### 2. Research Questions
Define **1 or 2 Research Questions**. These must be specific and answerable using your pipeline.
* **Example RQ:** *Is domain-terminology-heavy code harder to read than code that contains more general terminology?*
* **Constraint:** Your RQs must be *falsifiable*. That is, there must be a way to test your hypothesis through evidence, observation, or experimentation.

### 3. Methodology & Dataset
* **Define your data source(s):** Every empirical study requires data. The easiest is likely Github Repositories, but you are allowed to collect data from elsewhere. Here are a few general places you might collect from:
    * You may collect data from GitHub or any other repository to which you have access.
    * You may survey students or professional developers-- but you must have access to a willing population if you choose this type of project.
    * You may collect data using any tools to which you have access, such as eye-trackers.
    * See me if you have an data source that you are uncertain about.
* **Define your sample:** You will need to sample some number of data points. The first step is to determine how many data points you will need to sample. Begin by using our sampling techniques from a few weeks ago. Consider using a 95 and 5 (Confidence Interval - Margin of Error). The second step is to choose a sampling approach-- random, stratified, systematic-- to use for collecting your sample.
    * In some cases, the number this gives you will be **far too big for 5 weeks**. You'll need to estimate how long it will take you to process these data points by analyzing a few of them. Time yourself processing 3–5 data points and use that to project total collection time. You don't want to spend more than **2 weeks** on collecting and processing/analyzing your sample.

### 4. Preliminary Related Work
Find **one academic paper** (via Google Scholar or ACM DL) that relate to your topic. 
* Provide the citation.
* Read the paper and ask questions if you are uncertain about anything.
* Write a paragraph (2-4 sentences) on how the work relates to your proposed project.

---

## Evaluation Criteria
Your idea will be evaluated on the following:

1. You have constructed a clearly-defined goal (a problem to solve and/or knowledge you want to obtain).
2. You have constructed a set of research questions that address the goal in a falsifiable manner.
3. You have clearled-defined data source(s) that you can use to answer your research questions, and a plan of how you will sample from these sources.
4. You have identified and summarized at least two academic papers related to the topic that you wish to explore.

---

## Example Research Ideas

Not sure where to start? Here are some example problems scoped for a 5-week empirical study. These are meant to spark ideas - you are not required to use them, and you should make your own version if something here interests you.

---

**1. Do longer functions have more bugs?**
Mine a set of open-source GitHub repositories. Use srcML to extract function length (lines of code, cyclomatic complexity). Cross-reference with bug-fix commits (identified via commit message keywords like "fix", "bug", "error"). RQ: *Is function length positively correlated with the number of associated bug-fix commits?*

---

**2. Do projects with CI pipelines resolve issues faster?**
Sample GitHub repositories with and without CI configuration files (e.g., `.github/workflows/`). Collect issue open/close timestamps. RQ: *Do repositories with CI pipelines have a shorter median issue resolution time than those without?*

---

**3. Are PRs with more review comments more likely to be revised before merging?**
Collect pull request data (comments, commits after first review, merge status) from a set of active repositories. RQ: *Is the number of review comments on a PR positively associated with the number of follow-up commits before merge?*

---

**4. Does test coverage correlate with post-release bug reports?**
Find projects that publish code coverage reports (e.g., via Codecov badges). Compare coverage levels against the number of bug-labeled issues opened after each release. RQ: *Do releases from high-coverage projects attract fewer bug reports than releases from low-coverage projects?*

---

**5. Do descriptive commit messages correlate with lower bug density?**
Use NLP features (message length, presence of issue references, imperative verbs) to score commit message quality across repositories. Compare against bug-fix commit rates. RQ: *Is commit message quality associated with a lower proportion of bug-fix commits in a project's history?*

---

**6. Is commented-out code a signal of future churn?**
Use srcML to detect commented-out code blocks. Track whether those regions are modified or deleted in subsequent commits. RQ: *Are functions containing commented-out code more likely to be refactored or deleted within 6 months?*

---

**7. Do developer perceptions of code quality match static metrics?**
Survey students or developers rating code samples for readability/quality. Compare ratings against static metrics (e.g., Halstead complexity, comment ratio). RQ: *Do developer perceptions of code quality align with standard static analysis metrics?* *(Requires access to a willing survey population.)*

---