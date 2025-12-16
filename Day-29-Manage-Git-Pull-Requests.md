# Day 29: Manage Git Pull Requests

## Challenge Description
We need to merge changes from a feature branch (`story/fox-and-grapes`) into the `master` branch using a Pull Request (PR) workflow on Gitea. The user `max` has already pushed their changes. We must create a PR, assign `tom` as a reviewer, and then have `tom` approve and merge the request. This ensures code is reviewed before reaching production.

## Key Concepts
*   **Pull Request (PR):** A mechanism to notify team members that a feature is ready to be merged. It allows for code review and discussion.
*   **Code Review:** The process of examining source code by other developers to catch errors and improve quality.
*   **Merge:** Combining the changes from one branch into another.
*   **Gitea:** A self-hosted Git service (similar to GitHub or GitLab) used for version control.

## Solution

1.  **Verify Repository Content (CLI)**
    We first verified the repository status on the storage server.
    ```bash
    ssh max@ststor01
    # Password: Max_pass123
    
    # Navigate to the repository (e.g., story-blog)
    cd <repository-name>
    
    # Check logs to confirm branch and history exist
    git log --oneline --graph --all
    ```

2.  **Create Pull Request (Gitea UI)**
    *   Logged into Gitea as `max` (Password: `Max_pass123`).
    *   Navigated to the repository.
    *   Created a new Pull Request:
        *   **Base branch:** `master`
        *   **Head branch:** `story/fox-and-grapes`
        *   **Title:** `Added fox-and-grapes story`

3.  **Assign Reviewer (Gitea UI)**
    *   In the newly created PR, located the **Reviewers** section on the right sidebar.
    *   Selected `tom` to assign him as the reviewer.

4.  **Review and Merge (Gitea UI)**
    *   Logged out and logged back in as `tom` (Password: `Tom_pass123`).
    *   Navigated to the Pull Request.
    *   Reviewed the changes.
    *   Clicked **Merge Pull Request** and confirmed the action.

## Verification
After the merge, the `master` branch in the Gitea repository (and subsequently on the server after a `git pull`) will contain the "Fox and Grapes" story file.
