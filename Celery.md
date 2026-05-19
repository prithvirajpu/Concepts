# Celery Notes

> Distributed task queue framework for Python, used for running async / scheduled tasks, background jobs, and message‑driven workflows.

## Table of Contents
- [Celery Notes](#celery-notes)
  - [Table of Contents](#table-of-contents)
  - [What is Celery?](#what-is-celery)
  - [Why Celery?](#why-celery)
  - [Key Features](#key-features)
  - [Core Concepts](#core-concepts)
  - [Basic Architecture](#basic-architecture)
  - [Where is Celery Used?](#where-is-celery-used)
  - [Real-World Applications](#real-world-applications)
  - [How Celery Works (Step-by-Step)](#how-celery-works-step-by-step)
    - [Core Flow](#core-flow)
    - [Simple Flowchart (Text)](#simple-flowchart-text)


## What is Celery?

**Celery** is an open‑source, **distributed task queue** written in Python. It lets you:

- Run functions **asynchronously** (in the background)
- Schedule tasks (periodic / cron‑like)
- Split work across multiple workers

Celery is **not the broker**; it uses brokers like Redis, RabbitMQ, or Amazon SQS to move messages between producers and workers.

## Why Celery?

- **Decoupling**: Web requests become fast while heavy work runs in the background.
- **Scalability**: You can spin up many workers across machines.
- **Fault Tolerance**: Tasks can retry, time out, and be tracked.
- **Flexibility**: Works with Django, Flask, FastAPI, and plain Python.
- **Rich Ecosystem**: Flower, Django‑Celery‑Beat, monitoring, backends (Redis, DB, etc.).

Compared to plain threaded/async code, Celery is easier to manage, monitor, and scale across services.

## Key Features

- Asynchronous task execution
- Periodic / cron‑style scheduling (via Celery‑Beat)
- Retries with exponential backoff
- Task timeouts and rate limiting
- Multiple result backends (Redis, database, RPC, etc.)
- Task routing and queues
- Web monitoring (Flower)
- Support for Redis, RabbitMQ, SQS, etc. as message brokers

## Core Concepts

- **Task**: A Python function decorated with `@celery.task` (or using `@shared_task`).
- **Producer**: Your app (e.g., Django view) that sends tasks to the broker.
- **Broker**: Redis / RabbitMQ that holds the task queue.
- **Worker**: `celery worker` process that picks tasks from the broker and runs them.
- **Result Backend**: Optional storage (e.g., Redis) to store task return values/status.
- **Beat**: Scheduler (`celery beat`) that sends periodic tasks to the broker.

## Basic Architecture

- Your app (Django / Flask / FastAPI) imports the Celery app and calls:
  - `task.delay(*args)` or `task.apply_async(...)` to send a task.
- The broker (e.g., Redis) holds the task message.
- One or more Celery workers pull tasks from the broker and execute them.
- The result backend (if configured) stores success/failure, return value, and metadata.

## Where is Celery Used?

- Sending emails asynchronously
- Processing image/video uploads
- API calls to external services
- Data import/export and ETL jobs
- Reporting and analytics background tasks
- Payment processing workflows
- Webhooks and event processing

Many Django apps use Celery + Redis as the default setup for background jobs.

## Real-World Applications

1. **Email/SMS Notifications** – Send async without blocking the HTTP request.
2. **File/Image Processing** – Resize, compress, or analyze uploads.
3. **Data Sync Jobs** – Sync with third‑party APIs overnight or hourly.
4. **Slack/Discord Bots** – Handle heavy workflows in the background.
5. **Reporting & Dashboards** – Precompute aggregates and cache results.
6. **Payment Settlements** – Retry failed payments with exponential backoff.

## How Celery Works (Step-by-Step)

### Core Flow

1. **App calls task** (e.g., `send_email_task.delay(email, subject)`).
2. **Celery serializes** the task name, args, kwargs into a message.
3. **Message is sent** to the broker (e.g., Redis queue).
4. **Worker receives** the message and deserializes it.
5. **Worker executes** the task function and captures the result.
6. **Result is stored** (optional) in the result backend.
7. **Producer can check** task status (pending, success, failed) later.

### Simple Flowchart (Text)

```mermaid
flowchart TD
    A[Web App / Django View] -->|task.delay()| B[(Broker: Redis)]
    B -->|Task Message| C[Celery Worker]
    C --> D[Execute Task Function]
    D --> E{Success?}
    E -->|Yes| F[Store Result (Optional)]
    E -->|No| G[Retry / Error Handling]
    F --> H[Return to Client (Later)]
    G --> H
```

