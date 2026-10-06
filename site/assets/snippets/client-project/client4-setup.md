Once again, create a new React app (from the root of your directory). e.g. `npx create-react-app abc123-react-client4` (`abc123` is YOUR id). You should copy over any files you need from client3 into client4

You will need to add a FastAPI server to your project to host the RESTful API and the DB. There is no skeleton code for this - you've already built one in REST, so set it up the same way. Create a folder named `server` in your project, at the same level as your `abc123-react-client4` folder, and give it the same structure you used in REST: a `src` folder with `api` and `db` packages (along with any `__init__.py` files), a `config` folder for your `db.yml`, and a `requirements.txt` with the same packages you used in REST (`fastapi`, `uvicorn`, `psycopg2`, `PyYAML`). Bring over the pieces you can reuse, like your DB utilities and management endpoints, and write your own schema and test data for this assignment.

Remember to keep `db.yml` out of version control. For your `.gitlab-ci.yml`, use the version from `client-3` and update as needed.

Your directory structure should look similar to this:

```
├── abc123-react-client4
│   ├── public
│   └── src
├── server
│   ├── config
│   │   └── db.yml
│   ├── requirements.txt
│   └── src
│       ├── __init__.py
│       ├── server.py
│       ├── api
│       │   ├── __init__.py
│       │   └── ...            // your routers
│       └── db
│           ├── __init__.py
│           ├── schema.sql
│           ├── test_data.sql
│           └── swen610_db_utils.py
```
