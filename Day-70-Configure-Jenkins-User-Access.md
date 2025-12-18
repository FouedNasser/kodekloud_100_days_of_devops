# Day 70: Configure Jenkins User Access

The Nautilus team is configuring granular user access for their Jenkins server to ensure that developers have the appropriate permissions for their roles. This involves implementing the Project-based Matrix Authorization Strategy.

## Challenge Description

*   Create a new Jenkins user named `james`.
*   Install the **Matrix Authorization Strategy** plugin.
*   Configure **Project-based Matrix Authorization Strategy** globally.
*   Assign overall **Read** permission to `james`.
*   Ensure the `admin` user retains **Administer** permissions and disable all access for anonymous users.
*   Grant `james` only **Read** permissions for a specific existing job.

## Key Concepts

*   **Matrix Authorization Strategy Plugin:** Provides a matrix-based authorization model to manage permissions at both global and project levels.
*   **Project-based Matrix Authorization:** Allows defining permissions for specific users or groups on a per-project (job) basis.
*   **Overall Permissions:** Global permissions that apply to the entire Jenkins instance (e.g., Read, Administer).
*   **Job Permissions:** Specific permissions for individual jobs (e.g., Build, Configure, Read).

## Solution

### 1. Install Necessary Plugin

1.  We log in to the Jenkins UI with the `admin` credentials.
2.  Go to **Manage Jenkins** > **Plugins** > **Available plugins**.
3.  Search for **Matrix Authorization Strategy**.
4.  Check the plugin and click **Install**.
5.  Check the box **Restart Jenkins when installation is complete and no jobs are running**.
6.  We wait for Jenkins to restart and log back in.

### 2. Create the Jenkins User

1.  Navigate to **Manage Jenkins** > **Users**.
2.  Click **Create User**.
3.  Enter the following details:
    *   **Username:** `james`
    *   **Password:** `B4zNgHA7Ya`
    *   **Confirm password:** `B4zNgHA7Ya`
    *   **Full name:** `James`
    *   **E-mail address:** `james@stratos.xfusioncorp.com`
4.  Click **Create User**.

### 3. Configure Global Authorization Strategy

1.  Navigate to **Manage Jenkins** > **Security**.
2.  Under **Authorization**, select **Project-based Matrix Authorization Strategy**.
3.  **Configure Admin:**
    *   Click **Add user or group...**, enter `admin`, and click **OK**.
    *   Check the **Administer** box in the `admin` row (this usually checks all other boxes).
4.  **Configure James:**
    *   Click **Add user or group...**, enter `james`, and click **OK**.
    *   Check **only** the **Read** checkbox under the **Overall** category.
5.  **Restrict Anonymous Users:**
    *   Ensure the **Anonymous** row has no checkboxes selected.
6.  Click **Save**.

### 4. Configure Job-Specific Permissions

1.  On the Jenkins dashboard, click on the existing job.
2.  Click **Configure** in the sidebar.
3.  Scroll to the **General** section and check **Enable project-based security**.
4.  **Add James to Job:**
    *   Click **Add user or group...**, enter `james`, and click **OK**.
    *   Check **only** the **Read** checkbox under the **Job** category.
5.  Click **Save**.

## Verification

To verify the configuration:

1.  Log out of the `admin` account.
2.  Log in as `james` with the password `B4zNgHA7Ya`.
3.  We should be able to see the Jenkins dashboard (Overall Read).
4.  We should be able to see the specific job we configured.
5.  Inside the job, we should only see the job details but **not** have options to "Build Now", "Configure", or "Delete Job" (Job Read only).
6.  Attempting to access administrative pages via URL should result in an "Access Denied" message.
