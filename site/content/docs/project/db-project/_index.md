---
title: "DB Project"

weight: 40
bookToC: true
bookSearchExclude: false

draft: true
---

## DB Project 0 - Project Setup

{{% steps %}}

1. Log into https://git.gccis.rit.edu using your RIT username and password.

    * If you have never logged into this GitLab instance, the instructor would have sent an invite email instead of add your username directly. You might get an error like `"This invitation was sent to abc123@rit.edu, but you are signed in as @abc123 with email abc123@g.rit.edu"`. Fear not! Just make sure you log in (so your account gets created), then contact your instructor and they’ll re-add you using your username.
    * You should find yourself already assigned to a group. If you are not in a group, get in touch with your instructor.
    * We are **not** using gitlab.com.

2. In your group, you need to create a repo called `db-abc123` where `abc123` is your username. Note that you have access to 4 other students’ repositories. You are going to be pushing code to your own repository and making comments on other students’ repositories. (See our [FAQ on working with other students’ code](/docs/syllabus/expectations#actionable-feedback)).

3. Clone your project repository locally using your favorite Git client. (See our [Git resources page](/git-resources) for some helpful resources.)

4. You will need [Python 3.12](https://www.python.org/downloads/) installed on your system. Be sure to add `python` to your PATH in installation. *SWEN lab machines:* If you are in the lab, this is pre-installed and available on the command line.

5. Install [PostgreSQL 17](https://www.postgresql.org/download/). Mac users, see info below (Step 14). *SWEN lab machines:* PostgreSQL is already installed on the SE lab machines.

6. Using the PostgreSQL admin console (pgAdmin), create a user called `swen610` with a password of your choosing. Make sure you remember that password, because we’re about to put it in a file in a moment. **Note:** be sure to check the box for “User Can Login” on the Privileges tab. *SWEN lab machines:* this has been done for you. The password is `salutecaptionearthyfight`.

7. Still in pgAdmin, create a database also called `swen610` and make the owner of it the user `swen610`.

8. Now let’s create our project structure in our local repository. Create the following files and folders according to the structure below. We’re going to explain every file, but let’s start by turning some of our directories into [Python packages](https://docs.python.org/3.12/tutorial/modules.html#packages) by creating a bunch of directories and empty `__init__.py` files. Please use these exact folder names. Your file structure should be this:

    ```
    db-abc123/       // your username instead of abc123
    ├── __init__.py
    ├── config/      // don't need an __init.py__ here
    ├── src/
    │   ├── __init__.py
    └── tests/
        ├── __init__.py
    ```

9. We need to tell Git to not version control a bunch of files. Create `.gitignore` (note the dot at the beginning) file at the root of the repository with the following contents:

    ```
    *.pyc
    **/db.yml
    ```

10. Create a file `config/db.yml` that has the following content. Change the `replaceme` password to the password you made a moment ago in the pgAdmin console. This is our configuration file that we’ll use to connect to our PostgreSQL database. Notice how our `.gitignore` file tells Git *not* to commit this to the repository - which is good because passwords in repositories is a [bad, bad thing](https://cwe.mitre.org/data/definitions/798.html).

    ```yaml
    host: localhost
    database: swen610
    user: swen610
    password: replaceme
    port: 5432
    ```

11. Create a file `config/gitlab-credentials.yml` with the following content. This is one of our configuration files for our CI server to run on GitLab. When we run our tests on the server, we’ll copy this over to replace `db.yml`.

    ```yaml
    # This file doesn't need to be changed. It's the same for everyone

    host: postgres
    database: swen610
    user: swen610
    password: whowatchesthewatchmen
    port: 5432
    ```

12. Next, we need to tell GitLab how to run our build on a server. For this, we’re using a technology called Docker. We’ll discuss this more later in class, but for now just copy and paste this config. Create a file called `.gitlab-ci.yml` (note the dot at the beginning) at the top of your repository:

    ```yaml
    image:
    name: kalrabb/docker-344-v2025:latest

    services:
    - postgres:17

    variables:
    POSTGRES_DB: swen610
    POSTGRES_USER: swen610
    POSTGRES_PASSWORD: whowatchesthewatchmen
    PYTHON_RUN: python3

    before_script:
    - pip install -r requirements.txt
    - cp config/gitlab-credentials.yml config/db.yml

    testrunner:
    script:
        - echo "hello class"
        - $PYTHON_RUN --version
        - $PYTHON_RUN -m unittest -v
    stage: test
    ```

13. Now we need to tell Python what packages we need. At the root of the repository make a file called `requirements.txt` with this content. Note: if you ever need to add new Python packages for your project, feel free to add them here and they will get installed upon every run of the CI.

    ```
    psycopg2==2.9.10
    pyyaml==6.0.2
    ```

14. Let’s tell Python we need to install those two packages. Run `pip install -r requirements.txt`. The `psycopg2` package is what we use to connect to PostgreSQL, and `pyyaml` is the package we use to parse our YML config files. *SWEN lab machines:* you’ll need to add `--user` to that command since you don’t have root access to those machines.

    *MAC users:*

    * We suggest using the EDB installer for PostgreSQL (not `homebrew`)
    * We *do* suggest using `homebrew` to install Python3.12
        * NOTE: Most MACs have Python 2.7 pre-installed, you need to install 3.12 in addition to that
        * Run your Python commands (after install) as `python3` and `pip3` (since you can’t change the MAC default of 2.7)
    * You may need to install `psycopg2-binary` depending on how you installed PostgreSQL.
    * If you do have to install `psycopg2-binary` then also install the other dependencies in `requirements.txt` manually (command line). You still need `requirements.txt` for the CI, but don`t need to run it locally, once the requirements have been installed.
    * MACs that have python2.7 pre-installed, will run python2.7 when you type `python`. You should type `python3` to make sure the right version is run for this class, and pip3 to install the packages. With some of the newest MACs the configuration is different and the python version is different. You may need to customize some of the installation to get everything to work.

15. The power of unit tests is that they can tell us what’s wrong if something breaks. One thing that can change is your PostgreSQL setup. So let’s make a test for that. If this test ever fails, we know we have a problem with our environment (as opposed to some new code we wrote). This test will connect to PostgreSQL locally and just check the version string. Make a file called `tests/test_postgresql.py` and put this in it:

    ```python
    import unittest
    from src.swen610_db_utils import connect

    class TestPostgreSQL(unittest.TestCase):

        def test_can_connect(self):
            conn = connect()
            cur = conn.cursor()
            cur.execute('SELECT VERSION()')
            self.assertTrue(cur.fetchone()[0].startswith('PostgreSQL'))
            conn.close()

    if __name__ == '__main__':
        unittest.main()
    ```

16. Now let’s run this test. From the root of the repository, run `python -m unittest`. Everything should pass, right? Nope. My output looked like this:

    ```
    >python -m unittest
    E
    ======================================================================
    ERROR: tests.test_postgresql (unittest.loader._FailedTest)
    ----------------------------------------------------------------------
    ImportError: Failed to import test module: tests.test_postgresql
    Traceback (most recent call last):
    File "C:\Users\andy\AppData\Local\Programs\Python\Python37-32\lib\unittest\loader.py", line 436, in _find_test_path
        module = self._get_module_from_name(name)
    File "C:\Users\andy\AppData\Local\Programs\Python\Python37-32\lib\unittest\loader.py", line 377, in _get_module_from_name
        __import__(name)
    File "C:\code\meneely-db-survey\tests\test_postgresql.py", line 2, in <module>
        from src.swen610_db_utils import connect
    ModuleNotFoundError: No module named 'src.swen610_db_utils'


    ----------------------------------------------------------------------
    Ran 1 test in 0.000s
    ```

17. The key part here is `No module named 'src.swen610_db_utils'` - the test is referring to code that doesn’t exist (yet). Here it is. We’ve put together a small utility file that gets you connected to PostgreSQL from your config file easily. Create a file `src/swen610_db_utils.py` with the following content:

    ```python
    import psycopg2
    import yaml
    import os

    def connect():
        config = {}
        yml_path = os.path.join(os.path.dirname(__file__), '../config/db.yml')
        with open(yml_path, 'r') as file:
            config = yaml.load(file, Loader=yaml.FullLoader)
        return psycopg2.connect(dbname=config['database'],
                                user=config['user'],
                                password=config['password'],
                                host=config['host'],
                                port=config['port'])

    def exec_sql_file(path):
        full_path = os.path.join(os.path.dirname(__file__), f'../../{path}')
        conn = connect()
        cur = conn.cursor()
        with open(full_path, 'r') as file:
            cur.execute(file.read())
        conn.commit()
        conn.close()

    def exec_get_one(sql, args={}):
        conn = connect()
        cur = conn.cursor()
        cur.execute(sql, args)
        one = cur.fetchone()
        conn.close()
        return one

    def exec_get_all(sql, args={}):
        conn = connect()
        cur = conn.cursor()
        cur.execute(sql, args)
        # https://www.psycopg.org/docs/cursor.html#cursor.fetchall

        list_of_tuples = cur.fetchall()
        conn.close()
        return list_of_tuples

    def exec_commit(sql, args={}):
        conn = connect()
        cur = conn.cursor()
        result = cur.execute(sql, args)
        conn.commit()
        conn.close()
        return result
    ```

    Let’s do a quick review of what this code above does:

    * `connect` will connect you to Postgres via our config. Closing this connection is up to you.
    * `exec_sql_file` will open up an SQL file and blindly execute everything in it. Useful for test data and your schema. Having your code in a SQL file also gives syntax highlighting!
    * `exec_get_one` will run a query and assume that you only want the top result and return that. It does not commit any changes, so don’t use it for updates.
    * `exec_get_all` will run a query and return all results, usually as a list of tuples. It does not commit any changes, so don’t use it for updates.
    * `exec_commit` will run SQL and then do a commit operation, so use this for updating code.

    Now that you have all these handy utilities, how would you change `test_can_connect`?

    Replace the code in that function, with this (check for any import updates needed):

    ```python
    result = exec_get_one('SELECT VERSION()')
    self.assertTrue(result[0].startswith('PostgreSQL'))
    ```

18. Now run your tests again. Your output will (hopefully) look something like this:

    ```
    > python -m unittest
    .
    ----------------------------------------------------------------------
    Ran 1 test in 0.037s

    OK
    ```

    By removing the duplicate code, and using the utility `exec_get_one` we follow the **DRY** principle.
    
    Now that you have a running unittest, since we will keep using this python module for our work, you should review/ understand how to utilize `unittest`. Take the time to read the introductory information at https://docs.python.org/3/library/unittest.html#organizing-test-code

19. To clarify, your file structure will be this structure (your username instead of abc123)

    ```
    db-abc123/
    ├── __init__.py
    ├── .gitlab-ci.yml
    ├── .gitignore
    ├── config/
    │   ├── db.yml                  // git should NOT be committing this file
    │   └── gitlab-credentials.yml
    ├── requirements.txt
    ├── src/
    │   ├── __init__.py
    │   └── swen610_db_utils.py
    └── tests/
        ├── __init__.py
        ├── test_postgresql.py
    ```

20. At this stage, now is a great time to `git add`, `git commit`, and `git push` our work to GitLab. Take a few moments to explore GitLab’s functionality and find the console output to your code. Push to your master branch and check that this builds on your CI. 

    **Note:** Just because your build is green doesn’t mean you’re done. Always check your console output to make sure the run went the way you expected! The beginning of the CI console output will look something like this:

    ```
    Running with gitlab-runner 15.2.1 (32fc1585)
    on gitlab-ci03 drtP63uZ
    Preparing the "docker" executor
    00:14
    Using Docker executor with image kalrabb/swen610-pg-py-base:latest ...
    Starting service postgres:14 ...
    Pulling docker image postgres:14 ...
    ```

    And then at the end it will look like this:

    ```
    $ pip install -r requirements.txt
    Collecting psycopg2==2.9.10
    Downloading psycopg2-2.9.10.tar.gz (380 kB)
    Collecting pyyaml==6.0.2
    Downloading PyYAML-6.0.2.tar.gz (274 kB)
    Building wheels for collected packages: psycopg2, pyyaml
    Building wheel for psycopg2 (setup.py): started
    Building wheel for psycopg2 (setup.py): finished with status 'done'
    Created wheel for psycopg2: filename=psycopg2-2.9.10-cp39-cp39-linux_x86_64.whl size=485633 sha256=f2d3700383b857bc39e67ff4aa37ad8b75c016193001a1304077001ecb38bcb0
    Stored in directory: /root/.cache/pip/wheels/b3/a1/6e/5a0e26314b15eb96a36263b80529ce0d64382540ac7b9544a9
    Building wheel for pyyaml (setup.py): started
    Building wheel for pyyaml (setup.py): finished with status 'done'
    Created wheel for pyyaml: filename=PyYAML-6.0.2-cp39-cp39-linux_x86_64.whl size=44073 sha256=255c91142981506ac9f3445e88b2e39ce3aa77546b5d3e8284f0ed7be4bf8f09
    Stored in directory: /root/.cache/pip/wheels/68/be/8f/b6c454cd264e0b349b47f8ee00755511f277618af9e5dae20d
    Successfully built psycopg2 pyyaml
    Installing collected packages: pyyaml, psycopg2
    Successfully installed psycopg2-2.9.10 pyyaml-6.0.2
    $ cp config/gitlab-credentials.yml config/db.yml
    $ echo "hello class"
    hello class
    $ $PYTHON_RUN --version
    Python 3.12.2
    $ $PYTHON_RUN -m unittest
    .
    ----------------------------------------------------------------------
    Ran 1 test in 0.015s
    OK
    Job succeeded
    ```

21. Recognize some of those commands? This build essentially automated your development cycle but in an isolated, minimal environment. Also, does this feel like it’s taking too long? Often, various technologies will have generators or project templates to start from. We’re taking a slow stroll through this structure to explain everything.

22. Now that our skeleton is set up, we need to starting building our actual project. Let’s start with how we will be building our database schema. We need methods in our main code to do that. Create a file called `src/chat.py`, `src/rideshare.py` - or whatever name relates to your project and makes sense to you. Next, create a unit test for that file in `tests` called `test_xxxx.py` (or where xxx is your project).

23. Add this unit test to your new test file - adapting to your project, of course:

    ```python
    import unittest
    from src.chat import *
    from src.swen610_db_utils import connect

    class TestChat(unittest.TestCase):

        def test_build_tables(self):
            """Build the tables"""
            buildTables()
            result = exec_get_all('SELECT * FROM example_table')
            self.assertEqual([], result, "no rows in example_table")
    ```

24. Run your tests, you’ll get an error about not knowing what buildTables is. So let’s add that to our main code (in this case `<your_project>.py` )

    ```python
    from src.swen610_db_utils import *

    def buildTables():
    """Create the tables to initialize the db"""
    ```

25. *Why not add buildTables to the utils?* Utility classes can often become the junk drawer of software designs. Don’t succumb to the temptation to not organize things and put everything in to a `util`. Resist that laziness. These utils are general-purpose for SWEN 610, not your project. So this code belongs in your main code.

26. Run your tests now, and you’ll get an error about `psycopg2.errors.UndefinedTable: relation "example_table" does not exist`. Because we haven’t made it yet!

27. Now let’s get this to pass. Finish implementing buildTables() …

    ```python
    def buildTables():
    """Create the tables to initialize the db"""
    conn = connect()
    cur = conn.cursor()
        sql = """
            CREATE TABLE example_table(
                example_col VARCHAR(40)
            )
    """
    exec_commit(sql)
    ```

28. If you run your tests again, we now pass. We successfully created a table! BUT! Run your tests again. You’ll get a `DuplicateTable: relation "example_table" already exists`. Oops. We just violated a fundamental rule of unit tests: **idempotent**. If something is idempotent then we can run it multiple times in a row and get the same effect. Unit tests should always be idempotent and not be impacted by side effects. So let’s fix this. Rename everything to `rebuildTables` instead of `buildTables`. Rename the test case, fix the comments, change the method name, etc.

29. Next, let’s have our code drop the table first. Now, this assumes that the table is already there! So that will break our first run of the unit tests - which means it will work on your machine and not on a new colleague’s machine - the ol’ works-for-me-but-not-for-you problem. To fix that, use `DROP TABLE IF EXISTS`. Something like this:

    ```python
    def rebuildTables():
        conn = connect()
        cur = conn.cursor()
        drop_sql = """
            DROP TABLE IF EXISTS example_table
        """
        create_sql = """
            CREATE TABLE example_table(
                example_col VARCHAR(40)
            )
        """
        cur.execute(drop_sql)
        cur.execute(create_sql)
        conn.commit()
        conn.close()
    ```

30. Tests pass now! Note that there are many ways to refactor this above code. We could use psycopg2’s `executemany` method, or add everything to a loop and execute them. We could make a utility method for executing SQL and returning the cursor. We could add a utility method for checking if a table exists. There are many ways we can improve upon this. In this class, we’ll *constantly* be talking about how to improve our code for readability, maintainability, extensibility, error-proneness, performance, security, and many other concerns we’ll have.

31. Remember our idempotency problem, though? What if we make that mistake later? Surely we’ll add new tables to this project and we’ll probably also forget to add the DROP or something at some point. We’re human (probably). Let’s make sure that mistake *never* happens again. Now that we’ve learned about it, let’s immortalize our fear of making that mistake again by making another unit test. This is the true power of unit testing: whenver you realize that something *could* go wrong, you can usually automate that check in an isolated way and know that the system lacks those problems. Here’s my idempotency test:

    ```python
    def test_rebuild_tables_is_idempotent(self):
        """Drop and rebuild the tables twice"""
        rebuildTables()
        rebuildTables()
        result = exec_get_all('SELECT * FROM example_table')
        self.assertEqual([], result, "no rows in example_table")
    ```

32. To reiterate, you are welcome to improve this code. Just because the instructor gave it to you doesn’t mean it’s perfect. Mess with the config files and see what happens. Add your own utilities or improve ours. This is all here to just get you started. In this setup we worked to make this follow modern engineering conventions as best we could. The only two things that we ask that you keep are Python and PostgreSQL.

    ```
    db-abc123/                         // your username instead of abc123
    ├── __init__.py
    ├── .gitlab-ci.yml
    ├── .gitignore
    ├── config
    │   ├── db.yml
    │   └── gitlab-credentials.yml
    ├── requirements.txt
    ├── src
    │   ├── __init__.py
    │   ├── chat.py                  // or whatever your project is called
    │   └── swen610_db_utils.py
    └── tests
        ├── __init__.py
        ├── test_chat.py             // or whatever your project is called
        ├── test_postgresql.py
    ```

33. Ok, this gives you plenty to start with. Push your code to GitLab and make sure the build still runs (assuming your tests pass locally).

34. You’ll need to be researching how to use psycopg2 (we won’t be using most of its fancy features). Most of the hard work will be in writing SQL. The Python is there to make things realistic. Take a few moments to read the psycopg2 documentation, starting with the `connection` class and the `cursor` class. Look at the code and see where you can replace some of the cursor/ connection code with API calls to swen610_db_utils methods!

35. Finally, we need to tell the instructors what to grade. Create a tag on the commit that marks your completion of this setup. You can do this on the command line, but it’s probably easiest to do this in GitLab itself by going to *Repository > Tags*. Please call this tag `db0`.

{{% /steps %}}