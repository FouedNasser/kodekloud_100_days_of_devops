# Day 26: Git Manage Remote

## Challenge Description
The xFusionCorp development team added updates to the project that is maintained under `/opt/official.git` repo and cloned under `/usr/src/kodekloudrepos/official`. Recently some changes were made on Git server that is hosted on Storage server in Stratos DC. The DevOps team added some new Git remotes, so we need to update remote on `/usr/src/kodekloudrepos/official` repository as per details mentioned below:

1.  In `/usr/src/kodekloudrepos/official` repo add a new remote `dev_official` and point it to `/opt/xfusioncorp_official.git` repository.
2.  There is a file `/tmp/index.html` on same server; copy this file to the repo and add/commit to `master` branch.
3.  Finally push `master` branch to this new remote origin.

## Key Concepts
*   **Git Remotes:** Managing remote repositories (`git remote add`).
*   **Git Push:** Pushing changes to a specific remote (`git push <remote> <branch>`).

## Solution

1.  **SSH into the storage server:**
    ```bash
    ssh natasha@ststor01
    ```

2.  **Navigate to the repository:**
    ```bash
    cd /usr/src/kodekloudrepos/official
    ```

3.  **Add the new remote `dev_official`:**
    ```bash
    git remote add dev_official /opt/xfusioncorp_official.git
    ```

4.  **Copy the `index.html` file to the repository:**
    ```bash
    cp /tmp/index.html .
    ```

5.  **Add and commit the changes:**
    ```bash
    git add index.html
    git commit -m "Added index.html"
    ```

6.  **Push the `master` branch to the new remote:**
    ```bash
    git push dev_official master
    ```

## Verification
1.  **Verify the remote URL:**
    ```bash
    git remote -v
    ```
    This should list `dev_official` pointing to `/opt/xfusioncorp_official.git`.

2.  **Verify the push:**
    You can try to clone the remote repo or check the log if possible, but the successful push command output is the primary verification.
