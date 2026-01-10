---
title: 'Data Analysis I'
date: '2025-09-29T11:24:52-04:00'

weight: 2
bookToC: true
bookSearchExclude: false

draft: true
---

# STRATA — DA1: Data Analysis I (srcML-based Static Analysis)

---

## Goal

Teach you how to work with srcML XML artifacts and XPath-style queries to extract function- and file-level features. You will learn to: parse srcML XML, extract a compact set of features (function name, parameter count, lines-of-code, number of calls, presence of comments/docstrings), aggregate to file-level summaries, and produce a small dataset suitable for clustering or classification. While we are learning a specific technology, parsing ASTs with any framework will share similarities with what we will learn here.

## Learning outcomes

- Read and navigate srcML XML using XPath-style queries (or ElementTree).
- Extract function-level structural features and aggregate to file/repo level.
- Produce reproducible feature tables joinable to mined artifacts (commits, commit_files, run_log).
- Prepare a brief exploratory analysis and choose a clustering/classification target for DA2.

## Required public functions (names & signatures)

You should implement (or adapt) functions with these signatures.

- extract_functions_from_srcml(xml_str: str) -> List[dict]
  - Parse a single srcML XML string and return a list of function dicts with at least these keys:
    - `name` (str | None)
    - `params` (int)
    - `loc` (int) — approximate lines of code in the function body
    - `num_calls` (int)
    - `has_comment` (bool) — True if the function body contains a comment or docstring

- aggregate_file_features(functions: List[dict]) -> dict
  - Reduce a file's function list to summary features:
    - `n_functions`, `avg_loc`, `max_loc`, `avg_calls`, `pct_with_comments`

- xml_from_file(path: str) -> str
  - Helper to read a pre-generated srcML XML file from disk

These functions are deliberately small and testable. Keep them pure where possible (accept strings, return dicts/lists).

## Install srcML

You will need to download and install [`srcml`](https://www.srcml.org/)

## Using srcML locally (optional)

If you have srcML installed, they can produce XML for a source file like this (PowerShell example):

```powershell
srcml.exe path\to\file.py -o path\to\file.py.srcml
```

Or to write XML to stdout and capture it in a file:

```powershell
srcml.exe path\to\file.py > path\to\file.py.srcml
```

## Feature suggestions (what to extract)

Here are example feature ideas that work well for clustering/classification later:

- Function-level:
  - number of parameters
  - lines of code (LOC)
  - number of calls inside the function
  - number of nested blocks (approximate nesting depth)
  - presence/absence of comments or docstrings
  - function name length or tokenized name parts (camelCase/snake_case splits)

- File-level (aggregates):
  - number of functions
  - mean/median/max LOC per function
  - mean/median/max number of calls
  - fraction of functions with comments
  - file token count, comment ratio

Pick a small subset (4–8) for DA1 to keep the week focused.

## Suggested workflow

1. Take a small sample of files (provided srcML XML or run srcml locally) from the mined dataset.
2. Implement `extract_functions_from_srcml` and `aggregate_file_features`.
3. Run extractor on the sample, save `functions.csv` and `files.csv` (aggregates).
4. Do a short EDA: histograms of LOC, calls, and comments fraction.

## Tests & example implementation

A reference implementation `src/da1_srcml.py` and unit tests `test/test_da1_srcml.py` are provided as a template. The tests are intentionally small: they parse minimal srcML-like XML strings and assert feature extraction correctness. Use them as scaffolding when writing your own code.

## Deliverables

- `src/da1_srcml.py` (extractors and aggregators)
- `test/` unit tests for the extraction functions