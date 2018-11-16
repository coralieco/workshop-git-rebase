# workshop-git-rebase

**The objective of this kata is to learn how to use git rebase command.**

Here are the rules:

**goal**
- Reconstitute the full poem in correct order
- The best configuration is to keep the historic in order, such as commit list in order:

```
# paragraph_1
P1/l1
P1/l2
P1/l3
P1/L4
P1/l4 corrected
P1/l5

# paragraph_2
P2

# paragraph_3
P3/l1
P3/l2
P3/l3
P3/l4
P3/l5

# paragraph_4
P4/l1
P4/l2
P4/l3
P4/l4
P4/l5

# title
Title
Author
```

Seeing that, the challenges are:
- For P1, use the correct commit for `l4` (corrected) and not take the `bad` commit
- For P2, just rebase
- For P3, handle 3 different branches, don't take the bad commit on one of the branches
- For P4, put the commit in the correct order `l1` before `l2`
- For `bad` branch, do not merge any of the commits when merging 'P4'
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
Don't read this, if you don't want too much help.

P1: `git rebase -i` expected
P2: `git rebase` expected
P3: `git reset -hard` expected
P4: `git rebase -onto` expected
title: `git rebase -i` expected

---

## Corrections

**starting**

First thing first, you would need to fetch all remotes branches so you have a visibility on the branches that exist.

Ok let's proceed in order.

- We start on `poem` branch

```
$ git checkout poem
```

`git log` can teach us that there is already lines from paragraph_1 on the branch.

**paragraphe 1**

- let's checkout on paragraph_1

```
$ git checkout poem
```

 with `git log` we see that there is a `bad` commit to remove on the branch.

 I use `git rebase interative` to drop this commit.

 Start it with the last commit you want to retain as-is:

```
$ git rebase -i <after-this-commit>
```

Knowing this, I can apply a

```
$ git rebase -i HEAD~2
```

So I have the possibility to edit the before last commit, which is the `bad` one.

The VIM table opens, and then I `edit` the `pick` option in front on the `bad` commit and change it to `drop`. Many informations are provided below for the different options that can be applied to a `rebase`. (edit, drop, squash...)

Then I have a conflit to resolve. The terminal tells me that:

```
Auto-merging poem.txt
CONFLICT (content): Merge conflict in poem.txt
error: could not apply 71c8b26... P1/L5

Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".

Could not apply 71c8b26... P1/L5
```

which points to the commit P1/L5 (paragraph 1 line 5). So this is what I need to fix.

Once it is resolved. I can:

```
$ git add poem.txt
$ git rebase --continue
[detached HEAD 4ae337c] P1/L5
1 file changed, 1 insertion(+)
Successfully rebased and updated refs/heads/paragraph_1.
```

Then I can check that `bad` commit disappeared with `git log`

I can make a simple rebase to check that poem and paragraph_1 are up to date

```
$ git rebase poem
Current branch paragraph_1 is up to date.
```

Then I can safely merge to poem.

```
$ git checkout poem
$ git merge paragraph_1
Updating ec7a5bb..4ae337c
Fast-forward
 poem.txt | 3 ++-
 1 file changed, 2 insertions(+), 1 deletion(-)
```

**paragraphe 2**

I checkout to the paragraph_2

```
$ git checkout paragraph_2
```

This only thing to do now is to rebase poem into paragraph_2 so the changes from paragraph_1 we just merged into poem are integrated into paragraph_2.

```
$ git rebase poem
First, rewinding head to replay your work on top of it...
Applying: P2
Using index info to reconstruct a base tree...
M	poem.txt
Falling back to patching base and 3-way merge...
Auto-merging poem.txt
CONFLICT (content): Merge conflict in poem.txt
error: Failed to merge in the changes.
Patch failed at 0001 P2
Use 'git am --show-current-patch' to see the failed patch

Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".
```

A conflict has to be resolved. Which is normal as in the paragraph_1 there was a correction on a line 4 (commit `P1/l4 corrected`) that was not in paragraph_2. So now, git needs to know which line is the right one.

```
$ git add poem.txt
$ git rebase --continue
```

Then I can merge
```
$ git checkout poem
$ git merge paragraph_2

Updating 4ae337c..d89334b
Fast-forward
 poem.txt | 6 ++++++
 1 file changed, 6 insertions(+)
```

**paragraphe 3**

Many branches on paragraph_3.

```
$ git log
```

I have commit `P3/l1` and `P3/l2` on `paragraph_3_bis` so I need to catch the `P3/l3` (which is on paragraph_3_bis), `P3/l4`(which is on paragraph_3_bis), `P3/l5`(which is on paragraph_3_bis_bis).

Nothing special happens on paragraph_3_bis, I can do a simple rebase and merge it to paragraph_3

