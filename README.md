# Workshop `git rebase`

## Learn `git rebase` with a poem

**The objective of this kata is to learn how to use git rebase command.**

🌲 **set up**

```
git clone git@github.com:coralieco/workshop-git-rebase.git
cd workshop-git-rebase
git checkout poem
git checkout paragraph_1
```
🌲 **goal**

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

🌲 **visualize branches**

![](images/schema_git_rebase.jpeg?raw=true)


Seeing that, the challenges are:

- For `paragraph_1`, use the correct commit for `l4` (corrected) and not take the `bad` commit
- For `paragraph_2`, just rebase
- For `paragraph_3`, manage the 3 different branches, don't take the bad commit on `paragraph_3_bis_bis` branch
- For `paragraph_4`, put the two first commits in the correct order (`l1` before `l2`)
- For `bad_branch`, do not merge any of the commits into`poem` when merging `paragraph_4`
- For `title` branch, squash the 2 commits related to titles

🌲 **rules**

- You can use `git rebase` with options as much as you want
- You can't use `cherry-pick`
- you have to merge into `poem` (do not use `master`)
- level 1: You can't commit anything
- level 2: You have to preserve merges

🌲 **help**

- There is one commit per line of the poem (except for `paragraph_2` where there is a unique commit): `P1/l3` means this is a line 3 of Paragraph 1.
- Start in order: `paragraph_1` / `paragraph_2` / `paragraph_3` / `paragraph_4` / `Title`
- Some of these command are really helpful:
  - `git log`
  - `git reflog`
  - `git reset --hard`
- There will be conflicts to resolve ! Do not focus too much on resolving conflicts, this is not the workshop purpose. You can use the final poem below (lines are indicated) to help you out resolving the conflicts..

🌲 **final poem**

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

To resolve conflicts, information about lines number can be very useful.

🌲 **more help**

Don't read this, if you don't want too much help.

- `paragraph_1`: `git rebase -i` expected
- `paragraph_2`: `git rebase` expected
- `paragraph_3`: `git rebase -i` expected - `git reset -hard` can be used
- `paragraph_4`: `git rebase -onto` expected
- `title`: `git rebase -i` expected

---

## Corrections

🌲 **starting**

First thing first, you would have to fetch all remotes branches so you have a visibility on the branches that exist.

Ok now let's start and proceed in order.

- We start on `poem` branch

```
$ git checkout poem
```

`git log` can teach us that there is already lines from paragraph_1 on the branch.

🌲 **paragraphe 1**

- let's checkout on paragraph_1

```
$ git checkout paragraph_1
```

 with `git log` we see that there is a `bad` commit to remove on the branch.

 I use `git rebase interative` to drop this commit.

 The git-rebase doc explains "Start it with the last commit you want to retain as-is":

```
$ git rebase -i <after-this-commit>
```

Knowing this, I can apply a

```
$ git rebase -i HEAD~2
```

So I have the possibility to edit the before last commit, which is the `bad` one.

The VIM table opens, and then I `edit` the `pick` option in front on the `bad` commit and change it to `drop`. Many informations are provided just under for the different options that can be applied to a `rebase`. (edit, drop, squash...)

Then I have a conflict to resolve. The terminal tells me that:

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

Then I check that `bad` commit has disappeared with `git log`

I can make a simple rebase to check that `poem` and `paragraph_1` branches are up to date

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

🌲 **paragraphe 2**

I checkout to the paragraph_2

```
$ git checkout paragraph_2
```

This only thing to do here is to rebase poem into `paragraph_2` so the changes from `paragraph_1` we just merged into poem are integrated into paragraph_2.

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

A conflict has to be resolved. Which is normal as in the `paragraph_1` there was a correction on a line 4 (commit `P1/l4 corrected`) that was not in `paragraph_2`. So now, git needs to know which line is the right one.

```
$ git add poem.txt
$ git rebase --continue
```

Then we can merge
```
$ git checkout poem
$ git merge paragraph_2

Updating 4ae337c..d89334b
Fast-forward
 poem.txt | 6 ++++++
 1 file changed, 6 insertions(+)
```

🌲 **paragraphe 3**

There are 3 branches related to the paragraph 3.

```
$ git log
```

I have the commits `P3/l1` and `P3/l2` on `paragraph_3_bis`.
So I need to catch the `P3/l3` (which is on paragraph_3_bis), `P3/l4`(which is on paragraph_3_bis), `P3/l5`(which is on paragraph_3_bis_bis).

Nothing special happens on `paragraph_3_bis`, I can do a simple rebase and merge it to `paragraph_3`

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

With a `git log` we can see that I have `l1`, `l2`, `l3` and `l4`, on `paragraph_3`. I just need the `l5`.

```
$ git checkout paragraph_3_bis_bis
$ git log
```

The git log shows that there is a `bad` commit (last commit of the branch).
I can use a `git reset --hard HEAD~` or a rebase interactive. Let's do the last one.

```
$ git rebase -i HEAD~2
```

In the command line, we specify `drop` for the `bad` commit. Once it is done, we can rebase this branch with `paragraph_3` so it will be up to date before merging.

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

Once conflict is resolved, we can merge `paragraph_3_bis_bis` into `paragraph_3`, then to `poem`. (I can also directly merge `paragraph_3_bis_bis` into `poem` after rebasing as there are now the same)

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

🌲 **paragraphe 4**

```
$ git checkout paragraph_4
```

With a `git log`, we see that there is two commits that are not in the correct order

```
git rebase -i HEAD~5
```

We use a rebase interactive to change the order of the two commits (just change the line order)

Then, we resolve the conflicts (there are two in a row)

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

Be sure that `bad_branch` is in local otherwise the command will fail.
(You can checkout bad_branch and come back to be sure)

```
$ git rebase --onto poem bad_branch paragraph_4
```

There are many conflicts that have to be resolved. Then we can merge.
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

🌲 **title**

The last step is to squash two commits together (well, one into the previous: `Title incomplete` and `Title`). We can do this with a `git rebase interactive` using the `squash` command.

Finally we can rebase this branch and merge it into poem safely.

Once poem is clean, we can deploy on Master :)

---

## Conclusion

Hope you had fun and learnt more about `git rebase` with this workshop !
