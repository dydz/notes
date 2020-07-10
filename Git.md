# Git Done Right

This is my first note.

## Break apart work into logical commits

Before I can get to using Git right, the basic development workflow needs to be right. The basic unit of measure in this discussion is the patch, which I will refer interchangeably with the word commit throughout the rest of this note unless otherwise noted and I'll explain why in a moment. To be precise a commit is its own logical unit that represents one or more patches applied to a source control management system (aka repository). So, why will I be using patch and commit interchangebly? Because...

## One Patch <=> One Commit

The right way to do software engineering is to identify the smallest logical change (SLC) that does not break the build or tests. The SLC should be represented in 1 patch that is then applied to the repository in 1 commit. Why? Because:

1. It is easier to review and check correctness of small changes than large ones.
   	Why is it easier to review? Smaller changes have less characters to read. They also make it easier to keep discussions focused on the small details to ensure correctness.
2. It can be easily and cleanly reverted if it's wrong.
3. It makes it easy to bisect commits (i.e. use "git bisect") to find regressions and progressions.

Finding the SLC is a skill, but here's a general strategy I do when I can't identify it from the get-go:

1. Free flow write code and checkpoint my progress via "git commit" whenever I want until I get something working end-to-end.
2. Aggressively combine all the commits produced in (1) using "git commit --amend" or "git rebase -i" into logical commits.

This results in one or more SLC commits. Nobody knows how many times I had to fix some silly syntax error or that I forgot to add a file at one point...just like it should be.

## Rebase or merge?

Now that 1 patch == 1 commit we can get to the primary motivation for this note. What is the best merge strategy?

Rebase locally then optionally merge.

Why? Because:
1. It helps ensure that each SLC is conflict free, which is required to not break the build.
2. It requires that conflicts found in (1)  are addressed in each SLC, which helps also ensure that each SLC does not break the build or tests.

After rebasing, a build and test run should be performed and fixes for any failures should be combined with the appropriate SLCs to ensure that each SLC continues to not break the build or tests. When ready to push the changes (to a review branch or to the primary branch), repeat the rebase process until there are 0 conflicts. If working in a topic branch (i.e. a non-primary branch) and pushing to the primary branch (e.g. origin/master) now is the time to optionally merge it using "git merge" and then "git push" to the remote. Otherwise, just push the commits to the remote now.

What is the reason to wait until **after** rebasing to "git merge"? **Why not "git merge" from the get-go?** Doing a "git merge" from the get-go would create a merge commit. If there were any conflicts then they would be resolved here, not in the SLCs. After resolution it is no longer technically correct to call the commits under the merge commit SLCs because they don't build individually or may cause test failures (or both). This is an important property that should be preserved to make it easy in the future to bisect commits to find a regression or progression. That's why a "git merge", if done at all, should be done **after** rebasing.

What's the benefit of doing a "git merge" then? It really only benefits a repo archeologist: the merge commit marks in the timeline (graphically seen in gitk) that a set of commits originated from a topic branch before being merged into the primary branch. That's it.

