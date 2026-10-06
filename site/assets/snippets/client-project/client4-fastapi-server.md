To make sure your Client can talk to your Server, we need to make some changes to avoid CORS errors.

Your React pages are served from one origin (`http://localhost:3000`) and your API from another (`http://localhost:8000`), so the browser will block the API calls unless the server says they're allowed. FastAPI has CORS support built in, so there's no extra package to install - you just add the `CORSMiddleware` to your app.

You will see some additional code in `server.py` in the provided skeleton code. (Review this for your understanding)

```python
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI(lifespan=lifespan)  # create FastAPI instance

app.add_middleware(  # Enable CORS so the React dev server can call the API
    CORSMiddleware,
    allow_origins=['http://localhost:3000', 'http://127.0.0.1:3000'],
    allow_methods=['*'],
    allow_headers=['*'],
)

app.include_router(example_router)  # api router(s)
```

As before, run your API (FastAPI) server by typing `python server/server.py`. You need to start up the FastAPI server before running your React client. Remember that you can check your endpoints at http://localhost:8000/docs before wiring them into React.
