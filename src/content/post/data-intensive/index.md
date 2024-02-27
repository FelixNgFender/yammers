---
title: "TL;DR: Designing Data-Intensive Applications"
description: "My learning notes from the book Designing Data-Intensive Applications by Martin Kleppmann"
publishDate: "26 Feb 2024"
# updatedDate: "14 August 2023"
coverImage:
  src: "./cover.jpg"
  alt: "Designing Data-Intensive Applications"
tags: ["books", "systems", "learning-notes", "tl;dr", "draft"]
draft: true
---

This is my learning notes from the book [Designing Data-Intensive Applications](https://dataintensive.net/) by Martin Kleppmann.

## Chapter 1: Reliable, Scalable, and Maintainable Applications

- Main challenges of data-intensive applications are volume, complexity, and speed.

- Standard building blocks for a data-intensive application:
  - Databases: Store data so that they, or another application, can find it again later
  - Caches: Remember the results of an expensive operation, to speed up reads
  - Search indexes: Allow users to search data by keyword or filter it in various ways
  - Stream processing: Send a message to another process, to be handled asynchronously
  - Batch processing: Periodically crunch a large amount of accumulated data

### Thinking About Data Systems

This chapter will explore the fundamentals of what we are trying to achieve when building a data system: **R.M.S**.

- Reliability: Work correctly even in the face of adversity.
- Scalability: Have reasonable ways to deal with growth.
- Maintainability: Enable new developers to work on them productively.

### Reliability

- A fault is a deviation of a system component from its spec.
- Systems that anticipate faults are called fault-tolerant or resilient.
- A failure is when the system as a whole stops providing the required service to the user.
- It's best to design fault-tolerance mechanisms that prevent faults from causing failures.

#### Hardware Faults

- Redundancy can help mitigate hardware faults.
- There is a move towards tolerating the loss of entire machines by using software fault-tolerance techniques.
  - Has certain advantages operational advantages, e.g. rolling upgrades, etc.

#### Software Errors

- Systematic error within the system, usually correlated across nodes, and tend to cause many more system failures than hardware faults.
- Bugs lie dormant for a long time until they are triggered by an unusual set of circumstances.

#### Human Errors

- Minimizes human errors by:
  - Design systems in a way that minimizes opportunities for error.
  - Decouple the places where people make the most mistakes from the places where they can cause failures.
  - Test thoroughly at all levels, from unit tests to whole-system integration tests and manual tests.
  - Allow quick and easy recovery from human errors, to minimize their impact.
  - Set up detailed and clear monitoring, such as performance metrics and error rates.
  - Implement good management and operations practices.

### Scalability

- Describes a system's ability to cope with increased load.

### Maintainability
