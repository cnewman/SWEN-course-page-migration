---
title: 'Data Collection II'
date: '2025-09-29T11:31:00-04:00'

weight: 3
bookToC: true
bookSearchExclude: false

draft: true
---

## Data Collection II Ecosystem Artifacts

**Mode:** Test-driven assignment — your **test sketches** and **test data seeds** *are* the requirements you will implement against.

**Builds on:** DC1 (commit mining, idempotent ETL, invariants). DC2 extends the schema and code to ingest **issues**, **pull/merge requests**, and **CI pipelines/jobs** while keeping DC1 behavior intact.

### Goal

Augment your miner to collect provider-agnostic ecosystem data and persist it **idempotently**:

- **Issues** (opened/closed + metadata)
- **Pull/Merge Requests** (state incl. merged)
- **CI Pipelines/Jobs** (status, timing, and optional linkage to a commit SHA)

This expands the research substrate for later modeling: *How do code changes, review activity, and CI outcomes interrelate?*

### Learning Outcomes

1. **Provider-Agnostic Ingestion:** Normalize GitHub/GitLab JSON into a stable relational shape.
2. **Schema Design for Idempotency:** Use composite unique keys and conflict-aware upserts to make re-runs safe.
3. **Traceability:** Link CI pipelines to commits via `sha` when available.
4. **Reproducible Tests:** Drive development with small, deterministic tests and synthetic seeds.
5. **Research Ops Thinking:** Document what you sampled, how you sampled it, and how re-runs produce the same result set.

### What You Will Implement

Extend your `src/` code with idempotent ingestion helpers (exact names are **required** so that our autograder can find them):

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

> You may add small helper functions (e.g., timestamp coercion), but keep public function names and signatures above intact.

### Required Schema

Create these tables **in addition to** your DC1 tables. Keys indicated with **UNIQUE** are required.

```
issues(
  id SERIAL PK,
  provider TEXT NOT NULL,
  repo TEXT NOT NULL,
  issue_number INT NOT NULL,
  title TEXT NOT NULL,
  author TEXT,
  state TEXT NOT NULL,
  created_at TIMESTAMP NOT NULL,
  closed_at TIMESTAMP,
  UNIQUE(provider, repo, issue_number)
)

pull_requests(
  id SERIAL PK,
  provider TEXT NOT NULL,
  repo TEXT NOT NULL,
  pr_number INT NOT NULL,
  title TEXT NOT NULL,
  author TEXT,
  state TEXT NOT NULL,   -- 'open' | 'closed' | 'merged' (normalized)
  created_at TIMESTAMP NOT NULL,
  merged_at TIMESTAMP,
  closed_at TIMESTAMP,
  UNIQUE(provider, repo, pr_number)
)

ci_pipelines(
  id SERIAL PK,
  provider TEXT NOT NULL,
  repo TEXT NOT NULL,
  pipeline_id TEXT NOT NULL,
  status TEXT NOT NULL,
  created_at TIMESTAMP NOT NULL,
  updated_at TIMESTAMP,
  sha TEXT,
  UNIQUE(provider, repo, pipeline_id)
)

ci_jobs(
  id SERIAL PK,
  provider TEXT NOT NULL,
  repo TEXT NOT NULL,
  pipeline_id TEXT NOT NULL,
  job_id TEXT NOT NULL,
  name TEXT,
  status TEXT,
  started_at TIMESTAMP,
  finished_at TIMESTAMP,
  duration_seconds INT,
  UNIQUE(provider, repo, job_id)
)

```

### Test Data Seeds

Use only small, synthetic inputs. Keep them deterministic and comprehensible.

1. **Issues + PRs seed (provider-agnostic)**
   - Provider: `'github'` (or `'gitlab'` if you prefer).
   - Repo string: `'acme/widgets'`.
   - Issues array with two entries:
     - #1 `state='open'`.
     - #2 `state='closed'` with a `closed_at` timestamp.
   - PRs array with two entries:
     - #10 `state='open'`.
     - #11 with a non-null `merged_at` (expect normalized state `merged`).

2. **CI seed (linked to a real commit)**
   - Build a temporary repo with **≥ 2 commits** (as in DC1).
   - Mine commits via your DC1 miner.
   - Create a pipelines array with one pipeline:
     - `pipeline_id='1001'`, `status='success'`, `sha={HEAD commit hash}`.
     - `created_at` and `updated_at` separated by ~5 minutes.
   - Create `jobs_by_pipeline['1001']` with two jobs:
     - `job_id='2001'`, `name='build'`, successive timing, `duration_seconds=120`.
     - `job_id='2002'`, `name='test'`, successive timing, `duration_seconds=180`.

3. **Idempotency replay**
   - Re-run the exact same ingestions. Expect **no duplicate rows**.

