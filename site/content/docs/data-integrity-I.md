---
title: 'Data Integrity I'

weight: 3
bookToC: true
bookSearchExclude: false

draft: true
---

# STRATA — DI1: Data Integrity I (Qualitative text normalization)

---

## Goal

Implement a small, well-tested library that normalizes and parses qualitative artifacts (issue/pr titles, commit messages, and free-text bodies). Provide a few optional, idempotent DB helpers that apply these normalizations to existing tables. The emphasis is: robust pure functions + small, safe side-effectful updaters.

This assignment will teach you how to: coerce noisy text/timestamp inputs to canonical shapes, parse Conventional Commits, canonicalize account logins (bot detection), and write idempotent DB enrichment helpers that are safe to re-run.

## Learning outcomes

1. Write deterministic, well-documented pure functions for text and timestamp normalization.
2. Parse structured conventions (Conventional Commits) out of commit messages.
3. Design small, idempotent DB updates and understand how schema choices support safe re-runs.
4. Test text-cleaning edge cases (different newlines, control chars, Markdown, missing/None inputs).

## Required public functions (names & signatures)

You must implement the following functions so the autograder and tests can import them by name. Keep signatures exactly as below.

- normalize_timestamp(value: Any) -> Optional[datetime]
  - Accepts various timestamp-like inputs (None, empty, numeric, ISO strings with/without 'Z' and fractional seconds, datetime objects). Returns a naive UTC datetime or None for falsy inputs.

- normalize_text(md: Optional[str]) -> str
  - Light Markdown → plain text conversion. Requirements:
    - Replace fenced code blocks and inline code with a single token like `<CODE>...</CODE>` to preserve that content while making it analyzable.
    - Strip heading markers and list bullets.
    - Preserve URLs.
    - Normalize newlines to `\n`, collapse runs of blank lines to at most two, and remove control characters.
    - Return empty string for `None`.

- split_commit_message(msg: Optional[str]) -> Dict[str, Any]
  - Parse a raw commit message into `{subject, body, type, scope, breaking}` using Conventional Commit style when present. The returned `type` and `scope` should be None if absent. `breaking` should be a boolean.

- canonicalize_user(login: Optional[str]) -> Tuple[str, bool]
  - Return a normalized lowercase login and a boolean `is_bot` when the login looks like an automated account (examples: contain `[bot]`, `dependabot`, `renovate`, `github-actions`, `gitlab-ci`). Return `('', False)` for falsy logins.

