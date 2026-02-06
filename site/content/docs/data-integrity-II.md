---
title: 'Data Integrity II'

weight: 3
bookToC: true
bookSearchExclude: false

math: true
draft: true
---

# STRATA — DI2: Data Integrity II (Sampling & Sample Size)

**Mode:** Test-driven assignment — **tests are provided** to define the expected behavior.

**Builds on:** DC0/DC1/DC2 (Postgres helpers, commit mining, ecosystem artifacts) and DI1 (qualitative text normalization). Prepares your dataset for statistically valid analysis by implementing reproducible sampling.

---

## Goal

Implement a small sampling library that lets you draw reproducible samples from in-repo artifacts. This prepares you to answer: "How many commits/messages/files do I need to label to estimate a proportion (or mean) with a desired margin of error?" and "How do I draw a balanced (stratified) sample across groups like language, churn bucket, or author?"

---

## Learning Outcomes

1. Implement deterministic, reproducible sampling functions using seeded random number generators.
2. Understand when to use different sampling strategies (uniform, stratified, systematic).
3. Apply sample size formulas for proportions and means with finite population correction.
4. Recognize why reproducibility and statistical validity matter for research.

---

## What You Will Implement

You must implement **five functions** in `src/sampling_algorithms.py`. The function signatures and expected behavior are defined below.

