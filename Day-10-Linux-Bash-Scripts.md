# Day 10: Linux Bash Scripts

## Challenge Description
The production support team needs a bash script named `blog_backup.sh` on App Server 2 to automate daily backups. The script must:
1. Create a zip archive named `xfusioncorp_blog.zip` of the `/var/www/html/blog` directory.
2. Save the archive locally in `/backup/` on App Server 2.
3. Copy the archive to the Nautilus Backup Server in `/backup/`.
4. Run without asking for a password during the copy process.

## Key Concepts
*   **Bash Scripting:** Automating sequences of commands.
*   **Archiving:** Using `zip` to compress files and directories.
*   **SSH Key-Based Authentication:** Enabling passwordless login/transfer between servers.
*   **SCP (Secure Copy Protocol):** Securely transferring files between hosts.

## Solution

### 1. Access App Server 2
First, we SSH into App Server 2 using the user `steve`.
```bash
ssh steve@stapp02
# Password: Am3ric@
```

### 2. Install Required Packages
The challenge notes specify that `zip` must be installed manually.
```bash
sudo yum install zip -y
```

### 3. Configure Passwordless SSH
To allow the script to copy files to the Backup Server (`stbkp01`) without a password prompt, we set up SSH key-based authentication.

Generate an SSH key pair (press Enter to accept defaults):
```bash
ssh-keygen -t rsa
```

Copy the public key to the Backup Server (user `clint`):
```bash
ssh-copy-id clint@stbkp01
# Password: H@wk3y3
```
*Note: We verify the connection by running `ssh clint@stbkp01` to ensure we log in without a password, then `exit` to return to `stapp02`.*

### 4. Create the Backup Script
We create the script file in the required directory.
```bash
vi /scripts/blog_backup.sh
```

Add the following content to the script:
```bash
#!/bin/bash

# Navigate to the parent directory to zip the folder cleanly
cd /var/www/html/

# Create the zip archive of the 'blog' directory
zip -r xfusioncorp_blog.zip blog

# Move the archive to the local backup location
mv xfusioncorp_blog.zip /backup/

# Copy the archive to the remote Backup Server
scp /backup/xfusioncorp_blog.zip clint@stbkp01:/backup/
```

### 5. Make the Script Executable
We grant execute permissions to the script.
```bash
chmod +x /scripts/blog_backup.sh
```

### 6. Execute the Script
Finally, we run the script to perform the backup.
```bash
./scripts/blog_backup.sh
```

## Verification
We verify the backup exists locally on App Server 2:
```bash
ls -l /backup/xfusioncorp_blog.zip
```

Then, we verify the backup was successfully copied to the Backup Server:
```bash
ssh clint@stbkp01 "ls -l /backup/xfusioncorp_blog.zip"
```
