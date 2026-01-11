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

3. You will need [Python 3.9](https://www.python.org/downloads/) installed on your system. Be sure to add `python` to your PATH in installation. *SWEN lab machines:* If you are in the lab, this is pre-installed and available on the command line.

4. Install [PostgreSQL 14](https://www.postgresql.org/download/). *SWEN lab machines:* PostgreSQL is already installed on the SE lab machines.

5. Using the PostgreSQL admin console (pgAdmin), create a user called `swen610` with a password of your choosing. This password will be stored in a file, so you don’t need to memorize it - you can mash the keyboard. Just keep that string because we’re about to put it in a file in a moment. **Note:** be sure to check the box for “User Can Login” on the Privileges tab. *SWEN lab machines:* this has been done for you. The password is `salutecaptionearthyfight`

6. Still in pgAdmin, create a database also called `swen610` and make the owner of it the user `swen610`.

7. Now let’s create our project structure in our local repository. We’re going to explain every file, but let’s start by turning some of our directories into [Python packages](https://docs.python.org/3.7/tutorial/modules.html#packages) by creating a bunch of directories and empty `__init__.py` files. Please use these exact folder names. Your file structure should be this:

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
    name: kalrabb/docker-344-v2025:latest:latest

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

    You should just run this once on your local device, to install the dependencies. Use `pip install -r`

    **NOTE:** MacOS users should follow the [guidance from the DB project](/docs/project/db-project) re: psycopg2-binary. i.e. if you installed psycopg2-binary, don’t attempt to re-install it from requirements.txt.

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
    from flask import Flask
    from flask_restful import Resource, Api
    from api.hello_world import HelloWorld
    from api.management import *

    app = Flask(__name__)
    api = Api(app)

    api.add_resource(Init, '/manage/init') #Management API for initializing the DB

    api.add_resource(Version, '/manage/version') #Management API for checking DB version

    api.add_resource(HelloWorld, '/') 


    if __name__ == '__main__':
        rebuild_tables()
        app.run(debug=True)
    ```

    Notice that we rebuild the tables when the server starts up. You are welcome to update this to your liking. Maybe add some command-line arguments. Or dynamically loading all resource classes from that module. Totally up to you - this is just starter.

18. You’ll notice that we reference an `HelloWorld` class. That should be in `src/api/hello_world.py` with this content:

    ```python
    from flask_restful import Resource
    from db import example

    class HelloWorld(Resource):
        def get(self):
            return dict(example.list_examples())
    ```

    Also add the `Management` class, which will initialize/ setup the DB. This should be in `src\api\management.py` with this content:

    ```python
    from flask_restful import Resource, reqparse, request  #NOTE: Import from flask_restful, not python

    from db.swen610_db_utils import *

    from db.example import rebuild_tables

    class Init(Resource):
        def post(self):
            rebuild_tables()

    class Version(Resource):
        def get(self):
            return (exec_get_one('SELECT VERSION()'))
    ```

19. Let’s run our server now. Run python `src/server.py`. The console will look something like this:

    ```
    >python src\server.py
    * Serving Flask app "server" (lazy loading)
    * Environment: production
    WARNING: This is a development server. Do not use it in a production deployment.
    Use a production WSGI server instead.
    * Debug mode: on
    * Restarting with windowsapi reloader
    * Debugger is active!
    * Debugger PIN: 197-473-693
    * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
    ```

20. Open up a browser and go to http://127.0.0.1:5000. Do you see the `hello, world` in JSON?

    * Also try the command line tool `curl` (usually installed on Windows PCs) to test this simple endpoint e.g.
        * `curl http://localhost:5000`
        * You should see the same output

21. Now let’s add some client side test code. As in the DB project, we should test that Postgres is working. Make a file called `tests/db/test_postgresql.py` and put this in it:

    ```python
    import unittest
    from tests.test_utils import *

    class TestPostgreSQL(unittest.TestCase):

        def test_can_connect(self):
            version = get_rest_call(self, 'http://localhost:5000/manage/version')
            self.assertTrue(version[0].startswith('PostgreSQL'))
    ```

22. Now let’s set up our schema and test data. Create this test called `tests/db/test_db_schema.py`

    ```python
    import unittest
    from tests.test_utils import *

    class TestDBSchema(unittest.TestCase):

        def test_rebuild_tables(self):
            """Rebuild the tables"""
            post_rest_call(self, 'http://localhost:5000/manage/init')
            count = get_rest_call(self, 'http://localhost:5000')
            self.assertEqual(len(count), 1)

        def test_rebuild_tables_is_idempotent(self):
            """Drop and rebuild the tables twice"""
            post_rest_call(self, 'http://localhost:5000/manage/init')
            post_rest_call(self, 'http://localhost:5000/manage/init')
            count = get_rest_call(self, 'http://localhost:5000')
            self.assertEqual(len(count), 1)
    ```

23. You’ll also need this test utility, called `tests/test_utils.py`

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

24. We are now going to the the unit tests (so you will need a 2nd terminal).

    **Make sure your server is still running.**

    The tests should pass, hopefully.
    
    * To see details of the unittests, use the -v switch i.e. python -m unittest -v
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

25. We’re not quite done. We need some automated tests for our API! This is crucial. We’re going to be using a Python library that simulates a browser called `Requests`. The Requests library is essentially a wrapper for opening up a network socket and sending HTTP data into it, and not much more. Make a test called `tests/api/test_example.py` with this content:

    ```python
    import unittest
    from tests.test_utils import *


    class TestExample(unittest.TestCase):

        def setUp(self):  
            """Initialize DB using API call"""
            post_rest_call(self, 'http://localhost:5000/manage/init')
            print("DB Should be reset now")

        def test_hello_world(self):
            expected = { '1' : 'hello, world!' }
            actual = get_rest_call(self, 'http://localhost:5000')
            self.assertEqual(expected, actual)
    ```

    You may notice that we are calling a utility method called `get_rest_call()` and another called `post_rest_call()`. We made this method ourselves to reduce the repeated code and test that the call was successful (you will find this function in `tests/test_utils.py`). Feel free to make this your own.
    
    Also, you will notice we are using a RESTful API in the `setUp()` function to perform the DB init within unittest setup that we used to call directly. It’s all Client-Server now, so we use the `management` endpoint to set up the DB.

26. Let’s run our tests. Hopefully they pass now. (Your output should be something like this)

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

27. Once it’s working - commit and push to GitLab. Make sure the CI works there as well.

28. **Important step.** Break the code in a couple of ways and note the error messages. Specifically, do the following:

    * Add an Python syntax error in `src/api/hello_world.py` while the server is running. Note that the server dies as soon as you hit Save in your editor. Going to your browser will tell you the server can’t be found.
    * Add a runtime exception to `src/api/hello_world.py`, say add `foo.hello` that will cause a `NameError: foo not found`. Go to http://localhost:5000 in your browser. You’ll get a nifty debugging tool that shows you the stacktrace. Go to a line in the stacktrace and open up the console and run some Python within that stack frame. You’ll need to enter a PIN - that was printed to stdout when you started the server.
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

A (recent) update on OSX uses the default Flask port (5000) for other apps (Airplay?). This prevents Flask from listening on the same port. You can either disable Airplay (probably not the best idea), or change the Flask server port.

* To change the Flask port, modify the `app.run(debug=True)` line in `server.py` to `app.run(debug=True, port=4999)`, and make the corresponding change when calling the REST Api from the client side e.g.
* `actual = get_rest_call(self, 'http://localhost:5000')` becomes `actual = get_rest_call(self, 'http://localhost:4999')`. Obviously, the port doesn’t have to be `4999` - it just needs to be some unused port.
* On some MACs `localhost` doesn’t correctly get mapped to the loopback port (don’t ask me why …). You may need to use `127.0.0.1` instead of localhost (or manually modify your `hosts` file)