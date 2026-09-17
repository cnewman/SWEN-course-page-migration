---
title: 'RESTful Setup'

weight: 100
bookToC: true
bookSearchExclude: false

draft: true
---

## RESTful Setup Instructions

We’ll be using a very similar structure to what we did in the DB project. We need to interact with PostgreSQL, but we also need a **server** to run in the background listening to requests and doing any logic we need.

These first few instructions will be exactly the same as the DB project, but they start to get slightly different for this project so follow along carefully. We will assume that you have done the DB project prior to this.

{{% hint warning %}}

**NOTE:** There will be new groups and new partners for the REST project.

{{% /hint %}}

{{% steps %}}

1. Log into https://git.gccis.rit.edu using your RIT username and password. Go to the group were were assigned. In your group, we have created a project for you called `rest-abc123` where `abc123` is your username. Note that you have access to one other students’ repositories. You are going to be pushing code to your own repository and making comments on other students’ repositories. (See our [FAQ on working with other students’ code](/docs/syllabus/expectations#actionable-feedback))

2. Clone your project repository locally using your favorite Git client. (See our [Git page](/git-resources) for some helpful resources.)

3. You will need [Python 3.12](https://www.python.org/downloads/) installed on your system. Be sure to add `python` to your PATH in installation. *SWEN lab machines:* If you are in the lab, this is pre-installed and available on the command line.

4. Install [PostgreSQL 17](https://www.postgresql.org/download/). *SWEN lab machines:* PostgreSQL is already installed on the SE lab machines.

5. Using the PostgreSQL admin console (pgAdmin), create a user called `swen610` with a password of your choosing. This password will be stored in a file, so you don’t need to memorize it - you can mash the keyboard. Just keep that string because we’re about to put it in a file in a moment. **Note:** be sure to check the box for “User Can Login” on the Privileges tab. *SWEN lab machines:* this has been done for you. The password is `salutecaptionearthyfight`

6. Still in pgAdmin, create a database also called `swen610` and make the owner of it the user `swen610`.

7. Now let’s create our project structure in our local repository. We’re going to explain every file, but let’s start by turning some of our directories into [Python packages](https://docs.python.org/3.12/tutorial/modules.html#packages) by creating a bunch of directories and empty `__init__.py` files. Please use these exact folder names. Your file structure should be this:

    ```
    rest-abc123/                         // your username instead of abc123
        __init__.py
        config/                     // don't need an __init__.py here
        src/
            __init__.py
            api/
                __init__.py
            db/
                __init__.py
        tests/
            __init__.py
            api/
                __init__.py
            db/
                __init__.py
    ```

8. We need to tell Git to not version control a bunch of files. Create `.gitignore` (note the dot at the beginning) file at the root of the repository with the following contents:

    ```
    *.pyc
    **/db.yml
    ```

9. Create a file `config/db.yml` that has the following content. Change the `replaceme` password to the password you made a moment ago in the pgAdmin console. This is our configuration file that we’ll use to connect to our PostgreSQL database. Notice how our `.gitignore` file tells Git not to commit this to the repository - which is good because passwords in repositories is a [bad, bad thing](https://cwe.mitre.org/data/definitions/798.html).

    ```yaml
    host: localhost
    database: swen610
    user: swen610
    password: replaceme
    port: 5432
    ```

10. Create a file `config/gitlab-credentials.yml` with the following content. This is one of our configuration files for our CI server to run on GitLab. When we run our tests on the server, we’ll copy this over to replace `db.yml`

    ```yaml
    # This file doesn't need to be changed. It's the same for everyone

    host: postgres
    database: swen610
    user: swen610
    password: whowatchesthewatchmen
    port: 5432
    ```

11. Next is our `.gitlab-ci.yml`. A key difference from the DB project is that in the `before_script` we are running a file called `server.py`. That `&` is key here, too, as it tells the shell to run that job in the background and continue to the next command. The `sleep` command gives the server an extra 3 seconds to boot up.

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
      - $PYTHON_RUN --version
      - $PYTHON_RUN src/server.py & # fire up the server before we run our tests
      - sleep 3

    testrunner:
      script:
        - $PYTHON_RUN -m unittest -v # run the unit tests; -v prints the test being run
      stage: test
    ```

12. Now we need to tell Python what packages we need. At the root of the repository make a file called `requirements.txt` with this content. Note: if you ever need to add new Python packages for your project, feel free to add them here and they will get installed upon every run of the CI.

    ```
    fastapi==0.141.1
    uvicorn==0.53.0
    psycopg2==2.9.10
    PyYAML==6.0.2
    requests==2.32.3
    ```

    You should just run this once on your local device, to install the dependencies. Use `pip install -r requirements.txt`

    A quick tour of what these are for:

    * `fastapi` is our web framework - it turns Python functions into RESTful endpoints.
    * `uvicorn` is the server that actually listens on a port and hands requests to FastAPI.
    * `psycopg2` talks to PostgreSQL, and `PyYAML` parses our config files - both carried over from the DB project.
    * `requests` is used by our **tests** to make HTTP calls to our own server.

    **NOTE:** MacOS users should follow the [guidance from the DB project](/docs/project/db-project) re: psycopg2-binary. i.e. if you installed psycopg2-binary, don’t attempt to re-install it from requirements.txt. See the [Mac users](#mac-users) section below for a way to keep the CI happy while doing this.

13. Let’s create out test data set. Create a file called `src/db/test_data.sql` with this content:

    ```sql
    -- We specify our primary key here to be as repeatable as possible
    INSERT INTO example_table(id, foo) VALUES
    (1, 'hello, world!');

    -- Restart our primary key sequences here so inserting id=DEFAULT won't collide
    ALTER SEQUENCE example_table_id_seq RESTART 1000;
    ```

14. And finally here is our schema file, in `src/db/schema.sql`

    ```sql
    DROP TABLE IF EXISTS example_table;

    CREATE TABLE example_table(
    id SERIAL PRIMARY KEY,
    foo TEXT NOT NULL
    );
    ```

15. We need our database layer that calls Postgres. Make a file called `src/db/example.py` with this content:

    ```python
    import os
    from .swen610_db_utils import *

    def rebuild_tables():
        exec_sql_file('src/db/schema.sql')
        exec_sql_file('src/db/test_data.sql')

    def list_examples():
        """This is an example. Please remove from your code before REST1 deadline.
        DB layer call for listing all rows of our example.
        """
        return exec_get_all('SELECT id, foo FROM example_table')
    ```

16. You will also need our database utility in `src/db/swen610_db_utils.py` (note it's in the db directory now). Here is the contents for that:

    ```python
    import psycopg2
    import yaml
    import os

    def connect():
        config = {}
        yml_path = os.path.join(os.path.dirname(__file__), '../../config/db.yml')
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

17. Now we need to set up our server. Create a file called `src/server.py`. Here are its contents:

    ```python
    from contextlib import asynccontextmanager
    from fastapi import FastAPI
    import uvicorn

    from api.hello_world import router as hello_world_router
    from api.management import router as management_router
    from db.example import rebuild_tables

    @asynccontextmanager
    async def lifespan(app: FastAPI):
        rebuild_tables()  # runs once, right before the server starts accepting requests
        yield

    app = FastAPI(lifespan=lifespan)

    app.include_router(management_router)  # Management API for initializing the DB and checking its version
    app.include_router(hello_world_router)

    if __name__ == '__main__':
        uvicorn.run(app, host='127.0.0.1', port=8000)
    ```

    A few things to notice here:

    * Notice that we rebuild the tables when the server starts up. FastAPI calls that a **lifespan** - the code before the `yield` runs at startup, and anything after it would run at shutdown. You are welcome to update this to your liking. Maybe add some command-line arguments. Or dynamically load all the routers from that module. Totally up to you - this is just starter.
    * `uvicorn` is the actual web server - FastAPI itself only defines the endpoints, and something has to listen on a port. This is the one piece Flask handled for you with `app.run()`.
    * Unlike Flask-RESTful, FastAPI doesn’t use a class per endpoint. We group related endpoints into an `APIRouter` and then `include_router` them onto the app.
    * **There is no auto-reload here.** Whenever you change your server code, stop the server with `CTRL+C` and start it again. Uvicorn does have a `reload=True` option, and you are welcome to try it, but we have seen its file watcher hang on Windows and leave a dead process squatting on port 8000 - which is a far more confusing problem than just restarting the server yourself.

18. You’ll notice that we reference a `hello_world` router. That should be in `src/api/hello_world.py` with this content:

    ```python
    from fastapi import APIRouter
    from db import example

    router = APIRouter()

    @router.get('/')
    def hello_world():
        return dict(example.list_examples())
    ```

    Also add the management endpoints, which will initialize/ setup the DB. This should be in `src\api\management.py` with this content:

    ```python
    from fastapi import APIRouter

    from db.swen610_db_utils import *

    from db.example import rebuild_tables

    router = APIRouter(prefix='/manage')

    @router.post('/init')
    def init():
        rebuild_tables()

    @router.get('/version')
    def version():
        return exec_get_one('SELECT VERSION()')
    ```

    The `prefix='/manage'` on the router is why these end up at `/manage/init` and `/manage/version`. Whatever your function returns gets converted to JSON for you - so the tuple that comes back from `exec_get_one` shows up as a JSON array.

19. Let’s run our server now. Run `python src/server.py`. The console will look something like this:

    ```
    >python src\server.py
    INFO:     Started server process [13579]
    INFO:     Waiting for application startup.
    INFO:     Application startup complete.
    INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
    ```

    Leave this terminal open and keep an eye on it - uvicorn logs every request here, and it’s also where your stacktraces will show up.

20. Open up a browser and go to http://127.0.0.1:8000. Do you see the `hello, world` in JSON?

    * Also try the command line tool `curl` (usually installed on Windows PCs) to test this simple endpoint e.g.
        * `curl http://localhost:8000`
        * You should see the same output

21. Here’s something you get for free with FastAPI: go to http://127.0.0.1:8000/docs. FastAPI generates interactive API documentation from your code, and you can fire off requests right from the browser - including the `POST /manage/init` one, which is otherwise awkward to trigger from the address bar. Poke around in there; it’s the fastest way to sanity-check an endpoint you just wrote. (There’s a second flavor at `/redoc` if you prefer it.)

22. Now let’s add some client side test code. As in the DB project, we should test that Postgres is working. Make a file called `tests/db/test_postgresql.py` and put this in it:

    ```python
    import unittest
    from tests.test_utils import *

    class TestPostgreSQL(unittest.TestCase):

        def test_can_connect(self):
            version = get_rest_call(self, 'http://localhost:8000/manage/version')
            self.assertTrue(version[0].startswith('PostgreSQL'))
    ```

23. Now let’s set up our schema and test data. Create this test called `tests/db/test_db_schema.py`

    ```python
    import unittest
    from tests.test_utils import *

    class TestDBSchema(unittest.TestCase):

        def test_rebuild_tables(self):
            """Rebuild the tables"""
            post_rest_call(self, 'http://localhost:8000/manage/init')
            count = get_rest_call(self, 'http://localhost:8000')
            self.assertEqual(len(count), 1)

        def test_rebuild_tables_is_idempotent(self):
            """Drop and rebuild the tables twice"""
            post_rest_call(self, 'http://localhost:8000/manage/init')
            post_rest_call(self, 'http://localhost:8000/manage/init')
            count = get_rest_call(self, 'http://localhost:8000')
            self.assertEqual(len(count), 1)
    ```

24. You’ll also need this test utility, called `tests/test_utils.py`

    ```python
    import requests
    # The client (unittest) can only contact the server using RESTful API calls


    # For API calls using GET.  params and header are defaulted to 'empty'

    def get_rest_call(test, url, params = {}, get_header = {}, expected_code = 200):
        response = requests.get(url, params, headers = get_header)
        test.assertEqual(expected_code, response.status_code,
                        f'Response code to {url} not {expected_code}')
        return response.json()

    # For API calls using POST.  params and header are defaulted to 'empty'

    def post_rest_call(test, url, params = {}, post_header = {},expected_code = 200):
        '''Implements a REST api using the POST verb'''
        response = requests.post(url, params, headers = post_header)
        test.assertEqual(expected_code, response.status_code,
                        f'Response code to {url} not {expected_code}')
        return response.json()

    # For API calls using PUT.  params and header are defaulted to 'empty'

    def put_rest_call(test, url, params = {}, put_header = {},expected_code = 200):
        '''Implements a REST api using the PUT verb'''
        response = requests.put(url, params, headers = put_header)
        test.assertEqual(expected_code, response.status_code,
                        f'Response code to {url} not {expected_code}')
        return response.json()

    # For API calls using DELETE.  header is defaulted to 'empty'

    def delete_rest_call(test, url, delete_header={}, expected_code = 200):
        '''Implements a REST api using the DELETE verb'''
        response = requests.delete(url, headers = delete_header)
        test.assertEqual(expected_code, response.status_code,
                        f'Response code to {url} not {expected_code}')
        return response.json()
    ```

25. We are now going to run the unit tests (so you will need a 2nd terminal).

    **Make sure your server is still running.**

    The tests should pass, hopefully.

    * To see details of the unittests, use the -v switch i.e. `python -m unittest -v`
    * At this point, your file structure should look like this now:

    ```
    rest-abc123/                         // your username instead of abc123
    |   .gitignore
    |   .gitlab-ci.yml
    |   requirements.txt
    |
    +---config
    |       db.yml
    |       gitlab-credentials.yml
    |
    +---src
    |   |   server.py
    |   |   __init__.py
    |   |
    |   +---api
    |   |   |   hello_world.py
    |   |   |   management.py
    |   |   |   __init__.py
    |   |   |
    |   |
    |   +---db
    |   |   |   example.py
    |   |   |   schema.sql
    |   |   |   swen610_db_utils.py
    |   |   |   test_data.sql
    |   |   |   __init__.py
    |   |   |
    |   |
    |
    +--tests
        |   test_utils.py
        |   __init__.py
        |
        +---api
        |   |   __init__.py
        |   |
        |
        +---db
        |   |   test_db_schema.py
        |   |   test_postgresql.py
        |   |   __init__.py
        |   |
    ```

26. We’re not quite done. We need some automated tests for our API! This is crucial. We’re going to be using a Python library that simulates a browser called `Requests`. The Requests library is essentially a wrapper for opening up a network socket and sending HTTP data into it, and not much more. Make a test called `tests/api/test_example.py` with this content:

    ```python
    import unittest
    from tests.test_utils import *


    class TestExample(unittest.TestCase):

        def setUp(self):  
            """Initialize DB using API call"""
            post_rest_call(self, 'http://localhost:8000/manage/init')
            print("DB Should be reset now")

        def test_hello_world(self):
            expected = { '1' : 'hello, world!' }
            actual = get_rest_call(self, 'http://localhost:8000')
            self.assertEqual(expected, actual)
    ```

    You may notice that we are calling a utility method called `get_rest_call()` and another called `post_rest_call()`. We made this method ourselves to reduce the repeated code and test that the call was successful (you will find this function in `tests/test_utils.py`). Feel free to make this your own.
    
    Also, you will notice we are using a RESTful API in the `setUp()` function to perform the DB init within unittest setup that we used to call directly. It’s all Client-Server now, so we use the `management` endpoint to set up the DB.

    {{% hint info %}}

    **Why not FastAPI’s `TestClient`?** FastAPI ships a `TestClient` that calls your app in-process, without a network. That’s handy, but it would let us quietly skip the part we care about in this course - a real client talking to a real server over HTTP. Stick with `requests` against a running server.

    {{% /hint %}}

27. Let’s run our tests. Hopefully they pass now. (Your output should be something like this)

    ```
    python -m unittest -v
    test_hello_world (tests.api.test_example.TestExample) ... DB Should be reset now
    ok
    test_rebuild_tables (tests.db.test_db_schema.TestDBSchema)
    Rebuild the tables ... ok
    test_rebuild_tables_is_idempotent (tests.db.test_db_schema.TestDBSchema)
    Drop and rebuild the tables twice ... ok
    test_can_connect (tests.db.test_postgresql.TestPostgreSQL) ... ok
    ```

    Your file structure should look like this now:

    ```
    rest-abc123/                         // your username instead of abc123
    |   .gitignore
    |   .gitlab-ci.yml
    |   requirements.txt
    |
    +---config
    |       db.yml
    |       gitlab-credentials.yml
    |
    +---src
    |   |   server.py
    |   |   __init__.py
    |   |
    |   +---api
    |   |   |   hello_world.py
    |   |   |   management.py
    |   |   |   __init__.py
    |   |   |
    |   |
    |   +---db
    |   |   |   example.py
    |   |   |   schema.sql
    |   |   |   swen610_db_utils.py
    |   |   |   test_data.sql
    |   |   |   __init__.py
    |   |   |
    |   |
    |
    +--tests
        |   test_utils.py
        |   __init__.py
        |
        +---api
        |   |   test_example.py
        |   |   __init__.py
        |   |
        |
        +---db
        |   |   test_db_schema.py
        |   |   test_postgresql.py
        |   |   __init__.py
        |   |
    ```

28. Once it’s working - commit and push to GitLab. Make sure the CI works there as well.

29. **Important step.** Break the code in a couple of ways and note the error messages. Specifically, do the following:

    * Add a Python syntax error in `src/api/hello_world.py` - delete the colon off the end of `def hello_world():`. Note that the running server doesn’t care at all, because it already imported that file. Now stop it and start it again. It never gets as far as listening on a port; you just get a traceback ending in something like:

        ```
        File "...\src\api\hello_world.py", line 7
            def hello_world()
                             ^
        SyntaxError: expected ':'
        ```

        Note that the traceback blames `server.py` first and then walks down to the real culprit. Get in the habit of reading to the *bottom* of a traceback.

    * Fix that, then add a runtime exception instead: put `foo.hello` as the first line of `hello_world()`. Restart the server and go to http://localhost:8000 in your browser. This time the server starts up fine and stays up, and your browser gets a bare `Internal Server Error` with a 500 status code - no detail at all. The actual stacktrace, ending in `NameError: name 'foo' is not defined`, is printed in the **terminal where the server is running**. This is the single most important habit to build with FastAPI: when an endpoint misbehaves, the browser will rarely tell you why - go read the server terminal. When you need to poke around inside a failing request, drop a `breakpoint()` on the line above it and re-send the request; that server terminal will turn into a debugging console.
    * Stop the server and then run your tests. You’ll see lots of text fly by and probably an error that looks like `NewConnectionError('<urllib3.connection.HTTPConnection object at 0x03C88510>: Failed to establish a new connection)`.

    This is a really helpful practice. Whenever you “get things working” on a new piece of technology, think about the kinds of mistakes you might make, intentionally do them, and look at how that presents itself in your development environment. That way you are less likely to get thrown off by cryptic error messages later on.

{{% /steps %}}

{{% hint warning %}}

No need to tag your finished product at this time. This will be due along with `rest1`.

{{% /hint %}}

### Specific Issues

#### PyCharm users 

Your IDE will tell you that imports cannot be resolved. To fix this, right-click `src` on the project and click Mark as Sources Root.

#### Mac users

* **Port conflicts.** We’re using port 8000, so the old macOS AirPlay-squats-on-port-5000 problem doesn’t apply here. But if something else on your machine already has 8000, change the `port=8000` argument in `server.py` to some other unused port (say, `port=8001`) and make the corresponding change everywhere you call the REST API from the client side e.g. `actual = get_rest_call(self, 'http://localhost:8000')` becomes `actual = get_rest_call(self, 'http://localhost:8001')`.
* **localhost.** On some Macs `localhost` doesn’t correctly get mapped to the loopback address (don’t ask me why …). You may need to use `127.0.0.1` instead of `localhost` (or manually modify your `hosts` file, if you are comfortable doing that).
* **psycopg2.** For the typical `psycopg2` problems, where you have to (and should have previously) install `psycopg2-binary`: make a copy of `requirements.txt` and name it `requirements-mac.txt`, then remove the `psycopg2` line from that new file. On your local Mac, use `requirements-mac.txt`, but keep the original `requirements.txt` since the CI needs that one!

#### Windows users

A recent update on Windows 11 caused `localhost` to not work correctly. If you start seeing issues (slow API response or no API response), then change `localhost` to `127.0.0.1`.
