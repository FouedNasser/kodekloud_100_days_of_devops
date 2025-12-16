# Day 33: Resolve Git Merge Conflicts

## Challenge Description
Developer Max is unable to push his changes to the `story-blog` repository on the storage server because the remote repository contains changes he doesn't have (pushed by Sarah). We need to pull the remote changes, which will result in a merge conflict in `story-index.txt`. The goal is to resolve this conflict manually, ensuring all 4 story titles are present and fixing a typo ("Mooose" to "Mouse"), and then push the resolved code to the origin.

## Key Concepts
*   **Git Merge Conflicts:** Occur when Git is unable to automatically resolve differences in code between two commits (e.g., when the same line is modified differently).
*   **Manual Resolution:** The process of editing the conflicted file to select the desired changes and remove conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`).
*   **Git Pull:** Fetches changes from a remote repository and tries to merge them into the current branch.

## Solution

1.  **Access the Storage Server**
    SSH into the storage server `ststor01` as user `max`.
    ```bash
    ssh max@ststor01
    # Password: Max_pass123
    ```

2.  **Navigate to the Repository**
    Move to Max's local repository.
    ```bash
    cd /home/max/story-blog
    ```

3.  **Pull Remote Changes**
    Attempting to push immediately would fail. We must first pull the latest changes from the remote.
    ```bash
    git pull origin master
    ```
    Git will report a conflict in `story-index.txt`.

4.  **Resolve the Conflict**
    Open the conflicted file using a text editor (e.g., `vi` or `nano`).
    ```bash
    vi story-index.txt
    ```
    We see conflict markers indicating the divergence between HEAD (Max's changes) and the remote branch.
    
    *   **Task 1:** Ensure all 4 story titles are listed.
    *   **Task 2:** Fix the typo "Mooose" to "Mouse".
    *   **Task 3:** Remove the conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`).
    
    The final content should look similar to this:
    ```text
    The Fox and the Grapes
    The Lion and the Mouse
    The Ant and the Grasshopper
    The Wolf and the Lamb
    ```
    Save and exit the file.

5.  **Commit the Resolution**
    Stage the resolved file and create a new commit.
    ```bash
    git add story-index.txt
    git commit -m "Resolved merge conflict and fixed typo"
    ```

6.  **Push the Changes**
    Now that the conflict is resolved and the history is merged, we can push to the remote.
    ```bash
    git push origin master
    ```

## Verification
We can verify the file content to ensure it is correct.
```bash
cat story-index.txt
```
The output should list all 4 stories correctly with "Mouse" spelled correctly.
