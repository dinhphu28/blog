---
layout: post
title: "Making a Fragile System"
date: 2026-06-01 15:22:00+0700
description: Some folks just want to make it work, but they don't care about the iceberg of software development, which leads to a fragile system. Let me tell you the story of Jack, who is a software engineer but becomes a support guy because of his mindset.
mermaid:
  enabled: false
  zoomable: false
tags: software-engineering clean-code discipline professionalism
categories: software
citation: false
giscus_comments: true
toc:
  beginning: true
---

## I will support you, but I don't want to build the feature

Some folks develop software then willing to support to users for the features they built.
Jack (an imaginary software engineer) add a new feature that he thought this usually reads and nearly never writes, so he just write the API to read data from database, but not write to database.
He willing to write to database directly every time user need to update the data.

QA team test the feature, he willing to support them to write to database. With users, he also willing too.
He build so may features like this over the time because of thinking that it spends less time to build the feature.

Few months later, people ask him to support is growing more and more. He doesn't even have time to do anything except supporting.
So now Jack is as a support guy, not a software engineer anymore. The system is not itself, but Jack is the system LoL.

Jack will have to work at night, at weekend, and even at holiday to support users.
He is so tired and stressed out, but he has to do it because of his promise to support users.
He can't even take a break or go on vacation because of the support work.

But the paradox is Jack is considered as a hero by users, his boss, and his colleagues because of his dedication to support users. He is praised for his hard work and commitment.

The sunset of the system is near, some day in the future.
No working by itself, no one knows what's going on, no scale, no maintainability, no one want to use it, and no one want to support it.
It's now a fragile system.

## The reason why we make software

We know that the human-cost is the most expensive cost in software development.
Jack has violated the basic principle of not only software engineering but also the industry: People make machine to reduce human work, not the opposite.

But the problem is not only Jack, why his boss allow him to do that? Why his colleagues don't stop him? Why the users don't stop him? Why the QA team don't stop him?

I think many of us have been Jack at some point in our career, especially when we are new to the industry.

## The iceberg of software development

Some folks throw exceptions but never handle them, they just catch them at the end by default and let the system show "Something went wrong" to users.

One day users message us and said feature didn't work, we open the logs and see so many exceptions, but we don't know which one belongs to this case.
And even if we know which one, we don't know what happened because of "Something went wrong" message, the use-less logs LoL.

Jack thinks the task is done when it's working anyway. The "done" is just the head of the iceberg.

Error handling, logging, monitoring, alerting, documentation, testing, scalability, maintainability, security, and many other things are the body of the iceberg that we can't see but we have to deal with.
It's technical debt that we have to pay later.

Stakeholders don't see the iceberg, they only see the head of the iceberg, so they think the task is done when it's working anyway. They don't care about the technical debt, they just want to see the result.

## What should we do?

The responsibility of a professional software engineer is argue with stakeholders to make them understand the iceberg.
Think what we do is not "make it work", but "make it work well", think at the point of view of users, not only the end users, but also the support users.
And especially, think by ourselves, might we are Jack in the future.

## Symptoms of a fragile system

- Tight Coupling: The components are highly dependent on each other, making them hard to change or replace without affecting each other.
- Rigid Architecture: The system is hard to adapt to new requirements without a huge refactor or rewrite.
- Lack of Testing: Some folks just write code without writing tests, or just for satisfy the CI/CD pipeline, which allows hidden bugs.
- Many of Technical Debt: It's the story of Jack.
- Poor or No Separate Concerns: The code is a mess of business logic, access control, validation and other concerns, making it hard to understand and maintain.

## Key takeaways

OK, we can follow the SOLID principles, use design patterns, and write clean code, but if we don't have the right mindset, we will still end up with a fragile system.

Always think ourselves are the users, other developers who will maintain the system in the future.

I have the quote for you, "Don't let current work takes your time in the future".

If what you've done is for end users, put yourself in their shoes.
If your code is used by other developers even yourself, they are also users.

Don't do anything with the mindset of "I will support them", "I will explain to them" or "Do it later". Remember that "Later" is never!
Professional software engineers will do it right and aware of the iceberg, not just "complete the task".

Yeah, of course, no one want others developer read our code then say "what's the f\*\*k messy things", at least.
