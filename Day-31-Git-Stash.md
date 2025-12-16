# Day 31: Git Stash

## Challenge Description
The development team has stashed some in-progress changes in the `/usr/src/kodekloudrepos/media` repository on the storage server (`ststor01`). We need to restore a specific stash identified by `stash@{1}`, commit the restored changes, and push them to the origin.

## Key Concepts
*   **Git Stash:** Temporarily shelves (or stashes) changes you've made to your working copy so you can work on something else, and then come back and re-apply them later.
*   **Git Stash Apply:** Restores the changes recorded in the stash to the working directory.
*   **Git Commit & Push:** Saving the changes to the history and uploading them to the remote repository.

## Solution

1.  **Access the Storage Server**
    SSH into the storage server `ststor01` using user `natasha`.
    ```bash
    ssh natasha@ststor01
    # Password: Bl@kW
    ```

2.  **Navigate to the Repository**
    Move to the repository directory.
    ```bash
    cd /usr/src/kodekloudrepos/media
    ```

3.  **List Stashed Changes**
    (Optional) Check the list of stashed changes to confirm the existence of `stash@{1}`.
    ```bash
    git stash list
    ```

4.  **Restore the Stash**
    Apply the specific stash entry `stash@{1}`.
    ```bash
    git stash apply stash@{1}
    ```

5.  **Commit and Push Changes**
    Stage the restored files, create a commit, and push to the remote repository.
    ```bash
    git add .
    git commit -m "Restored stash@{1}"
    git push origin master
    ```

## Verification
We can verify the changes by checking the git log to ensure the new commit exists.
```bash
git log --oneline
```
