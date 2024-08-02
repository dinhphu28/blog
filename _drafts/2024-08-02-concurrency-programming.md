---
layout: post
title: Concurrency Programming
date: 2024-08-02 08:30:00-0400
description: Concurrency allows programs to deal with many tasks at once.
tags: performance
categories: system-design
giscus_comments: true
related_posts: false
toc:
  beginning: true
---

### Introduction

Concurrency allows programs to deal with many tasks at once. But writing concurrent programs is not really easy. We will face with so many problem such as: threads, race conditions, deadlocks,...

### History of computer

So many years ago, the hardware power of computer is not powerful as today. CPU and RAM are limited, a task it can process in a while depends on CPU Clock Speed. In that days, to increase the tasks which computers can process in the same time, they enhance the core, clock speed of CPU. But dealing with threads manually is not a easy feat for developers, and they can't increase the CPU Clock Speed and number of core to much because of technical circumstances.

When CPU and RAM become more powerful, to solve this problem, we don't handle threads directly but append tasks to a queue. Everything about threads should be managed by system. Developer only needs to     manage how the tasks should be process.

### What is concurrency?

In abstraction, we can think that many tasks can be processed in the same time. In detail, tasks are not really processed at the same time, they're suspended when they don't need CPU (waiting for IO) and allow next task in queue processed. When the task that suspended completes IO, it will be continued.

In this way, we don't need multi threads to write a concurrent program. The task can be processed in sequential queue in only 1 thread system, or concurrent queue in multi thread system to optimize CPU resource.

