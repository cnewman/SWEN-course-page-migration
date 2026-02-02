---
title: 'Data Collection II'

weight: 3
bookToC: true
bookSearchExclude: false

draft: true
---

# STRATA — DC2: Ecosystem Artifacts (Issues, PRs, CI) — Data Collection II

**Builds on:** DC1 (commit mining, idempotent ETL, invariants). DC2 extends the schema and code to ingest **issues**, **pull/merge requests**, and **CI pipelines/jobs** while keeping DC1 behavior intact.

---

## Goal

Augment your miner to collect provider-agnostic ecosystem data and persist it **idempotently**:

- **Issues** (opened/closed + metadata)
- **Pull/Merge Requests** (state incl. merged)
- **CI Pipelines/Jobs** (status, timing, and optional linkage to a commit SHA)

This expands the research substrate for things we might consider doing later, such as: *How do code changes, review activity, and CI outcomes interrelate?* --  while you won't necessarily use other providers, this tool should be extensible; supporting other providers without much modification.

---

## Learning Outcomes

1. **Attempt Provider-Agnostic Ingestion:** Normalize GitHub/GitLab JSON into a stable relational shape.
2. **Schema Design for Idempotency:** Use composite unique keys and conflict-aware upserts to make re-runs safe.
3. **Mine External Git Artifacts:** Use the Git Api to fetch data not found in commits.

---

## What You Will Implement

Extend your `src/` code with idempotent ingestion helpers:

- `ingest_issues(provider: str, repo: str, issues: Iterable[dict]) -> int`
  - Upsert into `issues(provider, repo, issue_number, title, author, state, created_at, closed_at)`.
  - State is normalized to basic strings like `open`, `closed`.

- `ingest_pull_requests(provider: str, repo: str, prs: Iterable[dict]) -> int`
  - Upsert into `pull_requests(provider, repo, pr_number, title, author, state, created_at, merged_at, closed_at)`.
  - Normalize state so that a PR with `merged_at` is `merged` (even if provider reports closed).

- `ingest_ci(provider: str, repo: str, pipelines: Iterable[dict], jobs_by_pipeline: Optional[Dict[str, Iterable[dict]]] = None) -> int`
  - Upsert into `ci_pipelines(provider, repo, pipeline_id, status, created_at, updated_at, sha)`.
  - Upsert jobs into `ci_jobs(provider, repo, pipeline_id, job_id, name, status, started_at, finished_at, duration_seconds)`.
  - If `sha` is provided on a pipeline, it should match a commit mined in DC1 (not enforced by FK, but your tests will sanity-check).

Below are Python function signatures and implementation guidance for DC2 functions. These are copy/paste-ready stubs describing inputs, outputs, and key behaviors your tests should rely on.

