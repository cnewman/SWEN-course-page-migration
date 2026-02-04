---
title: 'Data Integrity I'

weight: 3
bookToC: true
bookSearchExclude: false

draft: true
---

# STRATA — DI1: Data Integrity I (Qualitative Text Normalization)

**Mode:** Test-driven assignment — **tests are provided** to define the expected behavior.

**Builds on:** DC0/DC1/DC2 (Postgres helpers, commit mining, ecosystem artifacts) and prepares the dataset for later modeling by normalizing free text.

---

## Goal

Implement three pure functions that normalize qualitative artifacts from software repositories (issue/PR titles, commit messages, user logins). These normalizations are important for Mining Software Repositories (MSR) research because raw data contains inconsistencies that can skew analysis.

This assignment will teach you how to:
- Canonicalize user logins and detect bot accounts
- Clean Markdown formatting from text while preserving analyzable content
- Parse Conventional Commit messages into structured components

---

## Learning Outcomes

1. Write deterministic, well-documented pure functions for text normalization.
2. Parse structured conventions ([Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)) out of commit messages.
3. Understand why data normalization matters for research validity.
4. Use regex patterns for text transformation.

---

## What You Will Implement

You must implement **three functions** in `src/qual_clean.py`. The DB helper functions are **provided for you** — you should not need to modify them

```python
from typing import Any, Dict, Optional, Tuple

# ---- User Canonicalization ----

def canonicalize_user(login: Optional[str]) -> Tuple[str, bool]:
    """Canonicalize a user login and detect bot accounts.

    Why this matters for MSR:
    - Contributors appear with inconsistent casing ("Alice" vs "alice")
    - Bot accounts should often be filtered from human contributor analysis
    - Consistent formats enable accurate contributor metrics

    Parameters:
    - login: raw login string (may be None or empty)

    Returns:
    - (login_norm, is_bot) tuple where:
      - login_norm: lowercase, stripped login (empty string for falsy inputs)
      - is_bot: True if login matches common bot patterns

    Bot detection hints - look for these patterns:
    - "[bot]" suffix (e.g., "dependabot[bot]")
    - Known names: "dependabot", "renovate", "github-actions", "gitlab-ci"

    Examples:
    >>> canonicalize_user("Alice")
    ('alice', False)
    >>> canonicalize_user("dependabot[bot]")
    ('dependabot[bot]', True)
    >>> canonicalize_user(None)
    ('', False)
    """
    # TODO: Implement
    pass

# ---- Text Normalization ----

def normalize_text(md: Optional[str]) -> str:
    """Convert Markdown-like text into clean plain text for analysis.

    Why this matters for MSR:
    - Issue/PR bodies contain Markdown that obscures actual content
    - Code blocks should be preserved but marked for analysis
    - Consistent whitespace enables text comparison and NLP

    Parameters:
    - md: raw text that may contain Markdown (None returns empty string)

    Returns:
    - Cleaned text with these transformations:
      - Fenced code blocks (```...```) → <CODE>...</CODE>
      - Inline code (`...`) → <CODE>...</CODE>
      - Heading markers (e.g., "## ") removed
      - List bullets (e.g., "- ", "* ", "1. ") removed
      - Windows/Mac newlines → Unix \\n
      - Runs of 3+ blank lines → 2 blank lines
      - Runs of 2+ spaces/tabs → 1 space
      - ASCII control characters removed (except newlines)
      - URLs preserved as-is

    Examples:
    >>> normalize_text("# Hello World")
    'Hello World'
    >>> normalize_text("Use `print()` to debug")
    'Use <CODE>print()</CODE> to debug'
    >>> normalize_text(None)
    ''

    Implementation hints:
    - Use re.compile() for regex patterns
    - Process fenced code blocks BEFORE inline code (order matters!)
    - re.DOTALL makes . match newlines
    - re.MULTILINE makes ^ match line starts
    """
    # TODO: Implement
    pass

# ---- Commit Message Parsing ----

def split_commit_message(msg: Optional[str]) -> Dict[str, Any]:
    """Parse a commit message into Conventional Commit components.

    Why this matters for MSR:
    - Conventional Commits provide semantic meaning (feat, fix, etc.)
    - Enables automated analysis of development practices
    - Breaking changes can be systematically identified

    Conventional Commits format: <type>[optional scope][!]: <subject>
    Examples:
    - "feat(parser): add array support" → type=feat, scope=parser
    - "fix: correct typo" → type=fix, scope=None
    - "feat!: breaking API change" → type=feat, breaking=True

    Parameters:
    - msg: raw commit message (subject + optional body). None treated as empty.

    Returns:
    - dict with keys: subject, body, type, scope, breaking
      - subject: first line (or CC description if CC format)
      - body: everything after first line, stripped
      - type: CC type lowercase (e.g., 'feat', 'fix') or None
      - scope: CC scope if present, or None
      - breaking: True if '!' indicates breaking change

    Examples:
    >>> split_commit_message("fix(auth): resolve login bug")
    {'subject': 'resolve login bug', 'body': '', 'type': 'fix', 'scope': 'auth', 'breaking': False}

    >>> split_commit_message("Update readme\\n\\nMore details here")
    {'subject': 'Update readme', 'body': 'More details here', 'type': None, 'scope': None, 'breaking': False}

    >>> split_commit_message(None)
    {'subject': '', 'body': '', 'type': None, 'scope': None, 'breaking': False}

    Implementation hints:
    - First normalize newlines and split into subject (line 1) and body
    - Use a regex to match CC pattern: type(scope)!: subject
    - Remember to lowercase the type in output
    """
    # TODO: Implement
    pass
```

