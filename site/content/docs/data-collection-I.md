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

Below are the Python signatures and concise docstring-style guidance for the functions you will implement in `src/git_miner.py`. These are intended to be copy/paste-ready and describe the inputs, outputs, and the key behavior your tests will rely on. The `mine_history` function is completed for you; you just need to copy it.

```python
from datetime import datetime
from typing import Optional, Iterable, Tuple
from git import Repo
from . import db_utils

def upsert_commit(repo_commit) -> int:
  """Insert commit if missing and return its DB primary-key id.

  Behavior:
  - Build a dict with `hash`, `author_name`, `message`, `timestamp`.
  - INSERT ... ON CONFLICT DO NOTHING RETURNING id; if no row is
    returned, SELECT id FROM commits WHERE commit_hash=%(hash)s.
  - Return the integer id.
  """

def insert_parents(commit_id: int, parents: Iterable[str]) -> None:
  """Record parent links for `commit_id`.

  Insert rows into `commit_parents(commit_id, parent_hash)` using
  `ON CONFLICT DO NOTHING` to keep the operation idempotent.
  """

def insert_stats(commit_id: int, repo_commit) -> None:
  """Insert aggregate stats for a commit.

  Use `repo_commit.stats.total` (or default zeros) and write into
  `commit_stats(commit_id, files_changed, insertions, deletions)` with
  `ON CONFLICT (commit_id) DO NOTHING`.
  """

def insert_files(commit_id: int, repo_commit) -> None:
  """Insert per-file change rows for a commit.

  General structure (implement this pattern):

  - Obtain per-file stats dict:
    files = getattr(repo_commit.stats, 'files', {}) or {}

  - Attempt to infer change types via a diff to the parent commit:
    change_types = {}
    try:
      parent = repo_commit.parents[0] if repo_commit.parents else None
      if parent is not None:
        for diff in parent.diff(repo_commit):
          # set change_types[diff.b_path or diff.a_path]
          # to diff.change_type.upper() (A/M/D/R/T)
          # Note: `diff.b_path` is the path in the new commit (useful for
          # additions/renames); `diff.a_path` is the path in the parent (useful
          # for deletions). Preferring `b_path` records the post-change path,
          # while falling back to `a_path` preserves the old path when the file
          # was removed.
      else:
        # root commit: mark all paths in `files` as 'A'
        for path in files.keys():
          change_types[path] = 'A'
    except Exception:
      # If diffing fails, fall back to a conservative default
      # (e.g. treat unknown files as 'M') and continue.
      pass

  - For each (path, data) in `files.items()`:
    # compute additions = int(data.get('insertions', 0))
    # compute deletions = int(data.get('deletions', 0))
    # choose change_type = change_types.get(path, 'M')
    # INSERT into `commit_files(commit_id, file_path, change_type, additions, deletions)`
    # using `ON CONFLICT DO NOTHING` keyed by (commit_id, file_path).

  The goal is to be best-effort and idempotent; tests will assert
  that rows exist with non-negative additions/deletions and reasonable
  change_type values.
  """

def insert_run_log(repo_path: str, head_hash: str, commit_count: int) -> None:
  """Append a provenance row to `run_log`.

  Write `repo_path`, `head_hash`, and `commit_count`. `started_at` can
  be a DB default of `now()`.

  Motivation: when mining software repositories for research it's
  important to record provenance for reproducibility, auditing, and
  debugging. Recording the `repo_path`, the `head_hash` observed after a
  run, and the `commit_count` lets future analysts tie database rows to a
  specific repository state (commit SHA) and run. This supports:
  - reproducing results by checking out the recorded `head_hash`;
  - detecting incomplete runs or partial replays by comparing counts;
  - auditing which repository snapshot produced the stored facts.
  """

def validate_invariants() -> Tuple[int, int, int]:
  """Return (n_commits, n_stats, n_orphan_parents).

  Tests use this to assert `n_stats == n_commits` and `n_orphan_parents == 0`. Example of how to do orphans below.
  n_orphan_parents = db_utils.exec_get_one(
    "SELECT COUNT(*) FROM commit_parents cp WHERE NOT EXISTS (SELECT 1 FROM commits c WHERE c.commit_hash = cp.parent_hash);"
)[0]
  """
```

Below is the full reference implementation of `mine_history()` that your
tests will expect behaviorally (you may copy this into `src/git_miner.py`
or use it as the ground-truth when writing your own implementation):

```python
def mine_history(repo_path: str = ".", max_commits: Optional[int] = None, record_run: bool = True) -> int:
  """Traverse commit history and persist commits, stats, files, and parents.

  Processing order: oldest -> newest so the HEAD commit receives the highest id.
  Idempotent: uses unique(commit_hash) and ON CONFLICT safeguards.
  Returns: number of commits traversed this call (not newly inserted).
  """
  with Repo(repo_path) as repo:
    commits = list(repo.iter_commits("HEAD"))  # newest -> oldest
    commits.reverse()  # oldest -> newest
    count = 0
    for c in commits:
      cid = upsert_commit(c)
      insert_parents(cid, [p.hexsha for p in c.parents])
      insert_stats(cid, c)
      insert_files(cid, c)
      count += 1
      if max_commits is not None and count >= max_commits:
        break
    if record_run:
      head_hash = repo.head.commit.hexsha  # capture after processing
      insert_run_log(repo_path, head_hash, count)
    return count
```

