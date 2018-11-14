# workshop-git-rebase

**The objective of this kata is to learn how to use git rebase command.**

Here are the rules:

**goal**
- Reconstitute the full poem in correct order
- The best configuration is to keep the historic in order, such as commit list in order:

```
P1/l1
P1/l2
P1/l3
P1/l4 corrected
P1/l5

P2

P3/l1
P3/l2
P3/l3
P3/l4
P3/l5

P4/l1
P4/l2
P4/l3
P4/l4
P4/l5

Title
Author
```

Seeing that, the challenges are:
- For P1, use the correct commit for `l4` and not take the `bad` commit
- For P3, handle 3 different branches
- For P4, put the commit in the correct order `l1` before `l2`
- For `bad` branch, do not use any of the commits
- For `title` branch, squash the 2 commits for titles

**rules**

- You can use `git rebase` with options as much as you want
- You can use `git rebase -i` only once
- You can commit anything

**help**
