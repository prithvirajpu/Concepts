# Redis Notes

> High-performance, in-memory data store used as a database, cache, message broker, and more.

## Table of Contents
- [Redis Notes](#redis-notes)
  - [Table of Contents](#table-of-contents)
  - [What is Redis?](#what-is-redis)
  - [Why Redis?](#why-redis)
  - [Key Features](#key-features)
  - [Data Structures](#data-structures)
  - [Where is Redis Used?](#where-is-redis-used)
  - [Real-World Applications](#real-world-applications)
  - [How Redis Works (Step-by-Step)](#how-redis-works-step-by-step)
    - [Core Architecture](#core-architecture)
    - [Simple Flowchart (Text)](#simple-flowchart-text)

## What is Redis?

**Redis** (Remote Dictionary Server) is an open-source, **in-memory key-value data store**. It can function as:
- A database
- Cache
- Message broker
- Streaming engine
- Search engine (with RedisSearch module)

It was created by Salvatore Sanfilippo in 2009 and is now sponsored by Redis Inc.

## Why Redis?

- **Blazing Fast** — Sub-millisecond latency because data lives in RAM
- **Versatile** — Supports many data structures beyond simple key-value
- **Highly Available** — Replication, clustering, sentinel
- **Persistent** — Optional durability with RDB snapshots and AOF
- **Simple & Rich API** — Easy to learn yet powerful

**Redis vs Traditional Databases**:  
Redis trades some durability for extreme speed by keeping data primarily in memory.

## Key Features

- In-memory storage with optional persistence
- Built-in replication (Master-Replica)
- Clustering for horizontal scaling
- Transactions (atomic operations)
- Pub/Sub messaging
- Lua scripting
- Modules (RediSearch, RedisJSON, RedisGraph, etc.)
- High availability with Redis Sentinel

## Data Structures

Redis is famous for its rich data types:

| Data Type       | Description                          | Common Use Cases                     |
|-----------------|--------------------------------------|--------------------------------------|
| String          | Text, numbers, binary                | Cache, counters, sessions            |
| Hash            | Field-value pairs (like object)      | User profiles, product info          |
| List            | Ordered collection (linked list)     | Queues, timelines                    |
| Set             | Unique unordered items               | Tags, unique users                   |
| Sorted Set      | Unique items with score              | Leaderboards, priority queues        |
| HyperLogLog     | Probabilistic cardinality            | Unique visitor counting              |
| Bitmap          | Bit operations                       | User flags, analytics                |
| Geospatial      | Latitude/longitude                   | Location-based services              |
| Streams         | Log-like append-only structure       | Event sourcing, messaging            |

## Where is Redis Used?

- **Caching Layer** (most common)
- Session stores
- Real-time analytics
- Leaderboards & ranking systems
- Chat/messaging applications
- Rate limiting
- Queues & background jobs
- Pub/Sub for real-time features

**Companies using Redis**: Twitter (now X), GitHub, Stack Overflow, Pinterest, Snapchat, Discord, Uber, etc.

## Real-World Applications

1. **Caching** — Store frequent DB queries
2. **Session Management** — Fast user sessions
3. **Real-time Leaderboards** — Gaming, contests
4. **Rate Limiting** — API throttling
5. **Message Queues** — Background job processing
6. **Real-time Chat** — Via Pub/Sub or Streams
7. **Analytics Dashboards** — Fast counters & metrics

## How Redis Works (Step-by-Step)

### Core Architecture

1. **Client sends command** → Redis Server (TCP)
2. **Command is parsed** and validated
3. **Executed on in-memory data structure**
4. **Response sent back** immediately
5. **Background processes**:
   - Replication to replicas
   - RDB snapshot or AOF rewrite
   - Key eviction (when memory limit reached)

### Simple Flowchart (Text)

```mermaid
flowchart TD
    A[Client Request] --> B[Redis Server]
    B --> C{Command Type?}
    C -->|Read| D[Fast Memory Access]
    C -->|Write| E[Update in RAM]
    E --> F[Optional: Append to AOF]
    D --> G[Return Response]
    E --> G
    G --> H[Background: Replication + Persistence]