> Set `user.name` and `user.email` in the temporary repo as in DC1 to avoid identity issues.

### Test Case Sketches

Treat each sketch as a requirement. Your actual tests can combine steps, but keep them small and focused.

#### A. Issues/PRs are upserted idempotently
- **Given** issues & PRs seeds (two each) for a provider/repo
- **When** calling `ingest_issues` and `ingest_pull_requests` twice
- **Then** `COUNT(issues) == 2` and `COUNT(pull_requests) == 2`  
- **And** PR with non-null `merged_at` has normalized state `merged`

#### B. CI pipelines/jobs are upserted idempotently
- **Given** the CI seed (1 pipeline, 2 jobs)
- **When** calling `ingest_ci` twice
- **Then** `COUNT(ci_pipelines) == 1` and `COUNT(ci_jobs) == 2`

#### C. CI pipeline SHA matches a mined commit
- **Given** the CI seed with `sha=HEAD` of the mined repo
- **Then** a query `SELECT 1 FROM commits WHERE commit_hash = sha` returns a row

#### D. DC1 tests still pass
- **Given** your DC1 repository and schema now extended for DC2
- **Then** previously written DC1 tests pass unchanged (minor message changes are acceptable, but **schema and behavior guarantees must hold**).

#### E. Timestamp coercion is robust (lightweight)
- **Given** ISO8601 timestamps with or without 'Z' and with/without fractional seconds
- **When** ingesting records
- **Then** the rows are inserted with valid TIMESTAMP values (no crashes; edge cases can be covered with a small parametrized test).

### Commands

- Apply schema (make sure DC1 + DC2 statements are present):
  ```bash
  python -c "from src import db_utils; db_utils.exec_sql_file('data/schema.sql')"
  ```

- Run all tests:
  ```bash
  pytest -q
  ```

- (Optional) Manual smoke test in a local repo:
  ```bash
  python - <<'PY'
  from src.git_miner import mine_history, ingest_issues, ingest_pull_requests, ingest_ci
  from datetime import datetime, timedelta
  # Mine commits
  mine_history('.', max_commits=5)

  # Issues/PRs
  issues=[{'number':1,'title':'Bug','author':'alice','state':'open','created_at':datetime.utcnow().isoformat()}]
  prs=[{'number':10,'title':'Fix','author':'bob','state':'open','created_at':datetime.utcnow().isoformat()}]
  ingest_issues('github','me/repo',issues)
  ingest_pull_requests('github','me/repo',prs)

  # CI
  head_sha = __import__('src.db_utils', fromlist=['']).exec_get_one('SELECT commit_hash FROM commits ORDER BY id DESC LIMIT 1;')[0]
  now = datetime.utcnow()
  ingest_ci('github','me/repo',[{'pipeline_id':'1','status':'success','created_at':now.isoformat(),'updated_at':now.isoformat(),'sha':head_sha}],{})
  print('OK')
  PY
  ```

### Deliverables

1. Updated `data/schema.sql` with **all** DC2 tables and required constraints.
2. Updated `src/` code implementing the three ingestion functions above.
3. Tests under `tests/` that implement the **Test Data Seeds** and **Test Case Sketches**.
4. A short `README` section **Research Ops Notes** documenting your sampling choices:
   - Provider(s) and repo identifier(s) used in tests,
   - Fields captured and normalization decisions,
   - How idempotency is enforced (keys + conflict strategy).

### Grading Outline

- **Schema & Constraints:** Correct DC2 tables, proper unique identities, helpful indexes.
- **Idempotent Ingestion:** Re-runs do not duplicate; mutable fields update appropriately.
- **Traceability:** CI pipelines include `sha` when available; sanity join to commits proven.
- **Test Quality:** Small, focused, deterministic; cover idempotency and normalization.
- **DC1 Compatibility:** Prior tests still pass; no regressions in DC1 guarantees.
- **Code Quality:** Clear function boundaries, robust timestamp handling, concise helpers.
- **Documentation:** Research Ops Notes clearly state what/why/how of your sampling.

### Hints

- Treat the provider string and repo string as part of the **primary identity**.
- Normalize PR state to `merged` if `merged_at` is non-null, even if provider says `closed`.
- Favor `ON CONFLICT ... DO UPDATE` for mutable fields (titles, statuses, timestamps) and `... DO NOTHING` for immutable keys.
- Keep tests network-free; mock or feed normalized dicts directly to ingestion functions.

### Stretch Goals (Optional)

- Add **labels** and **assignees** tables (many-to-many from issues/PRs).
- Add a lightweight **pull_request_commits** table linking PRs to commit SHAs (provider API dependent).
- Add a `run_log` analog for DC2 collection runs with `started_at`, `provider`, `repo`, and counts ingested.
- CLI wrapper: `python -m src.cli ingest --provider github --repo owner/name --artifacts issues,prs,ci`