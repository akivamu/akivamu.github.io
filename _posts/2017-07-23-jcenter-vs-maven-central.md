---
layout: post
title: jCenter vs Maven Central
tags:
- Java
---

[This](https://inthecheesefactory.com/blog/how-to-upload-library-to-jcenter-maven-central-as-dependency/en) is an
awesome article. Here is the summary:
  - jCenter and Maven Central are both **Maven Repository**
  - jCenter is hosted by bintray.com. Maven Central is hosted by sonatype.org.
  - Other can also host their own Maven Repo servers.
  - Should publish your library to standard server (e.g. jCenter and Maven Central...)
  - Maven Central is harder to publish to.
  - jCenter pros:
    - Use CDN
    - The largest Java Repository
    - Easy to publish library