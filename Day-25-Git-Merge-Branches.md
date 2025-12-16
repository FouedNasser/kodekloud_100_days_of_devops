# Day 25: Git Merge Branches

## Challenge Description
The Nautilus application development team has been working on a project repository `/opt/blog.git`. This repo is cloned at `/usr/src/kodekloudrepos/blog` on the storage server in Stratos DC. The task is to:
1. Create a new branch `devops` in `/usr/src/kodekloudrepos/blog` repo from `master`.
2. Copy the `/tmp/index.html` file (present on storage server itself) into the repo.
3. Add/commit this file in the new branch.
4. Merge that branch into the `master` branch.
5. Push the changes to the origin for both of the branches.

## Key Concepts
*   **Git Branching:** Creating separate lines of development (`git checkout -b`).
*   **Git Merging:** Combining changes from one branch into another (`git merge`).
*   **Git Push:** Updating remote refs along with associated objects (`git push`).

## Solution

1.  **SSH into the storage server:**
    ```bash
    ssh natasha@ststor01
    ```

2.  **Navigate to the repository:**
    ```bash
    cd /usr/src/kodekloudrepos/blog
    ```

3.  **Create and switch to the `devops` branch:**
    ```bash
    git checkout -b devops
    ```

4.  **Copy the `index.html` file:**
    ```bash
    cp /tmp/index.html .
    ```

5.  **Add and commit the changes:**
    ```bash
    git add index.html
    git commit -m "Added index.html"
    ```

6.  **Switch back to the `master` branch and merge:**
    ```bash
    git checkout master
    git merge devops
    ```

7.  **Push the changes to the origin for both branches:**
    ```bash
    git push origin master
    git push origin devops
    ```

## Verification
1.  **Verify branches on remote:**
    You can check the remote repository to ensure both `master` and `devops` branches are updated.
    ```bash
    git ls-remote
    ```
