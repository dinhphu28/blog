---
layout: post
title: "Software Verification: Ensuring Correctness and Reliability"
date: 2026-05-04 15:52:00+0700
description: How can we ensuring the correctness.
mermaid:
  enabled: true
  zoomable: false
tags: quality-assurance testing
categories: software
citation: false
giscus_comments: true
toc:
  beginning: true
---

## The relationship between Software Engineering, Mathematics and Science

We all know to prove a function in software is correct, we must decompose it into smaller pieces, and prove each piece is correct, recursively.
Until all of them are provable units (primitives), we can ensure the original function is correct.

**Do you realize that this is exactly the same as the Mathematical proof?**
We decompose a theorem into smaller lemmas, and prove each lemma is correct, recursively.
Until all of them are provable unit (axioms), we can prove the original theorem is correct.
And this is the key strategy of Formal Methods.

**But nowadays, why Formal Methods is not widely used in software development?**
This is because of the complexity of software, it takes so much cost to prove each small pieces of software.
When the cost for business is more important than the cost for quality, we have to make a trade-off between them.

### Science

How can we know a scientific theory is correct?
The answer is: we can't know for sure, but we can test it with experiments.

Why I say that?
Everybody knows that an Apple always falls down, but how we can be sure that?

You can said that you have seen it many times, but how many times is enough?
You cannot be sure a day in the future, an Apple will fall up, or it will float in the air.

Because of the gravity?
But how do you know the gravity acceleration direction is always down?
Do you know how the gravity works in the space?
Nowadays, the essence of gravity is still a mystery, we only know how it works, but we don't know why it works.

Back to the day the Apple falls down on Newton's head. He and human knew that the Apple always falls down because they have seen it millions of times. But they didn't know why.

People have considered the Newton's gravity is correct until Einstein's theory of relativity comes out.

Do you realize that, in science, the theories and laws cannot be proven correct.
They can be prove wrong when we have any counter-example that can disprove them.

E.g.

$$F_n = 2^{2^n} + 1$$

Fermat conjecture that where $$n$$ is a non-negative integer, $$F_n$$ is always a prime number.

But one day, Euler showed that $$F_5=2^{2^5} + 1 = 4\,924\,967\,297 = 641 \times 4\,700\,417$$ and this is composite.

Just one case is enough to prove that **Fermat numbers** is wrong.

### Software Verification in modern days

As I have said, because of the hardness of mathematical proof, nowadays, we usually use scientific method to verify the software.
That means we assume the software is correct, and we try to find any wrong case that can disprove it. People call this process **testing**.

Obviously, we accept that we cannot be sure that the software is completely correct.
But in practice, we can be sure that the software is good enough for our use, if we cannot find any wrong case after testing it with a lot of cases.

So how can we best prove that the software is good enough for our use?
Just test the highest level of software? Absolute no, because if we only test the highest level of software, we cannot be sure that the lower level of software is correct.
We can said the higher level is correct that based on a wrong thing.
That why we need to test every level of software, from the lowest level to the highest level.

## Is the Formal Methods die?

No, in some critical system, such as the aerospace, the cost of failure is so high that we have to use Formal Methods to ensure the correctness of software.
IBM also use Formal Methods for their Customer Information Control System (CICS).

## Conclusion

The method we use to verify the software nowadays depends on the cost of failure and the cost of verification.
Even if just testing, the quality of software reflects the importance of cost of failure we spending on it.
For some software that the release speed, fancy features are more important than the quality, people usually accept the lower quality and ok with the risk of failure.

As a software engineer, my opinion is that we should always try to improve the quality despite the cost of failure is not high. This is the professionalism.