```python
from __future__ import annotations

from math import ceil
from typing import Any, Callable, Dict, List, Optional, Sequence, TypeVar
import random

T = TypeVar("T")


# ---- Uniform Sampling ----

def sample_uniform(items: Sequence[T], k: int, seed: Optional[int] = None) -> List[T]:
    """Draw k items uniformly at random without replacement.

    Why this matters for MSR:
    - Random sampling is the foundation of statistical inference
    - Reproducibility requires deterministic seeds for replication studies
    - Many research tasks need unbiased subsets of large datasets

    Parameters:
    - items: the population to sample from
    - k: number of items to select
    - seed: random seed for reproducibility (None = non-deterministic)

    Returns:
    - List of k sampled items (or all items if k >= len(items))

    Behavior:
    - If k >= len(items), return a shallow copy of all items
    - When seed is provided, the same seed must produce identical results

    Examples:
    >>> sample_uniform([1, 2, 3, 4, 5], k=3, seed=42)
    [4, 5, 2]  # deterministic with seed=42
    >>> sample_uniform([1, 2], k=5, seed=0)
    [1, 2]  # k > len, returns all

    Implementation hints:
    - Use random.Random(seed) to create an isolated RNG instance
    - The random module's .sample() method does sampling without replacement
    """
    # TODO: Implement
    pass


# ---- Stratified Sampling ----

def sample_stratified(
    items: Sequence[T],
    key: Callable[[T], Any],
    *,
    n: Optional[int] = None,
    frac: Optional[float] = None,
    seed: Optional[int] = None,
) -> List[T]:
    """Stratified sampling by key(item).

    Why this matters for MSR:
    - Ensures representation across groups (languages, authors, time periods)
    - Reduces variance when strata are internally homogeneous
    - Prevents dominant groups from overwhelming the sample

    Parameters:
    - items: the population to sample from
    - key: function that returns the stratum/group for each item 
      (Using a callable instead of a string allows grouping by computed values 
      and supports any data type, similar to Python's `sorted`)
    - n: exact number of samples per stratum (**mutually exclusive** with frac)
    - frac: fraction of each stratum to sample (**mutually exclusive** with n)
    - seed: random seed for reproducibility
    - * means that every parameter that comes after (to the right) must be named explicitly (frac=.5, seed=1, etc).

    Returns:
    - List of sampled items from all strata combined

    Behavior:
    - Exactly one of `n` or `frac` must be provided (raise ValueError otherwise)
    - For small strata, return up to the available members (don't error)
    - When frac > 0 but would yield 0 items, return at least 1 item
    - Selection must be reproducible with the same seed

    Examples:
    >>> items = [('py', 1), ('py', 2), ('js', 3), ('js', 4)]
    >>> sample_stratified(items, key=lambda x: x[0], n=1, seed=0)
    [('py', 2), ('js', 4)]  # 1 from each stratum

    >>> sample_stratified(items, key=lambda x: x[0], frac=0.5, seed=0)
    [('py', 1), ('js', 3)]  # 50% from each stratum

    Implementation hints:
    - Group items by key(item) into a dictionary
    - Sample within each group independently
    - For reproducibility, derive per-group seeds from the main seed
      (e.g., hash((group_key, seed)) to get consistent sub-seeds)
    """
    # TODO: Implement
    pass


# ---- Systematic Sampling ----

def sample_systematic(items: Sequence[T], step: int, seed: Optional[int] = None) -> List[T]:
    """Systematic sampling: random start, then every step-th item.

    Why this matters for MSR:
    - Efficient for large ordered populations (e.g., commit history)
    - Simpler than full random sampling for streaming data
    - Approximates uniform sampling when population is randomly ordered

    Parameters:
    - items: the population to sample from (order matters)
    - step: interval between selected items (must be >= 1)
    - seed: random seed for reproducibility of starting position

    Returns:
    - List of sampled items

    Behavior:
    - Choose a random start position in [0, step-1]
    - Select every step-th item from that starting point
    - Raise ValueError if step <= 0

    Examples:
    >>> sample_systematic(list(range(20)), step=5, seed=0)
    [3, 8, 13, 18]  # start=3 with seed=0, then +5 each time

    Implementation hints:
    - Use random.Random(seed).randrange(step) for the start position
    - Iterate with idx += step until idx >= len(items)
    """
    # TODO: Implement
    pass


# ---- Sample Size for Proportions ----

def sample_size_proportion(
    N: Optional[int], p: float = 0.5, margin: float = 0.05, z: float = 1.96
) -> int:
    """Compute required sample size to estimate a proportion.

    Why this matters for MSR:
    - Answers: "How many commits must I label to estimate the bug rate?"
    - Ensures statistical validity of research findings
    - Finite population correction prevents over-sampling small repos

    Parameters:
    - N: population size (None = infinite population, no FPC)
    - p: expected proportion (0.5 is most conservative when unknown)
    - margin: desired margin of error (e.g., 0.05 = ±5%)
    - z: z-score for confidence level (1.96 = 95% confidence)

    Returns:
    - Required sample size as an integer (ceiling), capped at N if provided

    Formulas:
    - Baseline (infinite population): n0 = z² * p * (1-p) / margin²
    - With FPC: n = n0 / (1 + (n0 - 1) / N)

    Examples:
    >>> sample_size_proportion(None, p=0.5, margin=0.05, z=1.96)
    385  # classic value for 95% CI, ±5%

    >>> sample_size_proportion(500, p=0.5, margin=0.05, z=1.96)
    218  # FPC reduces required sample for small population

    Implementation hints:
    - Validate that margin > 0 and p is in valid range
    - Apply ceiling (math.ceil) to get integer
    - Cap result at N when N is provided
    """
    # TODO: Implement
    pass


# ---- Sample Size for Means ----

def sample_size_mean(
    sigma: float, margin: float = 0.05, z: float = 1.96, N: Optional[int] = None
) -> int:
    """Compute required sample size to estimate a mean.

    Why this matters for MSR:
    - Answers: "How many files must I measure to estimate average complexity?"
    - Requires an estimate of population standard deviation

    Parameters:
    - sigma: known or estimated population standard deviation
    - margin: desired margin of error (same units as sigma)
    - z: z-score for confidence level (1.96 = 95% confidence)
    - N: population size (None = infinite population, no FPC)

    Returns:
    - Required sample size as an integer (ceiling), capped at N if provided

    Formulas:
    - Baseline: n0 = z² * σ² / margin²
    - With FPC: n = n0 / (1 + (n0 - 1) / N)

    Examples:
    >>> sample_size_mean(sigma=1.0, margin=0.1, z=1.96)
    385  # same as proportion with p=0.5 when units align

    Implementation hints:
    - Validate that sigma > 0 and margin > 0
    - Same FPC formula as sample_size_proportion
    """
    # TODO: Implement
    pass
```