```python
from datetime import datetime, timezone
from typing import Optional, Iterable, Dict, Any

# =============================
# DC2: Ecosystem Artifacts
# =============================

def _normalize_timestamp_to_utc(value: Any) -> datetime:
    """Coerce timestamps to timezone-aware UTC datetime."""
    if value is None:
        return None
    if isinstance(value, datetime):
        # If already datetime, ensure it's UTC-aware
        if value.tzinfo is None:
            return value.replace(tzinfo=timezone.utc)
        return value.astimezone(timezone.utc)
    
    # Parse string timestamp
    v = str(value)
    
    # Try common formats with explicit UTC 'Z' suffix
    for fmt in ("%Y-%m-%dT%H:%M:%S.%fZ", "%Y-%m-%dT%H:%M:%SZ"):
        try:
            dt = datetime.strptime(v, fmt)
            return dt.replace(tzinfo=timezone.utc)
        except ValueError:
            continue
    
    # Try formats without timezone (assume UTC)
    for fmt in ("%Y-%m-%dT%H:%M:%S.%f", "%Y-%m-%dT%H:%M:%S", "%Y-%m-%d %H:%M:%S", "%Y-%m-%d"):
        try:
            dt = datetime.strptime(v, fmt)
            return dt.replace(tzinfo=timezone.utc)
        except ValueError:
            continue
    
    # Fallback: fromisoformat
    try:
        dt = datetime.fromisoformat(v.replace('Z', '+00:00'))
        return dt.astimezone(timezone.utc) if dt.tzinfo else dt.replace(tzinfo=timezone.utc)
    except Exception:
        raise ValueError(f"Unrecognized timestamp: {value}")

# ---- Issues ----

def upsert_issue(provider: str, repo: str, issue: Dict[str, Any]) -> int:
    """Insert/update a single issue idempotently; return db id.
    
    Implementation guidance:
    - Build an INSERT ... ON CONFLICT (provider, repo, issue_number) DO UPDATE statement.
    - Extract: provider, repo, issue["number"], issue["title"], issue["author"], 
      issue["state"], issue["created_at"], issue["closed_at"].
    - Use _normalize_timestamp_to_utc() for timestamp fields.
    - ON UPDATE: set title=EXCLUDED.title, state=EXCLUDED.state,
      author=COALESCE(EXCLUDED.author, issues.author),
      created_at=LEAST(issues.created_at, EXCLUDED.created_at),
      closed_at=COALESCE(EXCLUDED.closed_at, issues.closed_at).
    - Use RETURNING id to get the row id.
    - If no rows returned (shouldn't happen with RETURNING), fallback SELECT.
    """
    # TODO: Implement upsert logic
    pass

def ingest_issues(provider: str, repo: str, issues: Iterable[Dict[str, Any]]) -> int:
    """Ingest multiple issues; return count processed.
    
    Implementation guidance:
    - Loop over issues iterable.
    - Call upsert_issue(provider, repo, issue) for each.
    - Return total count processed.
    """
    # TODO: Implement
    pass

# ---- Pull Requests ----

def upsert_pull_request(provider: str, repo: str, pr: Dict[str, Any]) -> int:
    """Insert/update a single pull/merge request idempotently; return db id.
    
    Implementation guidance:
    - Similar to upsert_issue, but for pull_requests table.
    - Extract: provider, repo, pr["number"], pr["title"], pr["author"],
      pr["state"], pr["created_at"], pr["merged_at"], pr["closed_at"].
    - Use _normalize_timestamp_to_utc() for timestamp fields.
    - ON CONFLICT (provider, repo, pr_number) DO UPDATE with similar COALESCE/LEAST logic.
    - Use RETURNING id; fallback SELECT if needed.
    """
    # TODO: Implement upsert logic
    pass

def ingest_pull_requests(provider: str, repo: str, prs: Iterable[Dict[str, Any]]) -> int:
    """Ingest multiple pull requests; return count processed.
    
    Implementation guidance:
    - Loop over prs iterable.
    - Call upsert_pull_request(provider, repo, pr) for each.
    - Return total count processed.
    """
    # TODO: Implement
    pass

# ---- CI Pipelines & Jobs ----

def upsert_ci_pipeline(provider: str, repo: str, pipe: Dict[str, Any]) -> int:
    """Insert/update a CI pipeline idempotently; return db id.
    
    Implementation guidance:
    - INSERT into ci_pipelines with fields: provider, repo, pipeline_id,
      status, created_at, updated_at, sha.
    - Use str(pipe["pipeline_id"]) to handle large integers.
    - Use _normalize_timestamp_to_utc() for timestamp fields.
    - ON CONFLICT (provider, repo, pipeline_id) DO UPDATE:
      status=EXCLUDED.status,
      created_at=LEAST(ci_pipelines.created_at, EXCLUDED.created_at),
      updated_at=COALESCE(EXCLUDED.updated_at, ci_pipelines.updated_at),
      sha=COALESCE(EXCLUDED.sha, ci_pipelines.sha).
    - Use RETURNING id; fallback SELECT if needed.
    """
    # TODO: Implement upsert logic
    pass

def upsert_ci_job(provider: str, repo: str, job: Dict[str, Any]) -> int:
    """Insert/update a CI job idempotently; return db id.
    
    Implementation guidance:
    - INSERT into ci_jobs with fields: provider, repo, pipeline_id, job_id,
      name, status, started_at, finished_at, duration_seconds.
    - Convert job_id and pipeline_id to strings.
    - Use _normalize_timestamp_to_utc() for timestamp fields (if not None).
    - For duration_seconds: int(job.get("duration_seconds", 0)) if not None else None.
    - ON CONFLICT (provider, repo, job_id) DO UPDATE with COALESCE logic.
    - Use RETURNING id; fallback SELECT if needed.
    """
    # TODO: Implement upsert logic
    pass

def ingest_ci(provider: str, repo: str, pipelines: Iterable[Dict[str, Any]], 
              jobs_by_pipeline: Optional[Dict[str, Iterable[Dict[str, Any]]]] = None) -> int:
    """Ingest pipelines and their jobs. Returns number of pipelines processed.
    
    Implementation guidance:
    - Loop over pipelines iterable.
    - For each pipeline, call upsert_ci_pipeline(provider, repo, pipe).
    - If jobs_by_pipeline is provided:
      - Get pipeline_id as str(pipe["pipeline_id"]).
      - For each job in jobs_by_pipeline.get(pipeline_id, []):
        - Ensure job has "pipeline_id" set
        - Call upsert_ci_job(provider, repo, job).
    - Return total count of pipelines processed.
    """
    # TODO: Implement
    pass

# ---------------------------------------------
# Helper Functions -- some are provided, others are stubbed; you can implement them or decide on your own way of providing similar functionality
# ---------------------------------------------

import requests

def fetch_json(url: str, headers: Optional[Dict[str, str]] = None, 
               params: Optional[Dict[str, Any]] = None) -> Dict[str, Any]:
    """Lightweight GET JSON wrapper."""
    resp = requests.get(url, headers=headers or {}, params=params or {}, timeout=30)
    resp.raise_for_status()
    return resp.json()

def collect_github_issues(owner_repo: str, state: str = "all", 
                          token: Optional[str] = None, 
                          per_page: int = 100, max_pages: int = 1) -> Iterable[Dict[str, Any]]:
    """Generator yielding normalized issue dicts from GitHub REST v3."""
    owner, repo = owner_repo.split("/", 1)
    headers = {"Accept": "application/vnd.github+json"}
    if token:
        headers["Authorization"] = f"Bearer {token}"
    url = f"https://api.github.com/repos/{owner}/{repo}/issues"
    page = 1
    while page <= max_pages:
        data = fetch_json(url, headers=headers, params={"state": state, "per_page": per_page, "page": page})
        if not data:
            break
        for it in data:
            if "pull_request" in it:
                # skip PRs here; use collect_github_pulls for PR details
                continue
            yield {
                "number": it["number"],
                "title": it.get("title", ""),
                "author": (it.get("user") or {}).get("login"),
                "state": it.get("state", "open"),
                "created_at": it.get("created_at"),
                "closed_at": it.get("closed_at"),
            }
        page += 1

def collect_github_pulls(owner_repo: str, state: str = "all", 
                        token: Optional[str] = None, 
                        per_page: int = 100, max_pages: int = 1) -> Iterable[Dict[str, Any]]:
    """Generator yielding normalized pull request dicts from GitHub REST v3.
    
    Implementation guidance:
    - URL: https://api.github.com/repos/{owner}/{repo}/pulls
    - Headers: Accept: application/vnd.github+json, Authorization if token provided
    - Query params: state={state}, per_page={per_page}, page={page}
    - Normalize state: if pr.get("merged_at") then state="merged", else pr.get("state", "open")
    - Yield dict with: number, title, author (from user.login), state, 
      created_at, merged_at, closed_at
    """
    # TODO: Implement (similar pattern to collect_github_issues)
    pass

def collect_github_actions_runs(owner_repo: str, token: Optional[str] = None, 
                                per_page: int = 100, max_pages: int = 1) -> Iterable[Dict[str, Any]]:
    """Yield pipelines from GitHub Actions workflow runs.
    
    Implementation guidance:
    - URL: https://api.github.com/repos/{owner}/{repo}/actions/runs
    - Headers: Accept: application/vnd.github+json, Authorization if token provided
    - Response is a dict with "workflow_runs" key containing array
    - For each run r, yield dict with:
      pipeline_id: r["id"]
      status: r.get("conclusion") or r.get("status") or "unknown"
      created_at: r.get("created_at")
      updated_at: r.get("updated_at")
      sha: r.get("head_sha")
    """
    # TODO: Implement
    pass

def collect_github_actions_jobs(owner_repo: str, run_id: str, 
                                token: Optional[str] = None, 
                                per_page: int = 100, max_pages: int = 1) -> Iterable[Dict[str, Any]]:
    """Yield jobs for a specific GitHub Actions run.
    
    Implementation guidance:
    - URL: https://api.github.com/repos/{owner}/{repo}/actions/runs/{run_id}/jobs
    - Headers: Accept: application/vnd.github+json, Authorization if token provided
    - Response is a dict with "jobs" key containing array
    - For each job j, yield dict with:
      pipeline_id: str(run_id)
      job_id: j["id"]
      name: j.get("name")
      status: j.get("conclusion") or j.get("status")
      started_at: j.get("started_at")
      finished_at: j.get("completed_at")
      duration_seconds: j.get("duration_ms", 0) // 1000 if j.get("duration_ms") else None
    """
    # TODO: Implement
    pass
```

