# Day 34: Git Hook

## Challenge Description
The Nautilus application development team was working on a git repository `/opt/media.git`, cloned under `/usr/src/kodekloudrepos/media` on the storage server (`ststor01`). The team wanted to set up a `post-update` hook in this git repository so that whenever any changes are pushed to the `master` branch, it automatically creates a release tag with the name `release-YYYY-MM-DD`, where YYYY-MM-DD is the current date. We needed to merge the `feature` branch into `master`, create and test the hook, and ensure the changes are pushed. All tasks were performed using the `natasha` user, without altering repository or directory permissions.

## Key Concepts
*   **Git Hooks:** Scripts that Git executes before or after events like commit, push, and receive.
*   **`post-update` Hook:** A server-side hook that runs after a push has been successfully applied to the remote repository. It's triggered on the receiving repository.
*   **Git Tag:** A pointer to a specific commit in the repository's history, often used to mark release versions.
*   **Bare Repository:** A repository that stores only the Git revision history, without a working directory (e.g., `/opt/media.git`).

## Solution

We followed these steps to accomplish the task:

1.  **SSH into the storage server as `natasha`**
    We connected to `ststor01` using the `natasha` user.
    ```bash
    ssh natasha@ststor01
    # Password: Bl@kW
    ```

2.  **Navigate to the working clone**
    We changed the directory to the client repository.
    ```bash
    cd /usr/src/kodekloudrepos/media
    ```
    We confirmed the existing branches:
    ```bash
    git branch
    ```
    This showed both `master` and `feature` branches.

3.  **Merge `feature` into `master` (without pushing yet)**
    We checked out the `master` branch and merged the `feature` branch into it.
    ```bash
    git checkout master
    git merge feature
    ```
    If `git` reported "Already up to date," it confirmed the branches were already in sync, which was acceptable for this step.

4.  **Go to the bare repository's hooks directory**
    This was a critical step, as `post-update` hooks run on the bare remote repository.
    ```bash
    cd /opt/media.git/hooks
    ```
    We verified the current directory:
    ```bash
    pwd
    # Expected output: /opt/media.git/hooks
    ```

5.  **Create the `post-update` hook script**
    We used `vi` to create the `post-update` file.
    ```bash
    vi post-update
    ```
    We inserted the following script content:
    ```bash
    #!/bin/bash

    DATE=$(date +%F)
    TAG="release-$DATE"

    git tag -f "$TAG"
    ```
    This script generates a tag name using the current date and forces the creation of this tag.

6.  **Make the hook executable**
    We ensured the script had execute permissions.
    ```bash
    chmod +x post-update
    ```
    We verified the permissions:
    ```bash
    ls -l post-update
    # Expected output similar to: -rwxr-xr-x 1 natasha natasha ... post-update
    ```

7.  **Trigger the hook by pushing to `master`**
    We returned to the client working clone and pushed the changes to the `master` branch. This action triggered the `post-update` hook on the remote repository (`/opt/media.git`).
    ```bash
    cd /usr/src/kodekloudrepos/media
    git push origin master
    ```

## Verification
After the push, we verified that the tag was created on the remote repository. To see it locally, we fetched the tags and then listed them.
```bash
git fetch --tags
git tag
```
We observed a tag named `release-2025-12-16` (or the current date) in the output, confirming the hook's successful execution.
