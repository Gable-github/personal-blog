---
title: "How do I understand a new codebase? A walkthrough."
date: 2025-06-16
authors:
  - Gabryel Soh
---

## Overview

I will walk through the understanding of a codebase without any plan first, then review how I looked through it after I'm done.

## Raw Walkthrough

Right now, I'm trying to integrate my 80% complete independent feature into the main codebase.

The codebase uses Celery (which uses Redis as its message broker running on a separate docker container) to manage lifecycles of asynchronous tasks / processes. The codebase also uses  `FastAPI` and its native "wrapper" (for developer convenience) for websockets, which under the hood is the `starlette` websocket implementation, to expose data for frontend UIs. Client-server communication is hence a mix of both conventional `GET` and `POST` methods, combined with "live-streamed" data.

!!! note "How does the data get "live-streamed"?"
    A websocket connection is established. Conventional websocket boilerplate (not exactly but I don't have a better word for this right now) are added, such as token authentication and websocket connection management.

    The core of the websocket is that (1) **the user can send a `json` body over the active websocket connection, which details an `enum` of actions that a user can take**, and (2) **a continuous polling task, which reads ongoing task details from the redis database,  packages it into a Pydantic model (basically UI DTOs) and sends it to the client via the websocket in constant intervals.**

### User Session Management

There is also an additional feature to link the task data with users. The codebase uses `mixpanel`, which is a product analytics tool, that exposes a really simple python SDK that is used to access their API. We use the SDK and corresponding API to call methods that track the user's actions and session metadata while using the vscode extension. Metadata like the user `session_id` is then used in functions throughout the codebase as parameters.

!!! note "Tight coupling with the vscode api"
    Apparently for now this is generally okay because most of the clients UI with the backend on their code is via vscode anyway.

## Codebase Layout

From a very high level look at the codebase, we only need to care about 2 folders. The `/server` and `/tasks` folder located in `root`.

### `/server` Layout

`/server` contains the following:

- `/routes` - Contains the endpoint methods, as well as the websocket.
- `/actions` - Service layer, methods that make up the core business logic of the application
- `/models` - DTO layer. Contains model definitions that standardize data being passed around the codebase.
- `/utils` - Utilities mainly for config settings, connections, to `celery`, `redis`, `mixpanel`
    - Need to understand the `middlewares.py` better...

#### Key areas to note about the `/server` module

1. `analysis.py`
    
    Contains endpoints for clients to send files and get results. 

    !!! note "Dependency Injection for FastAPI"
        From what I've seen, its most commonly used in parameters for the API endpoint method parameters, as a validation step. E.g. a valid `redis.StrictRedis` object is needed as a parameter to the endpoint function. We will use `redis: StrictRedis = Depends(get_redis_connection)` such that the validation function is called and injects a valid or `null` object right before the function runs in runtime.

2. `repository.py`

    Kind of similar to `analysis.py` but this time its to submit an entire repo.

3. `session.py`

    API security is handled by `fastapi.security`. This handles `GET`, `POST` and `DELETE` at the `/session` endpoint for user sessions.

    `fetch_session_models` is used at the `create` endpoint, which takes in the valid user credentials (session token) and valid redis connection. the "validity" of these 2 arguments are checked at the dependency injection level, before the endpoint method is even called.

      * `SessionData` is returned and it contains the jsonized output of the redis pipeline for that user (will be `None` or something, depending on the token existence checked by `redis.StrictRedis.pipeline.hget()`)

### FastAPI setup (to be updated)

Main file `app.py` is the main application factory that sets up the whole FastAPI server. 

!!! note "Quick recap on what application factory is"
    Application factory is a design pattern where we configure the main instance of the application with our own configuration. Many web frameworks like `Flask` and `FastAPI` uses this because users at the end need configurability for their application instance.

Within the `create_app` function we can also ensure that the settings are initiallized globally throughout the application. This means we dont have to `os` call the env each time.

Overall `app.py` loads configs, loggings, app creation with lifespan logic(?), adds routes and middleware, adds metric collection and redis, celery integrations. Then on app shutdown it gives a pdf.

!!! note "What is lifespan?"
    These type of functions are when you want the function to execute halfway then yield until a future event occurs.

### Intended User Flow

This codebase is originally meant for the user to interact with, whats seems to be the vscode extension. I think theres some vscode script layer than is an SDK for their UI that calls endpoints in our service.

## Understanding Celery

According to the [Celery Documentation](https://docs.celeryq.dev/en/stable/), its a distributed system that processes many messages + provide operations to maintain such a system.

### Whats a task queue?

Task queue is a mechanism to distribute individual units of work across threads or machines.

You need to have a dedicated worker process to constantly monitor task queues for new work to perform.

`Celery` uses a *broker* to mediate between clients and workers. A client adds a message to the queue, then the broker delivers the message to be picked up by the monitoring worker.

`Celery` can have multiple workers and brokers i.e. we can have high availability and horizontal scaling. It can run on a single machine, multiple machines, or even across data centers.

`Celery` needs a message transport to send and receive messages. `RabbitMQ` and `Redis` (and `Amazon SQS`) broker transports that are feature-complete. Which means we can almost plug and play with complete message broker features using either of these 2 libraries, without needing much code on top of whats already provided by them.

### A Quick Example

A client calls my endpoint and that endpoint method calls a method that is "heavy".

`Celery` will add the function to the `Redis` Queue. Under the hood, `Celery` will serialize the message packet before pushing it to the queue. Side note: the serialization of the task name and arguments are in `JSON` for `Celery` under the hood.

Worker method, which is decorated by the `@celery_app.task` decorator and called earlier, is listening in a **separate process**. Result is then stored in some db of your choice. 

The workers match the task name in the serialized `JSON` to a registered `@celery_app.task` function, deserializes the arguments, and executes the python function, in another process.

## Conclusion

I think the way I tackled understanding codebase (this one in particular) is by looking at the routes first, because all code cascades from there. So I made a mental map and grouping of the different endpoints. Previous understanding of some common idiomatic practices like having a utils, DTOs, and service layer helps.

On top of that are mostly reading up more on the documentation of other high level libraries being used to facilitate the application. In this case its Celery and Redis. 

Then after getting that kind of broad overview of the library I think I start to read deeper into how these libraries are actually implemented in the code. E.g. `app.py` for FastAPI is probably a standard format but with our own sprinklings on there for our custom purpose. This is the stage where we might start to see some weird issues that weren't previously resolved. Also at this stage, we might look at some more nitty gritty parts like why did we choose a certain design pattern (factory design). Be prepared to also learn about the code or language methods kind of level stuff because there will be some new kind of python or writing that is just a little bit hard to grasp. Do let yourself spend time reading articles and learning about those. 

!!! note "`partial()`, `yield`, `*args, **kwargs`
    These were just some of the things that I had to break my flow to go and read up more on. Using LLMs to aid can help, but I've found that the time spent reading articles somehow makes more sense to me. Probably a combination of the time spent learning about something and understanding the *purpose* of those stuff helps in a deeper understanding and consequently memory retention.

### Next steps

I will start to try to implement my feature, with the expectation to get stuck diving deeper into something at some point!

---

### TO FURTHER STUDY
- mixpanel middleware