---

## Required Schema (Additions to `data/schema.sql`)

Create these tables **in addition to** your DC1 tables. Keys indicated with **UNIQUE** are required.

**Important:** To use our new `_normalize_timestamp_to_utc` function, you should update your DC1 timestamp columns to use `TIMESTAMPTZ` instead of `TIMESTAMP`

This ensures all timestamps are stored with timezone awareness (UTC) for consistency with GitHub API timestamps and proper timezone handling. While we will likely not run into many of these types of problems solely using Github, if we add other sources, then timezone normalization becomes very important.

```sql
-- Issues (DC2)
CREATE TABLE IF NOT EXISTS issues (
    id SERIAL PRIMARY KEY,
    provider TEXT NOT NULL,                 -- e.g., 'github', 'gitlab'
    repo TEXT NOT NULL,                     -- 'owner/repo' or 'group/project'
    issue_number INTEGER NOT NULL,          
    title TEXT NOT NULL,
    author TEXT,
    state TEXT NOT NULL,                    -- 'open', 'closed', etc.
    created_at TIMESTAMPTZ NOT NULL,
    closed_at TIMESTAMPTZ
);
CREATE UNIQUE INDEX IF NOT EXISTS uq_issue_identity ON issues(provider, repo, issue_number);

-- Pull / Merge Requests (DC2)
CREATE TABLE IF NOT EXISTS pull_requests (
    id SERIAL PRIMARY KEY,
    provider TEXT NOT NULL,
    repo TEXT NOT NULL,
    pr_number INTEGER NOT NULL,
    title TEXT NOT NULL,
    author TEXT,
    state TEXT NOT NULL,                    -- 'open', 'closed', 'merged' (normalized, see notes in readme)
    created_at TIMESTAMPTZ NOT NULL,
    merged_at TIMESTAMPTZ,
    closed_at TIMESTAMPTZ
);
CREATE UNIQUE INDEX IF NOT EXISTS uq_pr_identity ON pull_requests(provider, repo, pr_number);

-- CI Pipelines (DC2)
CREATE TABLE IF NOT EXISTS ci_pipelines (
    id SERIAL PRIMARY KEY,
    provider TEXT NOT NULL,
    repo TEXT NOT NULL,
    pipeline_id TEXT NOT NULL,              -- provider-visible id (string to handle big ints)
    status TEXT NOT NULL,
    created_at TIMESTAMPTZ NOT NULL,
    updated_at TIMESTAMPTZ,
    sha TEXT                                -- commit hash the pipeline ran for (if known)
);
CREATE UNIQUE INDEX IF NOT EXISTS uq_pipeline_identity ON ci_pipelines(provider, repo, pipeline_id);

-- CI Jobs (DC2)
CREATE TABLE IF NOT EXISTS ci_jobs (
    id SERIAL PRIMARY KEY,
    provider TEXT NOT NULL,
    repo TEXT NOT NULL,
    pipeline_id TEXT NOT NULL,
    job_id TEXT NOT NULL,
    name TEXT,
    status TEXT,
    started_at TIMESTAMPTZ,
    finished_at TIMESTAMPTZ,
    duration_seconds INTEGER
);
CREATE UNIQUE INDEX IF NOT EXISTS uq_job_identity ON ci_jobs(provider, repo, job_id);
```
---

