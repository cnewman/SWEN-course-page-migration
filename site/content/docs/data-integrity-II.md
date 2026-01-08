---
title: 'Data Integrity II'
date: '2025-09-29T11:31:00-04:00'

weight: 3
bookToC: true
bookSearchExclude: false

math: true
draft: true
---

# STRATA — DI2: Data Integrity II (Sampling & Sample Size)
---

## Goal

Provide a small sampling library and tests that let you draw reproducible samples from in-repo artifacts. Emphasize: deterministic seeds, stratification, fraction-based and per-group sampling, systematic sampling, and basic sample-size formulas (proportions and finite-population correction).

This prepares you to answer: "How many commits/messages/files do I need to label to estimate a proportion (or mean) with a desired margin of error?" and "How do I draw a balanced (stratified) sample across groups like language, churn bucket, or author?"

## Required public functions (names & signatures)

You must implement these functions so tests and autograders can import them by name. Keep signatures exactly as specified.

- sample_uniform(items: Sequence[T], k: int, seed: Optional[int] = None) -> List[T]
  - Draw k items uniformly at random without replacement. If k >= len(items), return a shallow copy of items. When seed is provided, sampling must be reproducible.

- sample_stratified(items: Sequence[T], key: Callable[[T], Any], *, n: Optional[int] = None, frac: Optional[float] = None, seed: Optional[int] = None) -> List[T]
  - Stratified sampling by `key(item)`. Either `n` (samples per stratum) or `frac` (fraction per stratum) must be provided. For small strata, return up to the available members (do not error). Use sampling without replacement and make selection reproducible with `seed`.

- sample_systematic(items: Sequence[T], step: int, seed: Optional[int] = None) -> List[T]
  - Systematic sampling: choose a random start in [0, step-1] (use `seed`), then take every `step`-th item cyclically until items exhausted. Useful for large ordered populations.

- sample_size_proportion(N: Optional[int], p: float = 0.5, margin: float = 0.05, z: float = 1.96) -> int
  - Compute required sample size to estimate a proportion with margin of error `margin` at z-score `z`. If `N` (population size) is provided, apply finite population correction. Return an integer sample size (ceiling) and cap at `N` if provided.

- sample_size_mean(sigma: float, margin: float = 0.05, z: float = 1.96, N: Optional[int] = None) -> int
  - Compute sample size to estimate a mean with known (or assumed) population standard deviation `sigma`. Apply finite population correction if `N` provided.

## Tests & example implementation

A reference implementation and tests are provided in `src/di2_sampling.py` and `test/test_di2_sampling.py`. Use the tests as a template and ensure your final submission includes small, deterministic fixtures and seeds.

## Math & formulas

Students are not expected to derive these formulas from first principles, but you should understand what they mean and how to use them.

1) Proportion sample size (baseline, infinite population):

  To estimate a proportion $p$ with margin of error $E$ at z-score $z$ (e.g., $z=1.96$ for 95% confidence), the basic sample-size formula is

  $$n_0 = \frac{z^2 \; p (1-p)}{E^2}$$

  - `p`: a guess for the population proportion (when unknown, use `p=0.5` for the most conservative / largest `n`).
  - `E`: desired margin of error (e.g., `0.05` for ±5 percentage points).
  - `z`: z-score for the desired confidence level (1.96 for 95%).

2) Finite population correction (FPC):

  When the population size `N` is not huge compared to `n_0`, apply the FPC to get a reduced required sample:

  $$n = \frac{n_0}{1 + (n_0 - 1) / N}$$

  Finally, round up (ceiling) to an integer and cap at `N`.

3) Mean sample size (known/assumed $\sigma$):

  To estimate a population mean with known/assumed standard deviation `σ` the analogous formula is:

  $$n_0 = \frac{z^2 \; \sigma^2}{E^2}$$

  Apply the same FPC as above when `N` is provided.

4) Stratified sampling notes:

  - Stratified sampling splits the population into disjoint strata (groups) and samples within each group. This reduces variance when the strata are internally homogeneous.
  - Two common strategies are (a) equal allocation (same `n` per stratum) and (b) proportional allocation (sample fraction proportional to stratum size). Both are supported by the `sample_stratified` function via `n` or `frac`.

5) Systematic sampling notes:

  - Systematic sampling picks a random start in the first `step` items and then selects every `step`-th item. If the list is large and roughly randomly ordered, this approximates uniform sampling but is cheaper to implement in streaming contexts.

Practical tips:
 - Use `p=0.5` when in doubt for proportions; it produces the largest (most conservative) required `n`.
 - When strata sizes are very small, don't force a fixed `n` per stratum; instead sample up to the available members.
 - Document your assumptions (confidence level, margin, `p` or `σ`, and whether you used FPC) when reporting sample sizes.

## Test seeds and acceptance criteria

Create tests that cover:

1. Deterministic uniform sampling
   - Given a list of 100 integers and a fixed seed, `sample_uniform(..., k=10, seed=42)` returns the same list across runs.

2. Stratified sampling
   - Given items labeled into strata (for example authors or language), `sample_stratified(..., n=2)` yields up to 2 items per stratum and is reproducible with a seed.

3. Fractional stratified sampling
   - With `frac=0.5`, each stratum should yield floor(frac * size) samples (or at most the stratum size).

4. Systematic sampling
   - Given a known step and seed, the returned indices should match a deterministic pattern.

5. Sample size formulas
   - `sample_size_proportion(None, p=0.5, margin=0.05)` returns 385 (the classic value for 95% z=1.96).
   - With small N, FPC reduces sample size.

## Deliverables

1. `src/di2_sampling.py` — the implementation (will be emptied before publishing to students).
2. `test/test_di2_sampling.py` — unit tests demonstrating reproducible sampling and sample-size calculations (will be emptied before publishing to students).
3. A short section in your README or assignment writeup describing which sampling strategies you used and why.

## Commands

Run tests:

```powershell
pytest -q
```

Run only DI2 tests:

```powershell
python -m pytest -q test/test_di2_sampling.py
```

## Hints

- Make sampling deterministic when `seed` is provided. You can use `random.Random(seed)` to create an RNG instance.
- For stratified sampling, group by `key(item)` and sample inside each group independently using the same seed offset to retain reproducibility.
- When computing sample sizes, document assumptions (e.g., using p=0.5 when the true proportion is unknown gives the maximum required sample).