---

## Provided Tests

Tests are provided in [test_qual_clean.py](/code/test_qual_clean.py) (64 test cases). These tests define the expected behavior for each function. Run them frequently as you implement:

The tests are organized into three classes:
- **`TestCanonicalizeUser`** — 17 tests covering normal users, bot detection, whitespace, edge cases
- **`TestNormalizeText`** — 25 tests covering code blocks, headings, lists, URLs, newlines, whitespace, control chars
- **`TestSplitCommitMessage`** — 22 tests covering CC format variants, plain messages, multi-line bodies

Study the test cases to understand exactly what each function should do. For example:

```python
# From TestCanonicalizeUser
("Alice", "alice", False),           # lowercase normal user
("dependabot[bot]", "dependabot[bot]", True),  # detect [bot] suffix
("GitHub-Actions", "github-actions", True),     # detect known bot name

# From TestNormalizeText
def test_inline_code_basic(self):
    md = "Use `print()` to debug"
    out = qual_clean.normalize_text(md)
    assert "<CODE>print()</CODE>" in out
    assert "`" not in out

# From TestSplitCommitMessage
def test_cc_scope_and_breaking(self):
    result = qual_clean.split_commit_message("fix(api)!: remove deprecated endpoint")
    assert result["type"] == "fix"
    assert result["scope"] == "api"
    assert result["breaking"] is True
```

---

## Schema Notes

The DB helpers (provided below) add these columns to your existing tables:

- `issues` / `pull_requests`: `title_clean TEXT`, `author_norm TEXT`, `is_bot BOOLEAN`
- `commits`: `subject TEXT`, `body TEXT`, `cc_type TEXT`, `cc_scope TEXT`, `cc_breaking BOOLEAN`

You likely won't need to modify the schema — the `ensure_columns()` function will do all modifications unless your db varies from what we have given you up to this point.

These will add columns for your clean/normalized data to your tables.

```py
# ---------------------------------------------------------------------------
# Database helpers - Modify if you need to, but most likely you do not.
# ---------------------------------------------------------------------------

