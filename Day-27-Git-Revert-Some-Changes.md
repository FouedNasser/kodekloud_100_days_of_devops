# Day 27: Git Revert Some Changes

## Challenge Description
The Nautilus application development team was working on a git repository `/usr/src/kodekloudrepos/games` present on Storage server in Stratos DC. However, they reported an issue with the recent commits being pushed to this repo. They have asked the DevOps team to revert repo HEAD to last commit. Below are more details about the task:

In `/usr/src/kodekloudrepos/games` git repository, revert the latest commit (HEAD) to the previous commit (JFYI the previous commit hash should be with initial commit message).
Use `revert games` message (please use all small letters for commit message) for the new revert.

## Key Concepts
*   **Git Revert:** Creates a new commit that undoes the changes made in a previous commit. This is a safe way to undo changes as it preserves the project history.
*   **HEAD:** Refers to the tip of the current branch, which is the last commit made on that branch.

## Solution

1.  **SSH into the storage server:**
    ```bash
    ssh natasha@ststor01
    ```

2.  **Navigate to the repository:**
    ```bash
    cd /usr/src/kodekloudrepos/games
    ```

3.  **Revert the latest commit without auto-committing:**
    We use `--no-commit` to prevent `git revert` from automatically creating a commit and opening an editor, allowing us to explicitly provide the desired commit message in the next step.
    ```bash
    git revert --no-commit HEAD
    ```

4.  **Commit the revert with the specified message:**
    ```bash
    git commit -m "revert games"
    ```

## Verification
1.  **Check the git log:**
    After performing the revert, we can check the `git log` to confirm that a new commit with the message "revert games" has been added, and the changes from the original HEAD commit have been undone.
    ```bash
    git log -n 2 --oneline
    ```
    The output should show the "revert games" commit as the latest.
