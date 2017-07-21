---
layout: post
title: How to untrack files in GIT
tags:
- DevOps
---

I accidentally commit some files that should not be tracked, e.g. folder `.gradle`, IntelliJ IDEA project file *.iml...
Obviously that's not good. Searching around, I found a thread in [SOF](https://stackoverflow.com/questions/936249/how-to-stop-tracking-and-ignore-changes-to-a-file-in-git),
in summary:
  - Step 1: `git rm --cached <files/folders>`
  - Step 2: `git update-index --no-assume-unchanged <files/folders>`
  - Step 3: Add to `.gitignore`

**Sound good, but doesn't work!**

[This](http://www.randallkent.com/2010/04/30/gitignore-not-working/) is working:
  - Step 1 (remove all from index): `git rm -r --cached .`
  - Step 2 (add again): `git add .`
  - Step 3: `git commit -m "fixing .gitignore"`
