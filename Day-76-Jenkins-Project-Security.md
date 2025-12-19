# Day 76: Jenkins Project Security

xFusionCorp Industries requires granular access control for their Jenkins jobs. Two new developers, `sam` and `rohan`, need specific permissions for an existing job named `Packages`.

## Challenge Description

*   Grant permissions to users `sam` and `rohan` for the `Packages` job.
*   **Inheritance Strategy:** Inherit permissions from parent ACL.
*   **Permissions for `sam`:** Build, Configure, Read.
*   **Permissions for `rohan`:** Build, Cancel, Configure, Read, Update, Tag.
*   Do not modify other existing job configurations.

## Key Concepts

*   **Matrix Authorization Strategy:** A Jenkins plugin that allows for granular security controls, enabling permissions to be set globally or per project.
*   **Project-based Security:** Enabling security settings at the job level to restrict or grant access to specific users or groups.
*   **ACL Inheritance:** A mechanism where a job inherits the security settings defined at the global (parent) level.

## Solution

### 1. Install Matrix Authorization Strategy Plugin

1.  We logged into Jenkins (`admin` / `Adm!n321`).
2.  We navigated to **Manage Jenkins** > **Plugins** > **Available**.
3.  We searched for and installed the **Matrix Authorization Strategy** plugin (if not already installed).

### 2. Enable Project-based Matrix Authorization Strategy Globally

1.  We went to **Manage Jenkins** > **Security** (Configure Global Security).
2.  Under **Authorization**, we selected **Project-based Matrix Authorization Strategy**.
3.  **Critical Step:** We added the `admin` user and granted full **Administer** permissions to ensure we didn't lock ourselves out.
4.  We saved the configuration.

### 3. Configure Permissions for the 'Packages' Job

1.  We navigated to the `Packages` job and clicked **Configure**.
2.  We checked the box **Enable project-based security**.
3.  We confirmed that **Inheritance Strategy** was set to **Inherit permissions from parent ACL**.

### 4. Grant Permissions to Users

**For User `sam`:**
1.  We clicked **Add User or Group...**, entered `sam`, and clicked OK.
2.  We checked the following permissions in the matrix:
    *   **Job:** Build, Configure, Read.

**For User `rohan`:**
1.  We clicked **Add User or Group...**, entered `rohan`, and clicked OK.
2.  We checked the following permissions in the matrix:
    *   **Job:** Build, Cancel, Configure, Read.
    *   **Run:** Update.
    *   **SCM:** Tag.

### 5. Finalize

1.  We clicked **Save** to apply the changes.

## Verification

To verify, one could log in as `sam` or `rohan` and confirm that only the assigned actions are available for the `Packages` job. For example, `sam` should be able to build but not cancel a build, whereas `rohan` can do both.
