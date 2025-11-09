# Day 1: Linux User Setup with Non-Interactive Shell

## Key Concepts

*   **User Management:** In Linux, user management is a fundamental task for system administrators. It involves creating, modifying, and deleting user accounts.
*   **Interactive vs. Non-Interactive Shell:**
    *   An **interactive shell** allows a user to log in and execute commands directly (e.g., `/bin/bash`).
    *   A **non-interactive shell** is assigned to users who do not need to log in to the system directly. This is often used for service accounts or accounts that are only used for specific automated tasks (like running a backup agent). `/sbin/nologin` is a common non-interactive shell that politely refuses a login.
*   **`useradd` command:** The `useradd` command is used to create new user accounts in Linux.
*   **`/etc/passwd` file:** This file contains a list of all registered users on the system, along with their user ID (UID), group ID (GID), home directory, and login shell.

## Solution

To create a user named `yousuf` with a non-interactive shell, we used the `useradd` command with the `-s` flag to specify the shell.

### Command

```bash
sudo useradd -s /sbin/nologin yousuf
```

### Verification

To verify the user creation and the non-interactive shell, we can use the following commands:

1.  **Check the `/etc/passwd` file:**
    ```bash
    grep yousuf /etc/passwd
    ```
    This command should show the user `yousuf` with the shell set to `/sbin/nologin`.

2.  **Attempt to switch to the user:**
    ```bash
    su - yousuf
    ```
    This command should fail and return a message like "This account is currently not available."

This approach ensures that the user is created correctly and meets the requirement of having a non-interactive shell.
