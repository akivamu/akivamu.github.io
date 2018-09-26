---
layout: post
title: Git delete some commits in history
tags:
- DevOps
---


# Delete a bad commit

Current history:
```
Good1(HEAD) - Good2 - Bad - Good3 - Good4 ...
```

We want to remove `Bad` commit and result like this:
```
Good1(HEAD) - Good2 - Good3 - Good4 ...
```

## Solution
Use `git rebase -i`
```
git rebase -i Good3
```

An interactive prompt is shown:
```
pick Bad
pick Good2
pick Good1
```

Edit as below (drop commit `Bad`):
```
drop Bad
pick Good2
pick Good1
```

Then finish rebasing.