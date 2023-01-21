---
layout: post
title: "FastAPI Is Awesome"
date: 2023-01-02T21:24:11+08:00
tags: ["FastAPI", "Python"]
---

I am having a really great experience with using the FastAPI web framework at my current project.
When my client told me that they want their new web api project to be build from scrath with a
new python web framework called **FastAPI**, I initially thought that meh .. it's just another web framework.
Boy I was wrong! It's such a joy to use.

#### Concurrent with Async / Await

Coming from Flask and Django, which are synchronous python web frameworks,
FastAPI is different since it is built on top of Starlette, thus making it also an asynchronous framework.
At first I was confused with the Async/Await mental model but thanks to their documentation I now know how to
implement things in async way. See [Concurrency and Async / Await][Concurrency].

#### Automatic API Documentation

Another thing that I like is the automatic interactive api documentation based on OpenAPI (Swagger) and JSON schema.
You get two flavors of automatic documentation, run Uvicorn or whatever you prefer and visit /docs or /redoc.

#### Bigger Application with Multiple Files

Since I'm new to FastAPI, I've made the mistake of putting all my api endpoints into a single file main.py but later on,
discovered that there is a pattern for bigger applications. See [Bigger Appications][Bigger-Applications].
Now api endpoints are grouped into modules and have its own api router.

#### Redis Caching with fastapi-cache

Caching with Redis is an staple in any python web project.
Thankfully [fastapi-cache][fastapi-cache] fits our requirement.
Under the hood it uses redis-py async feature to communicate with Redis in an asynchronous way.

#### Job queues with AsyncIO and Redis

For our background tasks / job queue, I opted out for using Celery since I wanted something lightweight.
I initially used [Python RQ](https://python-rq.org/) but it turns out it's not compatible with async FastAPI.
I looked for an alternative - one that is async compatible and found [arq](https://arq-docs.helpmanual.io/).
arq fits our requirements since we wanted to have access to job info like status, result while it's running,
something that the built-in [Background Task](https://fastapi.tiangolo.com/tutorial/background-tasks/) from FastAPI lacks.
At development and production, arq is deployed and managed by Systemd.

#### Deployment with Gunicorn and Uvicorn Workers

We are deploying our app in a Digital Ocean droplet and I prefer using Gunicorn with Uvicorn workers + Systemd.
This is how I deployed it in development and production servers.

[FastAPI]: https://fastapi.tiangolo.com/
[Concurrency]: https://fastapi.tiangolo.com/async/
[Bigger-Applications]: https://fastapi.tiangolo.com/tutorial/bigger-applications/
[fastapi-cache]: https://github.com/long2ice/fastapi-cache
