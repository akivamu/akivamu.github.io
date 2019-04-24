---
layout: post
title: System design
category:
- Computer Science series
---

## Steps to design a system

Taking [example to design Pastebin.com](https://github.com/donnemartin/system-design-primer/blob/master/solutions/system_design/pastebin/README.md)

### Step 1: Requirement & scope

- Features, Use cases
  - What does the system do? Inputs and outputs?
  - Who is going to use it?
  - How are they going to use it?
- Constraints
  - How many users?
  - Data size?
  - Latency per request?
  - Throughput (requests per duration)?
  - Read/Write ratio
- Calculate/estimate: size, performance...

### Step 2: Create a high level design

Design high level architecture

- Modules, components
- Interfaces, communications
- Which parts to be scaled

### Step 3: Design core components

Base on each use case/feature:

- Flow diagram
- Sequence diagram
- Input output

### Step 4: Scale the design

- Identify bottleneck
- Scale strategy:
  - Vertical vs horizontal
  - Load balancer
  - Caching
  - Distributed data store

## Principles

- Performance vs scalability
  - Performance: the system is slow for a single user
  - Scalability: the system is fast for a single user, but slow under heavy load
- Latency vs throughput
  - Latency: processing time per request
  - Throughput: number of requests per time
  - Best: maximal throughput with acceptable latency
- Availability vs consistency
  - Consistency: always get same result, or same error
  - Availability: always get result, but not guarantee valid or consistence result
    - Fail-over: main node (serving request) and backup nodes (in case main node fails)
    - Replication: multiple, cloned nodes (all serving request)
  - Partition Tolerance: trade off

### Optimize independent unit-of-work

Mean reducing the processing time of one single, no-dependencies task

- Optimize algorithm
- Increase processing ability (CPU, mem upgrade)
- [x] [Caching](cs-cache)

### Optimize task with external dependencies (whole system)

For a system with input and output:

- Optimize internal system:
  - Parallel, asynchronism message queue
  - Optimize dependencies
  - [Internal caching input <-> output](cs-cache)
- [External cache: CDN](cs-cache)
- Vertical scaling internal modules
  - Increase processing ability (CPU, mem)
- [ ] [Horizontal scaling](cs-scaling)
  - Use Load Balancer as single input/output port
  - Create multiple worker nodes

## Standards, documentation

TODO

## References

- [system-design-primer](https://github.com/donnemartin/system-design-primer)

### [Scalability for dummies](https://www.lecloud.net/tagged/scalability/chrono)

- Clone (horizontal scale)
  - Load balancer
  - Consistency among clones
    - Stateless request
    - Same code base
    - External, centralized data store/cache
- Database
  - RDBMS:
    - Vertical scaling
    - Tuning
  - NoSQL: sharding by nature
  - [Why RDBMS dont scale](https://www.quora.com/Why-is-it-said-that-relational-SQL-databases-do-not-scale)
- Cache
  - What to cache
  - Where to store cache entry: Persistence vs Memcache
  - When to invalidate cache entry
- Asynchronism
  - Message queue
  - Worker pool