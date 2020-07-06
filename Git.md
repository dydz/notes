# Git Done Right

This is my first note.

## Break apart work into logical commits

Before I can get to using Git right, the basic development workflow needs to be right. The basic unit of measure in this discussion is the patch, which I will refer interchangeably with the word commit throughout the rest of this note unless otherwise noted and I'll explain why in a moment. To be precise a commit is its own logical unit that represents one or more patches applied to a source control management system (aka repository). So, why will I be using patch and commit interchangebly? Because...

## One Patch <=> One Commit

The right way to do software engineering is to identify the smallest logical code change (aka smallest logical change aka SLC) that does not break the build or tests. The SLC should be represented in 1 patch that is then applied to the repository in 1 commit. Why? Because:

1. It is easier to review and check correctness of small changes than large ones.
   	Why is it easier to review? Smaller changes have less characters to read. They also make it easier to keep discussions focused on the small details to ensure correctness.
2. It can be easily and cleanly reverted if its wrong.

Finding the SLC is a skill, but here's a general strategy I do when I can't identify it from the get-go:

1. Free flow write code and checkpoint my progress via "git commit" whenever I want until I get something working end-to-end.
2. Aggressively combine all the commits produced in (1) using "git commit --amend" or "git rebase -i" into logical commits.

This results in one or more SLC commits. Nobody knows how many times I had to fix some silly syntax error or that I forgot to add a file at one point...just like it should be.

## Rebase or merge?

Now that 1 patch == 1 commit we can get to the primary motivation for this note. What is the best merge strategy?