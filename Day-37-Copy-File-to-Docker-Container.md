# Day 37: Copy File to Docker Container

## Challenge Description
The Nautilus DevOps team has a confidential encrypted file, `/tmp/nautilus.txt.gpg`, located on App Server 2 (`stapp02`). We needed to copy this file into a running Docker container named `ubuntu_latest` on the same server. The file's destination within the container was specified as `/usr/src/`, and it was crucial that the file remained unmodified during the transfer process.

## Key Concepts
*   **`docker cp`:** This Docker CLI command is specifically designed to copy files or directories between a Docker host's filesystem and a container's filesystem, or vice-versa. It preserves file integrity during the transfer.
*   **Docker Host:** The operating system where the Docker daemon runs and manages containers. In this case, `stapp02` acts as the Docker host.
*   **Docker Container:** An isolated, executable environment that runs an application and its dependencies, providing a consistent runtime.

## Solution

We followed these steps to copy the file:

1.  **SSH into App Server 2 (`stapp02`):**
    We connected to `stapp02` using the `steve` user.
    ```bash
    ssh steve@stapp02
    # Password: Am3ric@
    ```

2.  **Copy the file to the container:**
    We used the `sudo docker cp` command to transfer `/tmp/nautilus.txt.gpg` from the host to the `ubuntu_latest` container's `/usr/src/` directory.
    ```bash
    sudo docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/usr/src/
    ```
    This command performs a direct copy, ensuring the file's contents are not modified.

## Verification
To confirm that the file was successfully transferred and placed in the correct location within the container, we executed a command inside the container to list the contents of the target directory:
```bash
sudo docker exec ubuntu_latest ls -l /usr/src/
```
We verified that `nautilus.txt.gpg` was present in the output of the `ls -l` command, confirming the successful file transfer.
