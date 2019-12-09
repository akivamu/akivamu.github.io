---
layout: post
title: Software Testing
tags:
- Testing
---

## [Write tests. Not too many. Mostly integration](https://kentcdodds.com/blog/write-tests)

- Write tests
  - Static typing and linting tools
  - Write tests
- Not too many
  - No need 100% coverage because of maintainence cost when refactoring
  - Still need 100% coverage for libraries
- Mostly integration

![Tradition](https://kentcdodds.com/static/60f8a435ae977edb9feb53efb985854f/8ff1e/2.png)

vs

![Lean](https://miro.medium.com/max/684/0*n6D7eQ_mtcidG1zR)

## [Lean Testing](https://blog.usejournal.com/lean-testing-or-why-unit-tests-are-worse-than-you-think-b6500139a009)

- End-to-end tests provide the greatest confidence, with highest time cost (implementation & execution)
- Integration tests provide the best balance of cost, speed and confidence
- Unit tests provides less cost to write and faster to run, but they test only a small part that might not even be critical

Summary:

- Use a typed language.
- Focus on integration and end-to-end tests.
- Use unit tests only where they make sense (e.g. pure algorithmic code with complex corner cases)

## [Giving up on test-first development](https://iansommerville.com/systems-software-and-technology/2016/03/17/giving-up-on-test-first-development/)

Unit test:

- You become conservative to avoid breaking lots of tests
  - More reluctant to make large-scale changes
- Makes you focus on detail rather than structure

## [The Failures of "Intro to TDD"](http://blog.testdouble.com/posts/2014-01-25-the-failures-of-intro-to-tdd/)

- Failure #1: Encouraging Large Units
  - Solution: refactoring after every test passed (Red-Green-Refactor)
- Failure #2: Encouraging Costly Extract Refactors
- Failure #3: Characterization Tests of Greenfield Code
- Failure #4: Redundant test coverage
- Failure #5: Eliminating Redundancy Sacrifices Regression Value
- Failure #6: Making a Mess with Mocks

### A Successful Approach to TDD

See the detail in post.

Basically:

- Top-down approach
- Fake it until you make it
