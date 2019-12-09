---
layout: post
title: Software Testing
tags:
- Testing
---

Below are collections of article against heavy unit testing.

## A1. [Write tests. Not too many. Mostly integration](https://kentcdodds.com/blog/write-tests)

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

## A2. [Lean Testing](https://blog.usejournal.com/lean-testing-or-why-unit-tests-are-worse-than-you-think-b6500139a009)

- End-to-end tests provide the greatest confidence, with highest time cost (implementation & execution)
- Integration tests provide the best balance of cost, speed and confidence
- Unit tests provides less cost to write and faster to run, but they test only a small part that might not even be critical

Summary:

- Use a typed language.
- Focus on integration and end-to-end tests.
- Use unit tests only where they make sense (e.g. pure algorithmic code with complex corner cases)

## A3. [Giving up on test-first development](https://iansommerville.com/systems-software-and-technology/2016/03/17/giving-up-on-test-first-development/)

Unit test:

- You become conservative to avoid breaking lots of tests
  - More reluctant to make large-scale changes
- Makes you focus on detail rather than structure

## A4. [Unit Test Fetish](http://250bpm.com/blog:40)

- Return on investment of unit tests is an order(s) of magnitude lower than that of end-to-end tests.
- End-to-end tests test the critical path. Unit test do not.
- Unit tests ossify the internal architecture.
- There are things that can't be unit-tested.
- Some stuff has no rigorous acceptance criteria.
- Unit tests are easy to measure and you should fear that.

Below are collections of article support unit testing.

## B1. [The Failures of "Intro to TDD"](http://blog.testdouble.com/posts/2014-01-25-the-failures-of-intro-to-tdd/)

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

## My take away

- Unit test has quick feedback cycle
- Refactoring internal code of unit (keep same public interface):
  - Unit test is great
  - Quickly test if refactored unit is working
- Refactoring public interface:
  - Unit test is bad: requires more maintainance cost
  - Should prevent changes in public interface. How? -> Top-down approach
- E2E test:
  - Quickly spot behavior bug
  - Test critical paths
  - Bad at: hard to cover all cases/edge

Conclusion: Unit test and e2e test are complementary

- Unit Test:
  - Quick feedback
  - Useful for clear-defined requirement/responsibility of unit
  - Cover edge cases
  - Support internal refactoring/optimization
- E2E:
  - Useful for overall behavior, user scenario
  - Cover large areas, critical paths
