---
layout: post
title: FastAPI with Redis Caching
---

I have been working on a FastAPI project lately and I needed to cache fastapi response and function results.
This post describes how to use fastapi with redis caching.

Let's first create a Python virtual environment called `venv`, activate it and install the requirements.

```bash
python3 -m venv venv
source ./venv/bin/activate
pip install fastapi==0.89.1 redis==4.4.2 fastapi-cache2==0.2.0 "uvicorn[standard]"
```

A minimal FastAPI application. Create a `main.py` alongside your `venv` and save the below content.

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def index():
    return {"message": "Hello, world!"}
```

Then run the app with uvicorn in development mode.

```bash
uvicorn main:app --reload
```

Or you can create a simple bash script `run.sh` to run it, it will save you time when developing.

```bash
#!/bin/bash
uvicorn main:app --reload
```

The `chmod +x` command will make the file `run.sh` an executable so you can run it like `./run.sh`.

```bash
chmod +x ./run.sh
./run.sh
```

With uvicorn server running, visit [http://127.0.0.1:8000](http://127.0.0.1:8000) and if all goes well, you will be greeted with
json response with a "Hello, world!" message on it.

![FastAPI Hello World Json Response](/assets/images/fastapi/fastapi-hello-world-json.png)

And the interactive API documentation provided by swagger ui. Visit [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)

![FastAPI API docs via Swagger UI](/assets/images/fastapi/fastapi-openapi.png)

Below is how to connect FastAPI with Redis in an asynchronous way. See [Asyncio Redis](https://redis.readthedocs.io/en/stable/examples/asyncio_examples.html) for more info.
If you notice that explicitly getting and setting values and encoding/decoding it can become tedious so let's find a python library to simplify this for us.

```python
import json
from fastapi import FastAPI
from redis import asyncio as aioredis

app = FastAPI()

redis = aioredis.from_url("redis://localhost", encoding="utf8", decode_responses=True)

@app.get("/")
async def index():
    key: str = 'my-key'
    value = await redis.get(key)

    if value is None:
        value = {"message": "Hello, world!"}
        await redis.set(key, json.dumps(value), 5*60)

    return json.loads(value)
```

Let's use [fastapi-cache](https://github.com/long2ice/fastapi-cache) to automatically cache function response and can be setup to use redis backend too.

```python
from fastapi import FastAPI
from fastapi_cache import FastAPICache
from fastapi_cache.backends.redis import RedisBackend
from fastapi_cache.decorator import cache
from redis import asyncio as aioredis

app = FastAPI()

@app.on_event("startup")
async def startup():
    redis = aioredis.from_url("redis://localhost", encoding="utf8", decode_responses=True)
    FastAPICache.init(RedisBackend(redis), prefix="api:cache")

@app.get("/")
@cache(expire=60*5)
async def index():
    return {"message": "Hello, world!"}
```

Now we can just use the `cache` decorator imported from `fastapi-cache` to cache function results!
The `cache` decorator can receive optional parameters, here I set `expire` to 5 * 60 which will expire
the cache content after 300 seconds or 5 minutes. Check for more [cache decorator optional parameters](https://github.com/long2ice/fastapi-cache#use-cache-decorator).

To see if it really cached the result, let's check via `redis-cli`.

![Redis cached result](/assets/images/fastapi/fastapi-redis-json-cache.png)

Yes, indeed! So it can cache json responses, let try to cache html response!

```python
from fastapi import FastAPI
from fastapi_cache import FastAPICache
from fastapi_cache.backends.redis import RedisBackend
from fastapi_cache.decorator import cache
from redis import asyncio as aioredis
from fastapi.responses import HTMLResponse

app = FastAPI()

@app.on_event("startup")
async def startup():
    redis = aioredis.from_url("redis://localhost", encoding="utf8", decode_responses=True)
    FastAPICache.init(RedisBackend(redis), prefix="api:cache")

@app.get("/")
@cache(expire=60*5)
async def index():
    return {"message": "Hello, world!"}

@app.get("/about", response_class=HTMLResponse)
@cache(expire=60*60)
async def about():
    return '<p style="color: blue;">This is the about page.</p>'
```

Running the above will result like below.

![FastAPI HTML Response](/assets/images/fastapi/fastapi-html-response.png)

And the cached html response in redis via `redis-cli`.

![Redis Cached HTML Response](/assets/images/fastapi/fastapi-cached-html.png)

Hmm I cached both the `index` and `about` routes but looking at the cache keys in redis, I cant determine
which keys it belongs to. I have a requiment where I will get all the cache keys and display it to an html list.
The user then can select a particular redis key to delete so that a fresh/new content can be generated from the route or view.

Thankfully `fastapi-cache` allows us to use a [custom redis key builder](https://github.com/long2ice/fastapi-cache#custom-key-builder)!

```python
from urllib.parse import urlparse
from typing import Optional
from starlette.requests import Request
from starlette.responses import Response
from fastapi import FastAPI
from fastapi_cache import FastAPICache
from fastapi_cache.backends.redis import RedisBackend
from fastapi_cache.decorator import cache
from redis import asyncio as aioredis
from fastapi.responses import HTMLResponse

app = FastAPI()

def cache_key_builder(
    func,
    namespace: Optional[str] = "",
    request: Request = None,
    response: Response = None,
    *args,
    **kwargs
):
    prefix = FastAPICache.get_prefix()
    if request:
        parsed = urlparse(str(request.url))
        cache_key = f"{prefix}:{namespace}:{func.__module__}:{func.__name__}:{parsed.path}"
    else:
        cache_key = f"{prefix}:{namespace}:{func.__module__}:{func.__name__}:{args}:{kwargs}"
    return cache_key

@app.on_event("startup")
async def startup():
    redis = aioredis.from_url("redis://localhost", encoding="utf8", decode_responses=True)
    FastAPICache.init(RedisBackend(redis), prefix="api:cache")

@app.get("/")
@cache(expire=60*5)
async def index():
    return {"message": "Hello, world!"}

@app.get("/about", response_class=HTMLResponse)
@cache(expire=60*60, key_builder=cache_key_builder)
async def about():
    return '<p style="color: blue;">This is the about page.</p>'
```

We are now using an optional parameter `key_builder` with the value of our custom `cache_key_builder` in the `cache` decorator.
I applied the cache decorator on the `about` page and it should now have a custom redis key on it.
Let's check it via `redis-cli`.

![Redis Custom Cache Keys](/assets/images/fastapi/fastapi-custom-redis-keys.png)

And that's a wrap! FastAPI + Redis = 🚀