---

## Math & Formulas

You are not expected to derive these formulas, so we're giving them to you-- but you should understand what they mean and how to use them.

1) **Proportion sample size (baseline, infinite population):**

   To estimate a proportion **p** with margin of error **E** at z-score **z** (e.g., **z=1.96** for 95% confidence), the basic sample-size formula is

   $$n_0 = \frac{z^2 \; p (1-p)}{E^2}$$

   - **p**: a guess for the population proportion (when unknown, use **p=0.5** for the most conservative / largest **n**).
   - **E**: desired margin of error (e.g., **0.05** for ±5 percentage points).
   - **z**: z-score for the desired confidence level (1.96 for 95%).

2) **Finite population correction (FPC):**

   When the population size **N** is not huge compared to **n_0**, apply the FPC to get a reduced required sample:

   $$n = \frac{n_0}{1 + (n_0 - 1) / N}$$

   Finally, round up (ceiling) to an integer and cap at **N**.

3) **Mean sample size (known/assumed **σ**):**

   To estimate a population mean with known/assumed standard deviation **σ** the analogous formula is:

   $$n_0 = \frac{z^2 \; σ^2}{E^2}$$

   Apply the same FPC as above when **N** is provided.

4) **Stratified sampling notes:**

   - Stratified sampling splits the population into disjoint strata (groups) and samples within each group. This reduces variance when the strata are internally homogeneous.
   - Two common strategies are (a) equal allocation (same **n** per stratum) and (b) proportional allocation (sample fraction proportional to stratum size). Both are supported by the `sample_stratified` function via `n` or `frac`.

5) **Systematic sampling notes:**

   - Systematic sampling picks a random start in the first `step` items and then selects every `step`-th item. If the list is large and roughly randomly ordered, this approximates uniform sampling but is cheaper to implement in streaming contexts.

**Practical tips:**
 - Use **p=0.5** when in doubt for proportions; it produces the largest (most conservative) required **n**.
 - When strata sizes are very small, don't force a fixed **n** per stratum; instead sample up to the available members.

---

## Provided Tests

Tests are provided in `test_sampling_algorithms.py`. These tests define the expected behavior for each function. Run them frequently as you implement:

The tests cover:
- **Uniform sampling** — reproducibility with seeds, handling k >= len(items)
- **Stratified sampling** — equal allocation (n per group), proportional allocation (frac)
- **Systematic sampling** — correct step intervals from random start
- **Sample size formulas** — classic values (385 for p=0.5, margin=0.05), FPC reducing sample size

Study the test cases to understand exactly what each function should do. For example:

```python
def test_sample_uniform_reproducible():
    items = list(range(100))
    a = sample_uniform(items, 10, seed=42)
    b = sample_uniform(items, 10, seed=42)
    assert a == b  # same seed = same result
    assert len(a) == 10

def test_sample_size_proportion_basic():
    n = sample_size_proportion(None, p=0.5, margin=0.05, z=1.96)
    assert n == 385  # the classic textbook value
```

---

## Deliverables

1. **Implementation** of the five functions in `sampling_algorithms.py`
2. All **tests passing** in `test_sampling_algorithms.py`
3. `main.py` updated to run your sampling code when you run the miner on a real repository

---

## Grading Outline

| Component | Weight |
|-----------|--------|
| `sample_uniform` — reproducible, handles edge cases | 15% |
| `sample_stratified` — n and frac modes work correctly | 20% |
| `sample_systematic` — correct step logic with random start | 20% |
| `sample_size_proportion` — correct formula and FPC | 20% |
| `sample_size_mean` — correct formula and FPC | 15% |
| `main` — updated | 10% |

---

## Hints

- Make sampling deterministic when `seed` is provided. Use `random.Random(seed)` to create an isolated RNG instance that won't affect global state.
- For stratified sampling, group by `key(item)` and sample inside each group independently. Use a derived seed per group (e.g., `hash((group_key, seed))`) to retain reproducibility while ensuring different groups get different random sequences.
- The `math.ceil()` function rounds up to the nearest integer — use it for sample sizes; you can't have a fraction of a person!