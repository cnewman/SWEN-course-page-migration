Once again, create a new React app (from the root of your directory). e.g. `npx create-react-app abc123-react-client4` (`abc123` is YOUR id). You should copy over any files you need from client3 into client4

You will need to add a FastAPI server to your project to host the RESTful API and the DB. This will be similar to your prior REST work. Create a folder named `server` in your project. This should be at the same level as your `abc123-react-client4` folder. Inside the `server` folder, create an `api` folder along with any `__init__.py` files. Add a db.yml file for your setup (in the api directory), and a `requirements.txt` with the same packages you used in REST (`fastapi`, `uvicorn`, `psycopg2`, `PyYAML`).

You can download example files for setting up full-stack server code [here](https://git.rc.rit.edu/swen-344-templates/full-stack-skeleton). Adapt for your code as necessary. There are no files in `src` in the example, but you can add any helper items there. There is a `.gitlab-ci.yml` file provided (to allow running this standalone), but for your project, you should use the version from `client-3` and update as needed.

You can copy the files in the `server` folder into your project, and modify to fit your assignment. A starter .sql file is provided for testing purposes. You can delete this file and use your own schema for the project. You will need to add your own `db.yml` to the `server/api` folder.

Your directory stucture should look similar to this:

```
├── abc123-react-client4   
│   ├── public  
│   └── src  
├── public  
├── server  
│   ├── api  
│   ├──── __init__.py  
│   ├──── db.yml  
│   ├──── example_api.py  
│   ├──── people.sql  
│   └──── swen_610_db_utils.py  
├── server.py  
│── __init__.py
```