## Database Schema
```sql
-- Drop existing tables to ensure a clean schema before creating objects. There are many other ways to do this, but I'm making it explicit here for your convenience
DROP TABLE IF EXISTS run_log CASCADE;
DROP TABLE IF EXISTS commit_parents CASCADE;
DROP TABLE IF EXISTS commit_files CASCADE;
DROP TABLE IF EXISTS commit_stats CASCADE;
DROP TABLE IF EXISTS commits CASCADE;

CREATE TABLE IF NOT EXISTS commits (
    id SERIAL PRIMARY KEY,
    commit_hash TEXT NOT NULL,
    author_name TEXT NOT NULL,
    message TEXT NOT NULL,
    commit_ts TIMESTAMP NOT NULL
);
-- Ensure no duplicate commits by hash
CREATE UNIQUE INDEX IF NOT EXISTS idx_commits_hash ON commits(commit_hash);

-- Per-commit aggregate stats
CREATE TABLE IF NOT EXISTS commit_stats (
    commit_id INTEGER PRIMARY KEY REFERENCES commits(id) ON DELETE CASCADE,
    files_changed INTEGER NOT NULL,
    insertions INTEGER NOT NULL,
    deletions INTEGER NOT NULL
);

-- Per-file change details for each commit
CREATE TABLE IF NOT EXISTS commit_files (
    id SERIAL PRIMARY KEY,
    commit_id INTEGER NOT NULL REFERENCES commits(id) ON DELETE CASCADE,
    file_path TEXT NOT NULL,
    change_type TEXT NOT NULL,         -- 'A','M','D','R' (added, modified, deleted, renamed) best-effort from GitPython stats
    additions INTEGER DEFAULT 0,
    deletions INTEGER DEFAULT 0
);
-- Ensure idempotent inserts per (commit_id, file_path)
CREATE UNIQUE INDEX IF NOT EXISTS uq_commit_files_commit_path ON commit_files(commit_id, file_path);

-- Parent relationships (to support DAG traversals/merges)
CREATE TABLE IF NOT EXISTS commit_parents (
    commit_id INTEGER NOT NULL REFERENCES commits(id) ON DELETE CASCADE,
    parent_hash TEXT NOT NULL,
    PRIMARY KEY (commit_id, parent_hash)
);

CREATE TABLE IF NOT EXISTS run_log (
    id SERIAL PRIMARY KEY,
    started_at TIMESTAMP NOT NULL DEFAULT NOW(),
    repo_path TEXT NOT NULL,
    head_hash TEXT NOT NULL,
    commit_count INTEGER NOT NULL
);
```

---

## Repository Layout (same as DC0 + additions)

```
.
├─ src/
│  ├─ db_utils.py
│  ├─ git_miner.py        # to be implemented
│  └─ ...
├─ data/
│  └─ schema.sql          # includes run_log and all DC1 tables
├─ tests/
│  ├─ conftest.py         # builds a temporary git repo with ≥2 commits
│  ├─ test_db_utils.py
│  └─ test_git_miner.py   # to be implemented and extended
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

### F. Add 2 New Tests in addition to the ones above
- Recall that tests should test average cases as well as corner cases. Come up with at least 2 addition test cases. They can augment the required tests above, or you can add unrelated tests that make sense to add. In your README.MD (this one), list the new tests you added and argue for why they are appropriate tests. You may to add more than 2 new tests.

---

## Required SQL Constraints (Put These In `schema.sql`)

- `UNIQUE(commits.commit_hash)`
- `UNIQUE(commit_files.commit_id, commit_files.file_path)`
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
2. A README section titled **New Tests** that explains:
   - Which tests you added and why these are appropriate tests
3. Tag your submission as DC1
---

## Grading Outline

- **Miner Correctness:** commits, parents, stats, files inserted as specified.
- **Reproducibility:** idempotent re-runs, deterministic behavior.
- **Provenance:** correct `run_log` entries.
- **Validation:** invariants checked programmatically.
- **Code Quality:** function boundaries, clear names, comments where they help.
- **CI pipeline** runs successfully; all tests pass.

---

## Hints

- Recall that you can `try/except` -- this will allow you to record stats even if you have difficulties with diffs
- Favor small, focused tests over monoliths.
- Use `ON CONFLICT DO NOTHING` with your UNIQUE indexes to keep re-runs clean.
> **Idempotency** is enforced by a **UNIQUE** index on `commits(commit_hash)` and a **UNIQUE** index on `commit_files(commit_id, file_path)` together with `ON CONFLICT DO NOTHING` during inserts.
- This new function may come in handy in your db_utils
```python
def exec_commit_returning(sql, args={}):
    """Execute a write query that RETURNS rows (e.g., INSERT ... RETURNING)."""
    conn = connect()
    cur = conn.cursor()
    cur.execute(sql, args)
    rows = cur.fetchall()
    conn.commit()
    conn.close()
    return rows
```