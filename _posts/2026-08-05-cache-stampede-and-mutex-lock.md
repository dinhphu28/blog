---
layout: post
title: "Cache Stampede Problem and Mutex Lock"
date: 2026-08-05 10:08:00+0700
description: "Cache-aside with mutex locking is a design pattern that used to prevent database hit when cache is expired, this is known as cache stampede problem."
mermaid:
  enabled: false
  zoomable: false
tags: caching concurrency system-design performance
categories: software system-design
citation: false
giscus_comments: true
toc:
  beginning: true
---

## Problem

We implement caching to reduce the database hit with cache-aside.

The strategy is just simple like this:

1. If cache hit, read from cache.
2. If cache miss, query from DB and write cache.

But sometimes we forget the problem that when cache is empty but so many requests come at the same time.

Result in all of them hit DB and write cache too, that put pressure on DB.

So, how we resolve it?

## Mutex Lock

The idea is when cache be empty, instead of let all request do the **_database read and write cache (1)_**, we just allow only 1 request to do this, the others will rely on this request.

How to do it?

**Approach:** We must have a mechanism that be a "single slot room". Then when the first request ask it to do **_(1)_**, it will lock the room and prevent any other come in, likes "I have only 1 slot for this room, and a persion has taken it. Give up or wait until he's out".

In implementation, we can use Redis `SET NX`, like this:

```lua
SET lock:instance-0123 xxx NX
```

This mean set if not exists.

We call it **Acquiring lock**.

Then whenever request come in while cache empty and start to do **_(1)_**, it cannot acquire lock. So it knows that it should not read database but just wait the cache written.

So now we have know the mechanism, but this will lock forever. So right after complete write cache, the process must release the lock immediately.

```lua
DEL lock:instance-123
```

But what happened when the process that acquired lock crashes or caused any problem, the lock is still be never released. So no any other request can read database and write cache. This call **deadlock**.

So we need to set TTL for the lock. Absolutely we cannot set the TTL too long or too short.

- If too short, lock will be released before the process complete then another process will acquire the lock and read the DB too.

- But too long, if deadlock, other process will be blocked too long, lead to increase latency.

The TTL should be 2x-3x of the max expected execution time.

E.g, overall read database and write cache process take 150ms, the TTL should be 300ms-450ms.

```lua
SET lock:instance-0123 xxx NX PX 300
```

For simple, we can set the TTL = p99 latency \* 2.

We should monitor the database read latency, lock contention rate and wait time to optimize the TTL.

For the process that cannot determine the execution time, means the execution time is not equivalent for each time and may take longer time, we can consider lock extension.

We estimate the expected TTL to set by default, then extend the lock TTL periodically until complete the process.

e.g.

```pseudocode
Thread 1:
	- acquire lock (TTL = 500ms)
	- extend TTL every 200ms
	- when complete then
		release lock
```

## Final Thoughts

The Mutex Lock is just suitable when the contention is not usually happened and the processes rely on this strategy don't require high concurrency, because the lock will block other processes lead to throughput reduction and increase the latency.

Do you remember our use-case, the first request acquired lock then all of others must wait for the lock. Behind the scene is it sleep and retry, likes the retry storm, too waste for the Redis traffic. But in this case, it just aims to reduce database hit, so mutex lock is enough, don't over-design.
