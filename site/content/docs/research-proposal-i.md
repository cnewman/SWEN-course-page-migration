---
title: 'Research Proposal (RP1)'
weight: 9
bookToC: true
draft: false
---

# RP1: Research Proposal & Topic Selection

**Goal:** Transition from executing a provided pipeline to designing an original study. You will identify a problem, select target repositories, and formulate measurable Research Questions (RQs).

---

## Deliverables

Submit a Markdown file (e.g., `PROPOSAL.md`) containing the following four sections:

### 1. The "Problem" (The Why)
Identify a specific area of interest in Software Engineering (e.g., code quality, developer productivity, bug prediction, or naming conventions). 
* **The Gap:** Briefly explain why this is interesting. (e.g., "We know long names are hard to read, but we don't know if they correlate with specific structural complexities found by srcML.")

### 2. Research Questions (The What)
Define **1 or 2 Research Questions**. These must be specific and answerable using your pipeline.
* **Example RQ:** *Do files with high 'vocabulary entropy' (from DA2) exhibit a higher frequency of 'fix' commits (from M1) compared to low-entropy files?*
* **Constraint:** Your RQs must identify an **Independent Variable** (what you vary/group) and a **Dependent Variable** (what you measure).

### 3. Methodology & Dataset (The How)
* **The Three Repo Rule:** Identify at least 3 GitHub repositories you will mine. 
    * *Recommendation:* Pick repos with different "personalities" (e.g., a mature library like `flask`, a fast-moving tool like `uv`, and a research project).
* **Pipeline Map:** List which parts of the existing pipeline (DC, DI, DA, M) you will use and any **modifications** you plan to make.

### 4. Preliminary Related Work
Find **two academic papers** (via Google Scholar or ACM DL) that relate to your topic. 
* Provide the citation.
* Write 2 sentences on how their work relates to your proposed project.

---

## Evaluation Criteria

| Criteria | Weight |
| :--- | :--- |
| **Feasibility:** Can this be done in 5 weeks using your pipeline? | 30% |
| **Clarity:** Are the RQs measurable and specific? | 30% |
| **Dataset:** Are the chosen repos appropriate for the RQs? | 20% |
| **Context:** Does the related work actually inform the proposal? | 20% |