```
$ git checkout paragraph_3_bis
$ git rebase paragraph_3
Current branch paragraph_3_bis is up to date.
$ git checkout paragraph_3
$ git merge paragraph_3_bis
Updating 3b0648f..37e11ec
Fast-forward
 poem.txt | 2 ++
 1 file changed, 2 insertions(+)
```

So now with a `git log` we can see that I have l1, l2, l3 and l4, on paragraph_3. I just need the l5.

```
$ git checkout paragraph_3_bis_bis
$ git log
```

The git log shows that there is a `bad` commit (last commit of the branch). I can use a `git reset --hard HEAD~` or a rebase interactive. Let's do the last one.

```
$ git rebase -i HEAD~2
```

In the command line, I say drop the `bad` commit. Once it is done, I can rebase this branch with paragraph_3 so it will be up to date before merging.

```
$ git rebase paragraph_3
First, rewinding head to replay your work on top of it...
Applying: P3/L5
Using index info to reconstruct a base tree...
M	poem.txt
Falling back to patching base and 3-way merge...
Auto-merging poem.txt
CONFLICT (content): Merge conflict in poem.txt
error: Failed to merge in the changes.
Patch failed at 0001 P3/L5
Use 'git am --show-current-patch' to see the failed patch

Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".
```

Once conflict is resolved, I can merge paragraph_3_bis_bis into paragraph_3, then to poem. (I can also directy merge paragraph_3_bis_bis into poem after rebasing as there are now the same)

```
$ git add poem.txt
$ git rebase --continue
Applying: P3/L5
$ git checkout paragraph_3
$ git merge paragraph_3_bis_bis
Updating 37e11ec..c9dccd5
Fast-forward
 poem.txt | 1 +
 1 file changed, 1 insertion(+)

$ git rebase poem
First, rewinding head to replay your work on top of it...
Applying: P3/L1
Using index info to reconstruct a base tree...
M	poem.txt
Falling back to patching base and 3-way merge...

$ git add poem.txt
$ git rebase --continue
Applying: P3/L1
Applying: P3/L2
Using index info to reconstruct a base tree...
M	poem.txt
Falling back to patching base and 3-way merge...
Auto-merging poem.txt
Applying: P3/L3
Applying: P3/L4
Applying: P3/L5

$ git checkout poem
Switched to branch 'poem'
Your branch is ahead of 'origin/poem' by 3 commits.
  (use "git push" to publish your local commits)

$ git merge paragraph_3
Updating d89334b..1d6851e
Fast-forward
 poem.txt | 6 ++++++
 1 file changed, 6 insertions(+)
```

**paragraphe 4**

```
$ git checkout paragraph_4
```

There is two commits that are not in the correct order. We can see that with a `git log`.

```
git rebase -i HEAD~5
```

I use a rebase interactive to change the order of the two commit (just change the line order)

Then, resolve the conflicts (there are two in a row)

```
Auto-merging poem.txt
CONFLICT (content): Merge conflict in poem.txt
error: could not apply 65d21ca... P4/L1
...
$ git add poem.txt
$ git rebase --continue
[detached HEAD 850d81b] P4/L1
 1 file changed, 15 insertions(+)

$ git add poem.txt
$ git rebase --continue
[detached HEAD cf2077f] P4/L2
 1 file changed, 1 insertion(+)
Successfully rebased and updated refs/heads/paragraph_4.
```

Then we see that `paragraph_4` inherits from a `bad_branch` that we don't want to merge into poem.

Be sure that `bad_branch` is in local otherwise the command will fail. (you can checkout bad_branch and come back to be sure)

```
$ git rebase --onto poem bad_branch paragraph_4
```

Then there are many conflicts that have to be resolved. Then we can merge.
```
First, rewinding head to replay your work on top of it...
Applying: P4/L1
Using index info to reconstruct a base tree...
M	poem.txt
Falling back to patching base and 3-way merge...
Auto-merging poem.txt
CONFLICT (content): Merge conflict in poem.txt

$ git add poem.txt
$ git rebase --continue
Applying: P4/L1
Applying: P4/L2
Using index info to reconstruct a base tree...
M	poem.txt
Falling back to patching base and 3-way merge...
Auto-merging poem.txt
Applying: P4/L3
Applying: P4/L4
Applying: P4/L5

$ git checkout poem
$ git merge paragraph_4
Updating 1d6851e..c7e4ce4
Fast-forward
 poem.txt | 6 ++++++
 1 file changed, 6 insertions(+)
```

**title**

The last step is to squash two commits together (well, one into the previous: `Title incomplete` and `Title`). We can do this with a `git rebase interactive` using the `squash` command.

Then we can rebase this branch and merge it into poem safely.

Once poem is clean, we can deploy on Master :)
