---
title: 'Final Paper (FP)'

weight: 2
bookToC: true
bookSearchExclude: false

draft: true
---
# FP: Final Research Paper

**Goal:** Communicate the empirical study you have designed and executed this semester as a complete, peer-reviewable research paper. You will describe your motivation, methods, results, and their implications in a format consistent with published software engineering research.

**Due: May 4th @ 11:59pm**

---

## Format Requirements

Your paper must follow **IEEE conference paper formatting**. Official templates (LaTeX and Word) are available at:

> https://www.ieee.org/conferences/publishing/templates

You are responsible for following all formatting requirements specified in the template, including margins, font sizes, column layout, and heading styles. Submissions that do not follow IEEE formatting will be penalized.

- **Minimum length:** 4 pages (not counting references)
- **Submission format:** PDF submitted to MyCourses under the **Final Paper** assignment
- **Citations:** IEEE citation style (numbered, e.g. `[1]`)
- **Editors:** You may use any editor you like. Word and Overleaf/LaTeX are encouraged.

---

## Group Work

You may complete your research **solo or in a group** (you should already know which you are doing). All group members must contribute to carrying out the research and writing the research paper.

- All group members' names must appear on the paper.
- Each group member must submit the same copy of the paper individually to MyCourses.

---

## Academic Integrity & AI/LLM Policy

The paper must be written by you (and your group, if applicable). You may use AI/LLM tools (e.g., ChatGPT, Claude) **only** to help improve your prose or to understand how to report something properly when you are uncertain - for example, how to phrase a statistical result or structure a section. You may not use AI to generate the substance of your work: your research questions, methodology, analysis, results, and interpretation must be your own.

---

## Required Sections

Your paper must include **all** of the following sections. Additional sections are allowed if needed.

### 1. Abstract
A concise summary (typically 150–250 words) of the entire paper: motivation, method, key findings, and implications. Write this last.

### 2. Introduction
Motivate your work. What problem are you addressing? Why does it matter? Briefly state your research questions and preview your approach.

### 3. Related Work
Summarize prior research relevant to your topic. How does your work build on, extend, or differ from what has been done before? Cite at least **two academic papers**. This section justifies the novelty of your study.

### 4. Methodology
Describe your research method in enough detail that a reader could replicate your study. Include:
- Your research questions
- Your data source(s) and how you collected the data
- Your sampling strategy and sample size (with justification)
- Your analysis approach (e.g., statistical tests, qualitative coding, modeling)
- Any tools or scripts used

### 5. Evaluation
Present your results clearly and objectively. Use tables, figures, or descriptive statistics as appropriate. Report metrics precisely, do not just say "results were good"; give numbers with context.

### 6. Discussion
Interpret your findings. What do the results mean? Do they answer your RQs? Are the results surprising? What are the practical implications for software engineers or researchers?

### 7. Threats to Validity
Identify the limitations of your study. Consider internal validity (confounds, measurement error), external validity (generalizability), construct validity (do your metrics really measure what you claim?), and conclusion validity (statistical power, effect size). Acknowledging threats honestly is a sign of rigorous research.

### 8. Data and Code
Provide a link to a **public repository** (e.g., Gitlab, GitHub) containing all code and data produced or used as part of your study. The repository should include a README explaining how to reproduce your results. Reviewers should be able to run your pipeline or verify your analysis from what you provide here.

### 9. Conclusion
Briefly restate the problem, summarize your key findings, and suggest directions for future work. Do not introduce new results here.

---

## Grading Criteria

Your paper will be evaluated primarily on:

1. **Research rigor** - How well did you follow your chosen research method? Were your RQs clearly defined and falsifiable? Was your methodology sound and well-justified?
2. **Writing quality** - Is the paper clearly written and logically structured? Are metrics and findings reported precisely and in context? Are claims supported by evidence?
3. **Threats to validity** - Are limitations identified honestly and thoughtfully? Do you explain how they affect the interpretation of your results?
4. **Justification of decisions** - Do you explain *why* you made key design choices (sampling strategy, tools, metrics, etc.)? Unsupported decisions will be penalized.
5. **Reproducibility** - Is the linked repository complete, organized, and documented well enough for someone else to reproduce your work?

    **Your final results will not influence your grade**. It is fine if your results are not what you expected. For example, you're trying to make a predictive model but it underperforms. As long as you followed the methodology and reported your results properly, your grade will be fine.
---

## Tips

- **Start writing early.** The paper takes longer than you expect. Week 14 is the recommended point to have a full draft in progress.
- **Write the abstract last.** It is much easier to summarize a paper you have already written.
- **Be precise with numbers.** "The results showed a strong correlation" is weak. "Spearman's ρ = 0.71, p < 0.01" is strong.
- **Figures and tables count toward your page length**, but must be relevant. Do not pad with unnecessary visualizations.
