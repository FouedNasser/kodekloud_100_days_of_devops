# Day 28: Git Cherry Pick

## Challenge Description
The Nautilus application development team has a project repository located at `/usr/src/kodekloudrepos` on the storage server (`ststor01`). The repository has two branches: `master` and `feature`. A developer working on the `feature` branch wants to merge a specific commit with the message "Update info.txt" into the `master` branch without merging the entire branch. We need to identify this commit, cherry-pick it into `master`, and push the changes.

## Key Concepts
*   **Git Cherry-Pick:** A command used to apply the changes introduced by some existing commits to the current branch.
*   **Git Log:** Used to review commit history and find commit hashes.
*   **Git Push:** Used to upload local repository content to a remote repository.

## Solution

1.  **Access the Storage Server**
    First, we SSH into the storage server `ststor01` using the user `natasha`.
    ```bash
    ssh natasha@ststor01
    # Password: Bl@kW
    ```

2.  **Navigate to the Repository**
    We navigate to the cloned repository directory.
    ```bash
    cd /usr/src/kodekloudrepos
    ```

3.  **Identify the Commit Hash**
    We switch to the `feature` branch (or simply view its log) to find the commit hash for the message "Update info.txt".
    ```bash
    git checkout feature
    git log --oneline
    ```
    We locate the commit with the message "Update info.txt" and copy its hash (e.g., `a1b2c3d`).

4.  **Cherry-Pick the Commit**
    We switch back to the `master` branch and apply the specific commit using `cherry-pick`.
    ```bash
    git checkout master
    git cherry-pick <commit-hash>
    ```

5.  **Push the Changes**
    Finally, we push the changes to the remote `master` branch.
    ```bash
    git push origin master
    ```

## Verification
To verify the changes, we can check the log of the `master` branch to ensure the commit "Update info.txt" is present.
```bash
git log --oneline
```
The commit "Update info.txt" should now be listed in the `master` branch history.
