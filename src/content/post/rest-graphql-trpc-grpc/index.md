---
title: "REST, GraphQL, tRPC, or gRPC?"
description: "Comparing REST, GraphQL, tRPC, gRPC in building APIs"
publishDate: "27 August 2024"
tags: ["systems", "learning-notes"]
---

# REST, GraphQL, tRPC, or gRPC?

## REST

An architectural style that uses a stateless, client-server, cacheable communications protocol. REST is often used in web services development.

Pros:

- Easy to understand and build

Cons:

- Tightly coupled to the client
- Over-fetching and under-fetching of data

## GraphQL

A query language for APIs and a runtime for executing those queries by using a type system you define for your data.

Pros:

- Client can request only the data they need
- Strongly typed schema

Cons:

- Hard to get right, often need a SME on the team

## tRPC

A TypeScript-first framework for building scalable, type-safe APIs with TypeScript.

Pros:

- Type-safe APIs if you are using TypeScript in both the client and server
- Thrives in a monorepo setup
- Runs on top of HTTP/2
- Out-of-the-box optimizations and features
- Lightweight compared to GraphQL

Cons:

- Does not fix the over-fetching and under-fetching problem

## gRPC

A high-performance, open-source universal RPC framework.

Pros:

- Good for multi-language, inter-service communication
- Runs on top of HTTP/2 
- Really fast and optimized
- Supports streaming

Cons:

- Not as mature as REST or GraphQL
- Does not work with JSON, uses Protocol Buffers, a binary serialization format
