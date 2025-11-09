# Day 2: Linux Temporary User Setup with Expiry Date

## Challenge Description

Create a temporary user named `anita` on App Server 2 with an expiry date of `2024-02-27`.

## Key Concepts

*   **User Expiry:** In Linux, user accounts can be configured with an expiry date, after which the account becomes inactive. This is useful for temporary access or managing accounts for contractors.
*   **`useradd` command:** The `useradd` command is used to create new user accounts.
*   **`-e` flag:** The `-e` or `--expiredate` flag with `useradd` specifies the date on which the user account will be disabled. The date format is typically `YYYY-MM-DD`.
*   **`/etc/shadow` file:** This file stores secure user account information, including password hashes and account expiration dates.

## Solution

To create a user named `anita` with an expiry date of `2024-02-27` on App Server 2, the `useradd` command was used with the `-e` flag.

### Command

```bash
sudo useradd -e 2024-02-27 anita
```

### Verification

To verify the user creation and the expiry date, you can use the following commands:

1.  **Check the user's account information:**
    ```bash
    sudo chage -l anita
    ```
    This command should display the account's last password change, password expiry, account expiry, and other related information. The "Account expires" field should show "Feb 27, 2024".

2.  **Check the `/etc/shadow` file (requires root privileges):**
    ```bash
    sudo grep anita /etc/shadow
    ```
    The output will contain several fields, and the 8th field (separated by colons) represents the account expiration date in days since January 1, 1970. You can convert `2024-02-27` to days since epoch to confirm.

This approach ensures that the user is created correctly with the specified expiry date.
