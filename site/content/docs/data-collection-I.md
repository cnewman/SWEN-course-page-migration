---
title: 'Data Collection I'

weight: 2
bookToC: true
bookSearchExclude: false

draft: true
---

# STRATA — DC1: Software Artifacts Mining (Data Collection I)

**Mode:** Test-driven assignment — your **tests** and **test data seeds** *are* the requirements.

**Builds on:** DC0 (working Postgres, GitPython, pytest, CI). You will merge the DC1 branch into your DC0 repository and extend it.

---

## Goal

Build an **idempotent** miner that walks a Git repository’s history and stores commit-, parent-, and file-level facts in Postgres, with **provenance** recorded for each run and **validation** of key invariants.

---

## Learning Outcomes

1. **Mining:** Extract commit history and file-level change data from Git using GitPython.
2. **Storage:** Normalize and store data in Postgres with appropriate keys and indexes.
3. **Testing:** Write tests that exercise mining and storage paths.
4. **Idempotent ETL & Reproducibility:** Re-running the miner does not duplicate data; justify schema/constraints that enable this.
5. **Provenance & Validation:** Record provenance of each run and verify invariants (counts/parents) programmatically.

---

## What You Will Implement

- `mine_history(repo_path: str, max_commits: Optional[int] = None) -> int`
  - Walk `HEAD` ancestry and write to the `DB`:
    - `commits(commit_hash UNIQUE, author_name, message, commit_ts)`
    - `commit_parents(commit_id, parent_hash)`
    - `commit_stats(commit_id, files_changed, insertions, deletions)`
    - `commit_files(commit_id, file_path, change_type, additions, deletions)`
  - Record a `run_log(repo_path, head_hash, commit_count, started_at DEFAULT now())` row.
- `validate_invariants() -> (n_commits, n_stats, n_orphan_parents)`

> **Idempotency** shoudl be enforced by a **UNIQUE** index on `commits(commit_hash)` and a **UNIQUE** index on `commit_files(commit_id, file_path)` together with `ON CONFLICT DO NOTHING` during inserts.

---

## Repository Layout (same as DC0 + additions)

```
.
├─ src/
│  ├─ db_utils.py
│  ├─ git_miner.py
│  └─ ...
├─ data/
│  └─ schema.sql          # includes run_log and all DC1 tables
├─ tests/
│  ├─ conftest.py         # builds a temporary git repo with ≥2 commits
│  ├─ test_db_utils.py
│  └─ test_git_miner.py   # you will extend
└─ config/
   └─ db.yml
```

---

## Test Data Seeds (You Create These In Tests)

Create small, deterministic Git repositories **on the fly** in tests (temporary directories) to seed scenarios:

1. **Two-commit linear history**
   - Commit 1: create `hello.txt` with one line.
   - Commit 2: append one line to `hello.txt`.
   - Purpose: ensures parent edge, file stats, and aggregate stats are populated.

2. **Idempotency replay**
   - Re-run the miner on the same repo without changes.
   - Purpose: verify counts do not change (no duplicates).

3. **Rename or new file (optional stretch)**
   - Add a third commit that either renames `hello.txt` to `greetings.txt` or creates a new file.
   - Purpose: exercise `change_type` heuristic and multiple file rows per commit.

> All seeds must set `user.name` and `user.email` in the test repo config to avoid identity errors.

---

## Test Case Sketches (You Write These Tests)

Treat each sketch as an acceptance criterion. Name tests clearly and keep them small.

### A. Head-mining compatibility (DC0 continuity)
- **Given** the two-commit seed repo
- **When** calling `mine_and_store(temp_repo)`
- **Then** one row exists in `commits` whose `commit_hash` matches `HEAD` and `author_name` is the configured value.

### B. Full-history mining populates stats and files
- **Given** the two-commit seed repo
- **When** calling `mine_history(temp_repo)`
- **Then**
  - `COUNT(commits) >= 2`
  - `COUNT(commit_stats) == COUNT(commits)`
  - `commit_files` has at least one row for `hello.txt` with non-negative `additions` and `deletions`.

### C. Idempotent ETL (must not duplicate on re-run)
- **Given** the two-commit seed repo
- **When** calling `mine_history(temp_repo)` twice
- **Then** the counts for `commits`, `commit_stats`, and `commit_files` are unchanged between runs.

### D. Provenance recorded in run_log
- **Given** the two-commit seed repo
- **When** calling `mine_history(temp_repo, record_run=True)`
- **Then** a `run_log` row exists whose:
  - `repo_path` contains the test repo path,
  - `head_hash` equals the latest `commits.commit_hash` (HEAD),
  - `commit_count` equals the returned traversal count.

### E. Validation invariants
- **Given** any mined repo (≥ 2 commits)
- **When** calling `validate_invariants()`
- **Then**
  - `n_stats == n_commits`
  - `n_orphan_parents == 0`

### F. (Optional) File rename/new-file path
- **Given** a third commit renaming `hello.txt` → `greetings.txt` (or adding a new file)
- **Then** `commit_files` includes appropriate `file_path` rows and a reasonable `change_type` (best-effort).

---

## Required SQL Constraints (Put These In `schema.sql`)

- `UNIQUE(commits.commit_hash)`
- `UNIQUE(commit_files.commit_id, commit_files.file_path)`
- Helpful non-unique indexes for performance:
  - `commit_files(commit_id)`
  - `commit_files(file_path)`
  - `run_log(started_at)`

> Your tests should **fail** if these integrity guarantees are missing (duplicates on re-run).

---

## Commands

- Initialize schema:
  ```bash
  python -c "from src import db_utils; db_utils.exec_sql_file('data/schema.sql')"
  ```
- Run miner:
  ```bash
  python -c "from src.git_miner import mine_history; print(mine_history('.'))"
  ```
- Run tests:
  ```bash
  pytest -q
  ```

---

## Deliverables

1. Updated `schema.sql`, `git_miner.py`, and tests under `tests/`.
2. A README section titled **Idempotency & Invariants** that explains:
   - Which constraints enforce idempotency,
   - How your tests demonstrate those guarantees.
3. Two SQL queries saved as `queries/`:
   - `top_commits_by_churn.sql` — `insertions + deletions`, top 5.
   - `most_changed_files.sql` — files with most commits touching them, top 5.

---

## Grading Outline (no points shown)

- **Schema & Constraints:** correct keys, uniqueness, and helpful indexes.
- **Miner Correctness:** commits, parents, stats, files inserted as specified.
- **Reproducibility:** idempotent re-runs, deterministic behavior.
- **Provenance:** correct `run_log` entries.
- **Validation:** invariants checked programmatically.
- **Code Quality:** function boundaries, clear names, comments where they help.
- **Queries:** both analytics queries run and return sensible results.

---

## Hints

- Wrap best-effort diffs in `try/except` and still record stats.
- Favor small, focused tests over monoliths.
- Use `ON CONFLICT DO NOTHING` with your UNIQUE indexes to keep re-runs clean.