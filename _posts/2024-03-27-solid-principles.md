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

### Introduction

SOLID stands for:

- **S**ingle Responsibility Principle

- **O**pen/Closed Principle

- **L**iskov Substitution Principle

- **I**nterface Segregation Principle

- **D**ependency Inversion Principle

These principles is the experience of many software developers that led to a set of design principles that are more robust, more maintainable, and easier to understand.

These principles is the experience of many software developers, taking from so many projects. SOLID principles were first introduced by Robert C. Martin (aka Uncle Bob) and writed in book called "Clean Architecture" of him. Yeah, he is a great software engineer and I'm a big fan of him.

SOLID principles help us to create maintainable, scalable, readable, reusable and testable software.

So if we can apply these principles, we will have a good software design, easier to create new features, reduce costs of so many things. At least, for developers, it will be easier to understand the code and maintain it.

### The Single Responsibility Principle (SRP)

> A class should have one and only one reason to change, meaning that a class should have only one job.

An active corollary to Conway's law: The best structure for a software system is heavily influenced by the social structure of the organization that uses it so that each software module has one, and only one, reason to change. - Robert C. Martin

##### Explaination

For one class (one feature or anything) should do one thing, so when we need to change a peace of them, it will not affect the others.

E.g. A class Employee as below violates SRP

```java
public class Employee {
  private String id;
  private String name;
  private String phoneNumber;

  // Getter and Setter

  // (1)
  public EmployeeOutput toOutput() {
    EmployeeOutput output = new EmployeeOutput(/* something here */);
    return output;
  }

  public void saveToDatabase(Employee employee) {
    // some code here
  }

  public void cache(Employee employee) {
    // some code here
  }
}
```

Class Employee does more than 1 thing, and have more than reason to change.

When we need to add more features, this class become bigger and hard to read and maintain.

You can answer this question: "We need to change `saveToDatabase` but properties of Employee doesn't need to change, why this method still be there?"

You may think `saveToDatabase` and `cache` are relate to Employee so they should be there. But an Employee don't need to save him to the database or cache himself.

You can move `saveToDatabase` to class **EmployeeRepository**.

**Bonus**: _(1)_

You may wonder that why (1) be there. Yeah, someone put method mapping entity <-> model right there, but you should let this method in module/class model mapper.

In additional, we should not set name `EmployeeOutput` and method name `toOutput`, which makes confused to read and understand them.

### The Open/Closed Principle (OCP)

> Objects or entities should be open for extension but closed for modification.

Bertrand Meyer made this principle famous in the 1980s. The gist is that for software systems to be easy to change, they must be designed to allow the behavior of those systems to be changed by adding new code, rather than changing existing code. - Robert C. Martin

##### Explaination

A module should be:

- Easy to extend
- Not allowed to modify

For example:

```java
public class Dog {
  public void bark() {
    // some code here
  }
}

public class Cat {
  public void say() {
    // some code here
  }
}

public class AnimalSaying {
  public void say(AnimalType animalType) {
    switch (animalType) {
      case DOG:
        Dog dog = new Dog();
        dog.bark();
        break;
      case CAT:
        Cat cat = new Cat();
        cat.say();
        break;
      default:
        throw RuntimeException("This animal does not exist!")
    }
  }
}
```

In this example, when we want to add new animal, e.g. Duck, we need to add class `Duck` and edit class `AnimalSaying`. It make the code hard to extend and quite risky.

We can re-write as below:

```java
public abstract class Animal {
  public void say();
}

public class Dog extends Animal {
  @Override
  public void say() {
    // some code here
  }
}

public class Cat extends Animal {
  @Override
  public void say() {
    // some code here
  }
}

public class AnimalSaying {
  private Animal animal;
  public AnimalSaying(Animal animal) {
    this.animal = animal;
  }
  public void say() {
    animal.say()
  }
}
```

When we want to add `Duck`, we just need to add

```java
public class Duck extends Animal {
  @Override
  public void say() {
    // some code here
  }
}
```

Follow Open-Close principle, everything becomes easier to extends and does not make old feature failure because we don't update it.

### The Liskov Substitution Principle (LSP)

> Let q(x) be a property provable about objects of x of type T. Then q(y) should be provable for objects y of type S where S is a subtype of T.

Barbara Liskov's famous definition of subtypes, from 1988. In short, this principle says that to build software systems from interchangeable parts, those parts must adhere to a contract that allows those parts to be substituted one for another. - Robert C. Martin

### The Interface Segregation Principle (ISP)

> A client should never be forced to implement an interface that it doesn’t use, or clients shouldn’t be forced to depend on methods they do not use.

This principle advises software designers to avoid depending on things that they don't use. - Robert C. Martin

### The Dependency Inversion Principle (DIP)

> Entities must depend on abstractions, not on concretions. It states that the high-level module must not depend on the low-level module, but they should depend on abstractions.

The code that implements high-level policy should not depend on the code that implements low-level details. Rather, details should depend on policies. - Robert C. Martin

**_Some references:_**

- https://www.digitalocean.com/community/conceptual-articles/s-o-l-i-d-the-first-five-principles-of-object-oriented-design#open-closed-principle
- Clean Architecture - Robert C. Martin [Book]
