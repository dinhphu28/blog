---
layout: post
title: "Java Virtual Thread"
date: 2026-04-17 16:19:00+0700
description: Java Virtual Thread
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

> This post continues from [Part 1: Why Blocking I/O Hurts and How Asynchronous Fixes It]({% post_url 2026-04-17-why-blocking-io-hurts-and-how-asynchronous-fixes-it %})

## Introduction

Official release at Java 21, **Virtual Thread** is a lightweight, software-based thread managed by the Java Virtual Machine (JVM) instead of the OS.

It allows us to write code in "linear" synchronous style while getting the high-scale performance of complex asynchronous code.

## How Virtual Thread works

Instead of running on the CPU, Virtual Thread run on top of a small pool of standard OS threads called Carrier Threads which usually one per CPU core.

### Mounting and Unmounting

- "Mounting"": When a Virtual Thread has work to do, the JVM mounts it into a Carrier Thread, which then executes tasks on the CPU.
- "Unmounting": When tasks hit a blocking operation (I/O), the JVM unmounts the Virtual Thread from the Carrier Thread.
  Virtual Thread's state (stack and variables) will be captured and moved from Carrier Thread to the Java Heap (RAM).
  The Carrier Thread is now free to process other Virtual Thread.

Once the I/O operation finishes, the JVM parks the Virtual Thread back into a queue. As soon as a Carrier Thread becomes available, the JVM restores the state from RAM and Virtual Thread continues exactly where it left off.

### Why it's better OS thread?

Because the state is just a tiny object in memory (~KB), much lighter than the OS thread.
And doing "mount"/"unmount", CPU does not "switch context", so we can avoid the cost I mention in previous article.

So what I mean when said CPU didn't "switch context". The work Carrier Thread do is mounting/unmounting between Virtual Threads. We can assume that the Carrier Thread now acts as the physical CPU core. The "context switching" becomes mounting/unmounting with very low memory cost.

The cost for CPU "context switching" is now approximately zero. Because the OS threads are always work to do the real task.

The question is, not matter OS thread or Virtual Thread, the Blocking I/O still there.
Because the Virtual Thread is just a higher level tasks run on OS threads as any other tasks, if it's blocked by I/O, OS thread is still be blocked, especially we still use the `java.io`, right?

Don't worry, the JVM has been rewritten to check every when we call a standard Java I/O method ("atomic" task):
If this is a Virtual Thread, unmount it.

## The Pinning Problem

**Pinning** happens when a Virtual Thread gets stuck to its Carrier Thread.

The reason is:
- Executing inside a `synchronized` block.
- Call native method (JNI), e.g. C/C++, assembly.

## Async and Virtual Thread, which one is better?

We have some reason when we should each of them.

### Virtual Thread

- **Readability**: We can write "linear" code, the old-school one that all people have already written.
- **Stack traces**: If an exception thrown in Async, the stack trace often points to the internal async runner rather than our logic. Pollute the stack trace lead to harder to debug.
- **Compatibility**: We can use the existing libraries even if not designed for async. We mostly don't need to change the old code.

### Async

- **Memory control**: In case we want to build ultra high performance system, async allows us to completely control when the memory is allocated and when tasks move.
- **Java <21**: If we must use any version of Java but before 21, we don't have the Virtual Thread so the only choice is async.
- **Pinning problem**

## Conclusion

For most of business applications, Virtual Thread is better. It makes coding easier, reduce the code complexity.
Less effort to join the project.

If you have any question or suggestions, feel free to ask or share your opinion in the comment section.