## Test Data Seeds (You Create These Inside Tests)

Use only small, synthetic inputs. Keep them deterministic and comprehensible.

1. **Issues + PRs seed (provider-agnostic)**
   - Provider: 'github' (but we could support others!).
   - Repo string: 'acme/widgets'.
   - Issues array with two entries:
     - #1 state='open'.
     - #2 state='closed' with a closed_at timestamp.
   - PRs array with two entries:
     - #10 state='open'.
     - #11 with a non-null merged_at (expect normalized state merged).

2. **CI seed (linked to a real commit)**
   - Build a temporary repo with **≥ 2 commits** (as in DC1).
   - Mine commits via your DC1 miner.
   - Create a pipelines array with one pipeline:
     - pipeline_id='1001', status='success', sha={HEAD commit hash}.
     - created_at and updated_at separated by ~5 minutes.
   - Create jobs_by_pipeline['1001'] with two jobs:
     - job_id='2001', name='build', successive timing, duration_seconds=120.
     - job_id='2002', name='test', successive timing, `duration_seconds=180`.

3. **Idempotency replay**
   - Re-run the exact same ingestions. Expect **no duplicate rows**.

> Set `user.name` and `user.email` in the temporary repo as in DC1 to avoid identity issues.

---

## Test Case Sketches (Acceptance Criteria)

