---
layout: post
title: "Why Blocking I/O Hurts and How Asynchronous Fixes It"
date: 2026-04-17 13:52:00+0700
description: Blocking I/O wastes CPU cycles and limits scalability. This post explains how asynchronous programming solves the problem and improves performance.
mermaid:
  enabled: true
  zoomable: false
tags: system-design concurrency performance async io java
categories: software
citation: false
giscus_comments: true
toc:
  beginning: true
---

## Problem of Blocking I/O Way

We know that, not all process in our code is CPU-bound. Some of them are I/O-bound, such as network calls, file system access, database queries, etc.

Almost software applications process a lot of I/O-bound tasks. But if and thread is blocked while waiting for an I/O operation to complete, it cannot do anything else. Result in inefficient resource utilization and poor performance.

The question is, why we don't create a new thread for each I/O-bound task? The answer is, creating and managing threads can be expensive in terms of system resources. If we create too many threads, it can lead to thread contention, increased memory usage, and even system instability.

E.g: Java OS thread stack size is 1MB by default. If we create 1000 threads, it will consume 1GB of memory just for the thread stacks.
Okay, we can pay more memory cost to create more threads, but in reality, it almost doesn't resolve the problem.

And I think with the current prices of RAM, we don't want to do that.

Why it takes so much cost? - "Context switching".

We know that CPU will read from L1, L2 cache (much faster than RAM) before reading from main memory. When a thread is switched out, the CPU needs to save the current state of the thread (including registers, program counter, etc.) and load the state of the next thread to be executed. This process is called context switching.

If we always do "context switching", the cache efficiency will lost. When a thread is switched out, the CPU's cache may be filled with data that is relevant to the thread that is being switched out. When the next thread is switched in, it may need to access different data that is not in the cache, leading to cache misses and increased latency. Now the CPU will do useless work than doing the actual task.

## Asynchronous

Because we cannot create a new thread for each I/O-bound task, we need to find a way to allow the thread to do other work while waiting for the I/O operation to complete. This is where asynchronous programming comes in.

The async concept is decoupling when and what to execute.

How does it work?

Think at the lowest level task which just do I/O operation, I call it "atomic task".
Each higher level task is composed of multiple atomic tasks, called "task chain". When an atomic task is waiting for I/O operation to complete, it can yield control to the next atomic task.

```pseudocode
CLASS Task
    PROPERTY next

    METHOD run()
        execute((result) -> {
            IF result.success AND next != null THEN
                next.run()
            ELSE IF NOT result.success THEN
                HandleError()
            END IF
        })
END CLASS


// usage
taskA.next <- taskB
taskB.next <- taskC

taskA.run()
```

Ideally, we have no blocking thread, all of them always be processed by the CPU, so the number of threads should be equal to the number of CPU cores. I will show you this problem and why in Java people usually use more threads than CPU cores right after this.

Some folks may ask, finally, the atomic task still needs to wait for the I/O, it still blocks the thread, right?

First, instead let the CPU wait, it hands the job of to the OS Kernel using features like epoll, IOCP and the hardware controller will notify the OS -> application when the I/O operation is complete to continue the tasks.

But some cases, such as JDBC drivers, do not support async operations, so the thread will be blocked while waiting for the I/O operation to complete.

Right, we can think about the R2DBC, but forget it, it's just a special case about the database drivers.

Yeah, we realize that, if we have many JDBC tasks, we have no more thread to do other tasks. So we need create more threads to CPU pause current thread and swap in another one, which continue the task chain.
When the JDBC's thread is completed, the CPU will swap back the thread to continue.

With this approach, we can minimize the context switching but still do tasks efficiently.

## The backpressure problem

Because in async, we can have many tasks running in fraction of second. We will accidentally DDoS our database, right?

No, the thread pool which has a limited number of threads will act as a safety valve to limit number of threads can query the database.

But if we let too many threads in thread pool, the DB will crash. Another approach is database connection pool, such as HikariCP.
Especially when we use Virtual Thread that allow us to create almost unlimited threads.

## Downside

For developer, we must manually split the code into pieces. We must write the "start" part, then provide a callback for the "finish" part. The thread is freed because we explicitly ended the function.
So the code is harder to read, debug. I think everyone knows "callback hell". Anyway, we have other syntax makes it easier like async/await in JavaScript, for/yield in Scala.
But I think the "linear" coding style like blocking is easier to swallow.

In next article, I will show you how the Virtual Thread works and why we should use it.
