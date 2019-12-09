---
layout: post
title: Repository Pattern in .NET Entity Framework
tags:
- Software Design
- DotNet
---


[Why shouldn't I use the repository pattern with Entity Framework?](https://softwareengineering.stackexchange.com/questions/180851/why-shouldnt-i-use-the-repository-pattern-with-entity-framework)

[This article against Repository](https://cpratt.co/repositories-and-entity-framework/)

Summary:

- It's redundant to create Repositories on top of `DbContext`
  - `DbContext` is the Unit Of Work
  - `DbSet` is the repository
- Repository provide too primitive operations and need to repeat for every model
- For abstraction, create **Services** on top of Entity Framework
  - Service is abstraction layer, just like Repository, but with more flexible functionality
