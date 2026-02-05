---
layout: post
title: "Error Handling in Programming: Best Practices and Techniques"
date: 2026-02-05 16:30:00+0700
description: Error handling is one of those topics every developer uses daily, but rarely stops to think about systematically. This post will show you an overview about error handling and best practice.
mermaid:
  enabled: false
  zoomable: false
tags: programming error-handling best-practices
categories: software
citation: true
giscus_comments: true
toc:
  beginning: true
---

## Introduction

Error handling is one of those topics every developer uses daily, but rarely stops to think about _systematically_.
Different languages promote very different styles, and those styles deeply influence how we design systems.

In this post, I want to step back and answer three questions:

- What are the fundamental models of error handling?
- How do those models affect system architecture?
- What are practical patterns you can apply when writing real services?

This is not a language-specific tutorial. Instead, it’s about _how to think_ about errors.

## What Is Error Handling?

At a high level, _error handling is how software reacts to unexpected conditions_:

- invalid user input
- I/O failures
- unavailable resources
- violated assumptions

Some people think errors are bugs, but:

- Errors are expected events in an imperfect world.
- Bugs are mistakes in the code.

Good error handling makes failures:

- explicit
- observable
- diagnosable
- recoverable (when appropriate)

## Four Fundamental Error-Handling Models

Across programming languages, most error-handling mechanisms fall into 4 core models.

### 1. Exception-based Error Handling

Errors are _control flow_. They interrupt normal execution and jump up the call stack.

```java
try {
    articleService.findById(id);
} catch (ArticleNotFound e) {
    // Handle ArticleNotFound here
}
```

Characteristics:

- Non-local control flow
- Stack unwinding
- Centralized handlers possible

Typical languages

- Java, C#, Scala
- Python, JavaScript, Ruby

Strengths

- Clean happy-path code
- Easy global handling

Weaknesses

- Hidden exits
- Harder reasoning in async / concurrent code
- Easy to over-catch or swallow errors

This model works well when frameworks control the lifecycle (e.g. Spring, Quarkus, ...).

### 2. Error-as-Values

Errors are returned explicitly as values, alongside results.

```go
article, err := service.Find(id)
if err != nil {
    return err
}
```

Characteristics

- Explicit, linear control flow
- No hidden stack jumps
- Caller decides how to handle

Typical languages

- Go
- C (return codes)
- Zig (partially)

Strengths

- Easy to reason about
- Predictable behavior
- Excellent for concurrent systems

Weaknesses

- Verbose
- No compile-time enforcement

This model is very common in system software and distributed services.

### 3. Result / Algebraic Error Types

A function returns either a success value or a typed error.

```rust
fn find(id: Id) -> Result<Article, FindError>
```

```scala
def findArticleById(id: String): Either[ArticleNotFoundException, Article]
```

Characteristics

- Explicit like error values
- Errors are _typed_
- Compiler forces handling

Typical languages

- Rust
- Scala (Either, Try)
- Haskell
- Swift

Strengths

- Very strong correctness guarantees
- Excellent for domain modeling
- Great for libraries

Weaknesses

- Type noise
- Can "infect" APIs

You can think of this as checked exceptions done right.

### 4. Effect-based Error Handling

Errors are modeled as _effects_ in the type system, handled separately from logic.

```pseudocode
findArticle : Id -> Article ! { ArticleError }
```

Typical languages/frameworks

- OCaml (algebraic effects)
- Koka
- Scala ZIO
- TypeScript (Effect library)

Strengths

- Extreme composability
- Minimal boilerplate
- Very powerful abstractions

Weaknesses

- Steep learning curve
- Overkill for most production systems

This model is still mostly academic or research. Usually, I don't use this model.

## Checked vs Unchecked vs Value Errors

This distinction mainly applies to exception-based systems.

- Checked exceptions (Java):
  The compiler forces you to catch or declare them.
  Usually used by library writer.
- Unchecked exceptions:
  No compiler enforcement.
- Error values / Result types:
  Explicit by construction.

Many modern languages moved away from checked exceptions because:

- They leak implementation details
- They force meaningless boilerplate
- They don’t scale well in large systems

Instead, they prefer explicit return channels.

## Error Handling as an Architectural Decision

Error handling is not just a language feature — it’s an _architectural choice_.

| Architecture Type          | Suitable Model  |
| -------------------------- | --------------- |
| Framework-centric          | Exceptions      |
| Microservices              | Error values    |
| Domain-driven design       | Result / Either |
| System tools / daemons     | Error values    |
| Research / Language design | Effects         |

> If failures are expected and frequent, make them explicit.

## Practical Error Handling Patterns

### 1. Handle Errors Where You Add Value

If you can’t improve the error, don’t handle it.

Bad:

```go
if err != nil {
    log.Println(err)
    return err
}
```

Good:

```go
if err != nil {
    return fmt.Errorf("find article %s: %w", id, err)
}
```

### 2. Translate Errors at Boundaries

Lower layers deal with technical errors.
Higher layers deal with business meaning.

```go
if errors.Is(err, sql.ErrNoRows) {
    return ArticleNotFound
}
```

This keeps domain logic clean and decoupled from infrastructure.

### 3. Log Errors Only Once

If you return an error, don’t log it. If you log an error, don’t return it.

Log at system boundaries:

- HTTP handlers
- CLI entry points
- goroutine roots
- message consumers

This avoids duplicated logs and preserves context.

### 4. Panic Is Not Error Handling

In Go and Rust, `panic` is not an exception system.

Use panic only for:

- programmer bugs
- violated invariants
- impossible states

Expected failures should _never_ panic.

## Testing Error Paths

Good error handling is testable.

- Unit tests should assert error types
- Integration tests should assert error mapping
- Observability (logs, metrics) should expose failure modes

Errors are part of your API, so test them like features.

## My Practial Recommendation

Do:

- Fail fast, handle later.
- Use specific error type for specific business.
- Meaningful error messages.
- Wrap and re-throw when adding context.
- Standardize error responses (e.g. RFC-9457 ProblemDetails).
- Document the error (e.g. Javadoc).
- Catch the most specific error first.

Don't:

- Log too much but hard to understand.
- Swallow error.
- Catch all as the same (e.g. Throwable).

## Conclusion

In this article, I have shown you an overview about the error handling and best practices.

- There are four fundamental error-handling models
- Each model fits different system architectures
- Explicit error handling scales better in distributed systems
- Log once, at boundaries
- Treat errors as first-class design elements

Good error handling doesn’t eliminate failures, it makes failures _boring_, _visible_, and _understandable_.

Feel free to leave your comments if you have any suggestion or question!
