---
title: "Git & Github - Overview, Commands"
categories:
  - git
tags:
  - git
  - github
  - version control
---

## Squash the Last 3 Commits (Replace 3 with ):
1. **Start an Interactive Rebase**: Run the following command

    ```bash
    git rebase -i HEAD~3
    ```
    - This tells Git to interactively rebase the last 3 commits.
2. **Edit the Commit List**: You will see a list of the last 3 commits in your editor.
    ```bash
    pick abc123 Commit message 1
    pick def456 Commit message 2
    pick ghi789 Commit message 3
    ```
    - The pick keyword means "keep this commit as is."
3. **Modify the List to Squash Commits**: Change the second and third commits from pick to squash (or just s):
    ```bash
    pick abc123 Commit message 1
    squash def456 Commit message 2
    squash ghi789 Commit message 3
    ```
4. **Save and Close the Editor**: After modifying, save and close the editor. Git will squash the commits into one.

5. **Edit the Commit Message**: Git will then prompt you to edit the new commit message. You will see something like this:
    ```bash
    # This is a combination of 3 commits.
    # The first commit's message is:
    Commit message 1

    # The following commit messages will also be included:
    Commit message 2
    Commit message 3
    ```
    - Edit this message as desired, keeping only the relevant parts. For example:
    ```bash
    Combined commit message
    ```
    - Save and close the editor.

6. **Finish the Rebase**: Git will complete the rebase, and the last 3 commits will be combined into one.

7. **Pushed commits**: If you've already pushed these commits to a remote repository (before starting the squash process), use `git push --force` to update the branch after squashing. For example:
    ```bash
    git push origin branch-name --force
    ```