Treat each sketch as a requirement. Your actual tests can combine steps, but keep them small and focused.

### A. Issues/PRs are upserted idempotently
- **Given** issues & PRs seeds (two each) for a provider/repo
- **When** calling `ingest_issues` and `ingest_pull_requests` twice
- **Then** `COUNT(issues) == 2` and `COUNT(pull_requests) == 2`  
- **And** PR with non-null `merged_at` has normalized state `merged`

### B. CI pipelines/jobs are upserted idempotently
- **Given** the CI seed (1 pipeline, 2 jobs)
- **When** calling `ingest_ci` twice
- **Then** `COUNT(ci_pipelines) == 1` and `COUNT(ci_jobs) == 2`

### C. CI pipeline SHA matches a mined commit
- **Given** the CI seed with `sha=HEAD` of the mined repo
- **Then** a query `SELECT 1 FROM commits WHERE commit_hash = sha` returns a row

### D. DC1 tests still pass
- **Given** your DC1 repository and schema now extended for DC2
- **Then** previously written DC1 tests pass unchanged (minor message changes are acceptable, but **schema and behavior guarantees must hold**).

### E. Timestamp coercion is robust (lightweight)
- **Given** ISO8601 timestamps with or without 'Z' and with/without fractional seconds
- **When** ingesting records
- **Then** the rows are inserted with valid TIMESTAMP values (no crashes; edge cases can be covered with a small parametrized test).

### F. Add 2 New Tests in addition to the ones above
- Recall that tests should test average cases as well as corner cases. Come up with at least 2 addition test cases. They can augment the required tests above, or you can add unrelated tests that make sense to add. In your README.MD (this one), list the new tests you added and argue for why they are appropriate tests. You may add more than 2 new tests if you would like.

---

## Deliverables

1. Updated `data/schema.sql` with **all** DC2 tables and required constraints.
2. Updated `src/` code implementing the required functions above
3. Tests under `tests/` that implement the **Test Data Seeds** and **Test Case Sketches**.
4. Updated `main.py` that allows you to run your new features on real repositories
5. A README section titled **New Tests** and **New Functions** that explains:
   - Which tests you added and why these are appropriate tests
   - How to run your new features (give example commands)
6. Tag your submission as DC2

---

## Hints

- Treat the provider string and repo string as part of the **primary identity**.
- **Normalize PR state to `merged` if `merged_at` is non-null**, even if provider says `closed`. This is critical because:
  - GitHub API returns `state="closed"` for **both** merged PRs and rejected/abandoned PRs
  - The only reliable way to distinguish is checking if `merged_at` has a timestamp
  - For research, these are fundamentally different outcomes:
    - **Merged**: Code accepted, integrated into main branch (successful collaboration)
    - **Closed without merge**: Code rejected, withdrawn, or superseded (different process implications)
  - Without this normalization, your analysis would incorrectly group merged and rejected PRs together, making it impossible to study merge rates, review effectiveness, or developer productivity accurately
- Favor `ON CONFLICT ... DO UPDATE` for mutable fields (titles, statuses, timestamps) and `... DO NOTHING` for immutable keys.
- Keep tests network-free; mock or feed normalized dicts directly to ingestion functions.

---