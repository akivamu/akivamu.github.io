---
layout: post
title: JAVA Static Code Analysis
tags:
- Java
---

[This](http://continuousdev.com/2015/08/checkstyle-vs-pmd-vs-findbugs/) points out the differences among some tools:

Checkstyle|PMD|Findbugs
---|---|---
Enforce coding standards and conventions|Find patterns that can lead to bugs|Find bugs, yes
naming conventions, whitespace...|duplicated code, unused variables, dead code...|bad practices, design flaws...

[Android Lint](http://tools.android.com/tips/lint) is another tool to find potential bugs in Android app code:
  - Missing translations (and unused translations)
  - Layout performance problems (all the issues the old layoutopt tool used to find, and more)
  - Unused resources
  - Inconsistent array sizes (when arrays are defined in multiple configurations)
  - Accessibility and internationalization problems (hardcoded strings, missing contentDescription, etc)
  - Icon problems (like missing densities, duplicate icons, wrong sizes, etc)
  - Usability problems (like not specifying an input type on a text field)
  - Manifest errors

And [this](http://vincentbrison.com/2014/07/19/how-to-improve-quality-and-syntax-of-your-android-code/) provides an 
example of using Checkstyle, PMD, Findbugs and Lint by Gradle build tool.