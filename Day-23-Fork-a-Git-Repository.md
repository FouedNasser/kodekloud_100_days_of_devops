# Day 23: Fork a Git Repository

## Challenge Description
There is a Git server utilized by the Nautilus project teams. Recently, a new developer named Jon joined the team and needs to begin working on a project. To begin, he must fork an existing Git repository.

The requirements are:

1) Click on the Gitea UI button located on the top bar to access the Gitea page.

2) Login to Gitea server using username `jon` and password `Jon_pass123`.

3) Once logged in, locate the Git repository named `sarah/story-blog` and fork it under the `jon` user.

Note: For tasks requiring web UI changes, screenshots are necessary for review purposes. Additionally, consider utilizing screen recording software such as loom.com to record and share your task completion process.

## Key Concepts
*   **Git Fork:** Creating a personal copy of a repository on the remote server. This allows independent development without affecting the original project.
*   **Gitea:** A self-hosted Git service similar to GitHub or GitLab.

## Solution

### 1. Access Gitea
*   Open the Gitea interface by clicking the **Gitea** button on the lab's top bar.

### 2. Login
*   Enter the credentials:
    *   **Username:** `jon`
    *   **Password:** `Jon_pass123`

### 3. Locate the Repository
*   Navigate to the **Explore** tab or use the search bar to find `sarah/story-blog`.
*   Click on the repository name to open its project page.

### 4. Fork the Repository
*   Locate the **Fork** button in the top right corner of the repository page (icon usually looks like a pitchfork).
*   Click the **Fork** button.
*   Confirm the fork to the `jon` user account.

## Verification
*   You should be redirected to `http://<gitea-url>/jon/story-blog`.
*   The page title should now read `jon/story-blog` indicating it is a fork owned by the user `jon`.
