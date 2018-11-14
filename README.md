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
P1/L4
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
- You can't use `cherry-pick`
- you have to merge into `poem`
- level 1: You can't commit anything
- level 2: You have to preserve merges

**help**

- Start in order: `paragraph_1` / `paragraph_2` / `paragraph_3` / `paragraph_4` / `Title`
- Some of these command are really helpful:
  - `git log`
  - `git reflog`
  - `git reset --hard`

**final poem**

```
1. The Road Not Taken
2. BY ROBERT FROST
3.
4. Two roads diverged in a yellow wood,
5. And sorry I could not travel both
6. And be one traveler, long I stood
7. And looked down one as far as I could
8. To where it bent in the undergrowth;
9.
10. Then took the other, as just as fair,
11. And having perhaps the better claim,
12. Because it was grassy and wanted wear;
13. Though as for that the passing there
14. Had worn them really about the same,
15.
16. And both that morning equally lay
17. In leaves no step had trodden black.
18. Oh, I kept the first for another day!
19. Yet knowing how way leads on to way,
20. I doubted if I should ever come back.
21.
22. I shall be telling this with a sigh
23. Somewhere ages and ages hence:
24. Two roads diverged in a wood, and I—
25. I took the one less traveled by,
26. And that has made all the difference.
```

**more help**
Don't read this, if you don't want to much help.

P1: `git rebase -i` expected
P2: `git rebase` expected
P3: `git reset -hard` expected
P4: `git rebase -onto` expected
title: `git rebase -i` expected