Optional DB helpers (side-effectful; mark them as safe to run repeatedly and make them no-ops if DB helpers aren't available):

- ensure_columns() -> None
  - Add lightweight normalized columns (e.g., `title_clean`, `author_norm`, `is_bot`) to `issues` and `pull_requests` and commit parsing columns to `commits` if they do not already exist. Use `IF NOT EXISTS` style statements so the function is idempotent.
 - ensure_columns() -> None
   - Add lightweight normalized columns (e.g., `title_clean`, `author_norm`, `is_bot`) to `issues` and `pull_requests` and commit parsing columns to `commits` if they do not already exist. Use `IF NOT EXISTS` style statements so the function is idempotent.

- clean_issues_db(limit: Optional[int] = None) -> int
- clean_prs_db(limit: Optional[int] = None) -> int
- clean_commits_db(limit: Optional[int] = None) -> int
  - Walk corresponding tables, compute normalized fields using the pure functions above, and write updates with parameterized statements. Each should return the number of rows processed. These functions are optional for unit tests that only exercise pure logic, but include them or mock `db_utils` for integration tests.

## Test seeds you should create inside tests

Create small, deterministic fixtures (temporary in-memory strings or temporary DB rows) to cover the following:

1. Text normalization seeds
   - Markdown with fenced code, inline code, headings, lists, URLs, CRLF (`\r\n`), and control characters.
   - Expect the `<CODE>` token in outputs and heading/list markers removed.

2. Commit-message seeds
   - Conventional commit messages and plain messages:
     - "fix(scope): do the thing" → type=`'fix'`, scope=`'scope'`, breaking=False, subject=`'do the thing'`.
     - "feat!: breaking change subject" → type=`'feat'`, breaking=True, subject=`'breaking change subject'`.
     - multi-line messages where the first line is the subject and the rest is the body.

3. Timestamp seeds
   - ISO strings like `2020-01-01T12:34:56Z`, `2020-01-01T12:34:56.123456`, `2020-01-01 12:34:56`, `2020-01-01` and `None`/empty.

4. Canonicalization seeds
   - Bot-like logins (`Dependabot`, `alice[bot]`, `github-actions`) vs normal logins. Mixed-case inputs should return lowercase.

5. (Optional integration) DB enrichment seeds
   - A tiny temporary Postgres/SQLite (depending on your `db_utils`) workspace or a mocked `db_utils` that returns a few rows for `issues`, `pull_requests`, and `commits`. Run the `clean_*_db` helpers and assert that the update calls were made with normalized values and that functions are idempotent.

  Reference tests: a compact example test suite is provided at `test/test_qual_clean.py`. Use it as a template for how to structure your unit tests and seeds.

## Test case sketches (acceptance criteria)

- normalize_text collapses whitespace and preserves URLs.
- normalize_timestamp handles ISO variants and returns None for empty inputs.
- split_commit_message extracts CC fields when present and leaves plain messages intact.
- canonicalize_user lowercases and detects bots.
- DB helpers call the expected SQL (or perform the expected updates) and are safe to run multiple times (idempotent).

## Required schema notes

DI1 is about light enrichment of existing DC1/DC2 tables. You do not need to redesign the schema, but include these optional normalized columns (or ensure your `ensure_columns()` function creates them if missing):

- `issues` / `pull_requests`: `title_clean TEXT`, `author_norm TEXT`, `is_bot BOOLEAN DEFAULT FALSE`
- `commits`: `subject TEXT`, `body TEXT`, `cc_type TEXT`, `cc_scope TEXT`, `cc_breaking BOOLEAN DEFAULT FALSE`
 - `issues` / `pull_requests`: `title_clean TEXT`, `author_norm TEXT`, `is_bot BOOLEAN DEFAULT FALSE`
 - `commits`: `subject TEXT`, `body TEXT`, `cc_type TEXT`, `cc_scope TEXT`, `cc_breaking BOOLEAN DEFAULT FALSE`
- `ci_pipelines`: `sha_missing BOOLEAN DEFAULT FALSE`

Use `ALTER TABLE ... ADD COLUMN IF NOT EXISTS` to make updates safe to re-run in `ensure_columns()`.

## Commands

Run tests:

```powershell
pytest -q
```

Apply schema (if your instructions/tests require the DB):

```powershell
python -c "from src import db_utils; db_utils.exec_sql_file('data/schema.sql')"
```

Small manual smoke test (optional):

```powershell
python - <<'PY'
from src import qual_clean
print(qual_clean.normalize_text('# hi\n```py\nprint(1)\n```\nhttp://example.com'))
PY
```

## Deliverables

1. Tests under `test/` that exercise the required pure functions (happy paths + edge cases). Add small integration tests for DB helpers or mock `db_utils`.
2. A short README section `Data Integrity I` describing the normalization decisions you made and any non-obvious heuristics (e.g., how you detect bots, how code blocks are tokenized).
3. (Optional) Simple examples of how the DB enrichment helpers are idempotent and safe to re-run.

## Hints & implementation notes

- Favor pure functions that accept and return simple types (str, dict, datetime) so they are trivial to unit test.
- Keep DB logic isolated and clearly marked `# pragma: no cover` if you don't want it executed in unit tests that lack a DB.
- Where feasible, use `ON CONFLICT DO NOTHING` or `IF NOT EXISTS` to make schema and updates idempotent.
- Preserve URLs when stripping Markdown. A liberal approach is OK — the goal is analyzability, not lossless conversion.
- For timestamp parsing, try a small set of common formats first and fall back to `datetime.fromisoformat()` as a last resort.

## Grading outline

- Text normalization correctness (headings, lists, code markers): 30%
- Commit message parsing (CC extraction + sensible defaults): 25%
- Timestamp coercion robustness: 15%
- Tests & edge cases coverage: 20%
- Optional DB helpers' idempotency & safety: 10%
