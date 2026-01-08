# STRATA — DC0: Repository & Database Setup

**STRATA (Software daTa Repository Analysis & Testing Architecture)**

Welcome to **DC0**, the very first "layer" of STRATA.  
In this assignment, you will set up your research pipeline foundation: repository posture, database connectivity, and automated testing in CI/CD.

This mirrors the setup assignment in SWEN-610 but adapted for **research methods**.

---

## Objectives

By the end of DC0, you will be able to:

- Initialize a structured project repository with required directories.  
- Connect to a PostgreSQL database using provided utilities.  
- Insert and retrieve records from the database.  
- Use GitPython to mine commit metadata.  
- Run tests automatically in GitLab CI with **pytest**.

---

## Repository Structure

Your repo must follow this structure:

```
src/        # implementation code (db_utils.py, git_miner.py)
test/       # pytest tests + fixtures
data/       # SQL schema and future data
config/     # credentials (gitlab-credentials.yml -> copied to db.yml in CI)
requirements.txt
.gitlab-ci.yml
README.md
```

We provide you with:

- A `db_utils.py` for connecting to PostgreSQL and executing SQL.  
- A `git_miner.py` that uses GitPython to read the HEAD commit and insert into the DB.  
- A `schema.sql` file that defines a simple `commits` table.  
- Example pytest tests (`test_db_utils.py`, `test_git_miner.py`) and fixtures (`conftest.py`).  
- A GitLab CI file that runs PostgreSQL as a service, installs dependencies, and executes tests.

---

## Setup Instructions

1. **Clone your repo** (created for this course).

```shell
git clone <your-gitlab-repo-url>
cd <your-repo>
```

2. **Add provided scaffold** (from DC0 zip).  
     
   - Copy the contents into your repo.  
   - Commit and push.

   

3. **Database Credentials.**  
     
   - In CI, `config/gitlab-credentials.yml` is copied to `config/db.yml` automatically.  
   - For local dev, create a `config/db.yml` with keys matching your own Postgres instance:

```
database: swen344
user: swen344
password: whowatchesthewatchmen
host: localhost
port: 5432
```

4. **Install dependencies locally.**

```shell
pip install -r requirements.txt
```

5. **Initialize database schema.**  
   Use the helper in `db_utils.py`:

```py
from src import db_utils
db_utils.exec_sql_file('data/schema.sql')
```

6. **Run pytest locally.**

```shell
pytest -q
```

   You should see all tests pass (including the Git miner test that creates a temporary repo and commits).

   

7. **Push to GitLab.**  
   On push, GitLab CI will:  
     
   - Spin up a Postgres service.  
   - Install requirements (psycopg2, PyYAML, GitPython, pytest).  
   - Run the test suite.  
   - Confirm Git → DB pipeline works.

---

## Deliverables

- A correctly structured repo with all scaffold files.  
- Passing GitLab CI pipeline (all pytest tests green).  
- Local proof you can connect to Postgres, insert commits, and query them.

---

## Tips

- If you see `Bad git executable` in CI, ensure your `.gitlab-ci.yml` includes the `apt-get install git` step (already provided).  
- On Windows, GitPython can hold file handles; our scaffold closes repos to avoid PermissionErrors.  
- Keep this structure intact—future assignments (DC1, DI1, etc.) will build directly on top of it.

---

## Grading

You will be graded on:

- Proper repo structure (all required files present).  
- CI pipeline runs successfully.  
- Tests all pass.  
- Database schema is set up and accessible.