---
layout: post
title: SOLID principles
date: 2024-03-27 11:59:00-0400
description: SOLID is an acronym for the first five object-oriented design principles by Robert C. Martin
tags: oop principles
categories: system-design
giscus_comments: true
related_posts: false
toc:
  beginning: true
---

### SOLID principles

SOLID stands for:

- **S**ingle Responsibility Principle

- **O**pen/Closed Principle

- **L**iskov Substitution Principle

- **I**nterface Segregation Principle

- **D**ependency Inversion Principle

These principles is the experience of many software developers that led to a set of design principles that are more robust, more maintainable, and easier to understand.

These principles is the experience of many software developers, taking from so many projects. These are writed in book called "Clean Architecture" of Robert C. Martin (aka Uncle Bob). Yeah, he is a great software engineer and I'm a fan of him.

SOLID principles help us to create maintainable, scalable, readable, reusable and testable software.

So if we can apply these principles, we will have a good software design, easier to create new features, reduce costs of so many things. At least, for developers, it will be easier to understand the code and maintain it.

### The Single Responsibility Principle (SRP)

> A class should have one and only one reason to change, meaning that a class should have only one job.

An active corollary to Conway's law: The best structure for a software system is heavily influenced by the social structure of the organization that uses it so that each software module has one, and only one, reason to change. - Robert C. Martin

### The Open/Closed Principle (OCP)

> Objects or entities should be open for extension but closed for modification.

Bertrand Meyer made this principle famous in the 1980s. The gist is that for software systems to be easy to change, they must be designed to allow the behavior of those systems to be changed by adding new code, rather than changing existing code. - Robert C. Martin

### The Liskov Substitution Principle (LSP)

> Let q(x) be a property provable about objects of x of type T. Then q(y) should be provable for objects y of type S where S is a subtype of T.

Barbara Liskov's famous definition of subtypes, from 1988. In short, this principle says that to build software systems from interchangeable parts, those parts must adhere to a contract that allows those parts to be substituted one for another. - Robert C. Martin

### The Interface Segregation Principle (ISP)

> A client should never be forced to implement an interface that it doesn’t use, or clients shouldn’t be forced to depend on methods they do not use.

This principle advises software designers to avoid depending on things that they don't use. - Robert C. Martin

### The Dependency Inversion Principle (DIP)

> Entities must depend on abstractions, not on concretions. It states that the high-level module must not depend on the low-level module, but they should depend on abstractions.

The code that implements high-level policy should not depend on the code that implements low-level details. Rather, details should depend on policies. - Robert C. Martin