def ensure_columns():
    """
    Create normalized columns on database tables if they don't exist.
    Idempotent - safe to run multiple times.
    """
    if db_utils is None:
        return
    stmts = [
        "ALTER TABLE issues ADD COLUMN IF NOT EXISTS title_clean TEXT;",
        "ALTER TABLE issues ADD COLUMN IF NOT EXISTS author_norm TEXT;",
        "ALTER TABLE issues ADD COLUMN IF NOT EXISTS is_bot BOOLEAN DEFAULT FALSE;",

        "ALTER TABLE pull_requests ADD COLUMN IF NOT EXISTS title_clean TEXT;",
        "ALTER TABLE pull_requests ADD COLUMN IF NOT EXISTS author_norm TEXT;",
        "ALTER TABLE pull_requests ADD COLUMN IF NOT EXISTS is_bot BOOLEAN DEFAULT FALSE;",

        "ALTER TABLE commits ADD COLUMN IF NOT EXISTS subject TEXT;",
        "ALTER TABLE commits ADD COLUMN IF NOT EXISTS body TEXT;",
        "ALTER TABLE commits ADD COLUMN IF NOT EXISTS cc_type TEXT;",
        "ALTER TABLE commits ADD COLUMN IF NOT EXISTS cc_scope TEXT;",
        "ALTER TABLE commits ADD COLUMN IF NOT EXISTS cc_breaking BOOLEAN DEFAULT FALSE;",
    ]
    for s in stmts:
        db_utils.exec_commit(s)


def clean_issues_db(limit: Optional[int] = None) -> int:
    """
    Apply normalization to issues table. Returns count of rows processed.
    """
    if db_utils is None:
        return 0
    ensure_columns()
    rows = db_utils.exec_query(
        "SELECT id, title, author FROM issues"
        + (f" LIMIT {int(limit)}" if limit else "")
        + ";"
    )
    count = 0
    for (iid, title, author) in rows:
        title_clean = normalize_text(title or "")
        author_norm, is_bot = canonicalize_user(author)
        db_utils.exec_commit(
            """
            UPDATE issues SET
                title_clean=%(title_clean)s,
                author_norm=%(author_norm)s,
                is_bot=%(is_bot)s
            WHERE id=%(id)s;
            """,
            {
                "title_clean": title_clean,
                "author_norm": author_norm,
                "is_bot": is_bot,
                "id": iid,
            }
        )
        count += 1
    return count


def clean_prs_db(limit: Optional[int] = None) -> int:
    """
    Apply normalization to pull_requests table. Returns count of rows processed.
    """
    if db_utils is None:
        return 0
    ensure_columns()
    rows = db_utils.exec_query(
        "SELECT id, title, author FROM pull_requests"
        + (f" LIMIT {int(limit)}" if limit else "")
        + ";"
    )
    count = 0
    for (pid, title, author) in rows:
        title_clean = normalize_text(title or "")
        author_norm, is_bot = canonicalize_user(author)
        db_utils.exec_commit(
            """
            UPDATE pull_requests SET
                title_clean=%(title_clean)s,
                author_norm=%(author_norm)s,
                is_bot=%(is_bot)s
            WHERE id=%(id)s;
            """,
            {
                "title_clean": title_clean,
                "author_norm": author_norm,
                "is_bot": is_bot,
                "id": pid,
            }
        )
        count += 1
    return count


def clean_commits_db(limit: Optional[int] = None) -> int:
    """
    Apply commit message parsing to commits table. Returns count of rows processed.
    """
    if db_utils is None:
        return 0
    ensure_columns()
    rows = db_utils.exec_query(
        "SELECT id, message FROM commits"
        + (f" LIMIT {int(limit)}" if limit else "")
        + ";"
    )
    count = 0
    for (cid, message) in rows:
        parts = split_commit_message(message or "")
        db_utils.exec_commit(
            """
            UPDATE commits SET
                subject=%(subject)s,
                body=%(body)s,
                cc_type=%(cc_type)s,
                cc_scope=%(cc_scope)s,
                cc_breaking=%(cc_breaking)s
            WHERE id=%(id)s;
            """,
            {
                "subject": parts["subject"],
                "body": parts["body"],
                "cc_type": parts["type"],
                "cc_scope": parts["scope"],
                "cc_breaking": parts["breaking"],
                "id": cid,
            }
        )
        count += 1
    return count
```
---

## Deliverables

1. **Implementation** of the three functions in `src/qual_clean.py`
2. All **64 tests passing** in `test_qual_clean.py`
3. A brief section below documenting your implementation decisions

---

## Grading Outline

| Component | Weight |
|-----------|--------|
| `canonicalize_user` — all tests pass | 30% |
| `normalize_text` — all tests pass | 30% |
| `split_commit_message` — all tests pass | 30% |
| `main.py updated` - runs normalization without issue | 10% |
---
