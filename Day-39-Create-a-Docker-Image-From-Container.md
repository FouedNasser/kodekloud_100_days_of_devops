# Day 39: Create a Docker Image From Container

## Challenge Description
A Nautilus developer made some changes within a running container and wanted to create a backup of these modifications. The DevOps team was tasked with creating a new Docker image from a specific running container. The requirement was to create an image named `official:datacenter` from the container `ubuntu_latest` that was running on Application Server 3 (`stapp03`).

## Key Concepts
*   **Docker Container:** A running instance of a Docker image, providing an isolated environment for an application. Changes made within a container are usually ephemeral unless committed.
*   **Docker Image:** A lightweight, standalone, and executable software package that includes everything needed to run an application. Images are typically built from Dockerfiles, but can also be created from existing containers.
*   **`docker commit`:** A Docker command that allows us to create a new Docker image from the current state of a running or stopped container. This captures any changes made to the container's filesystem and configuration.

## Solution

We followed these steps to create a new Docker image from the specified container on `stapp03`:

1.  **SSH into Application Server 3 (`stapp03`):**
    We connected to `stapp03` using the `banner` user.
    ```bash
    ssh banner@stapp03
    # Password: BigGr33n
    ```

2.  **Confirm the container is running (Optional):**
    Although the challenge stated the container was running, we could optionally verify its status to ensure it was available for committing.
    ```bash
    sudo docker ps -a
    ```

3.  **Create the new image from the container:**
    We used the `docker commit` command, specifying the source container `ubuntu_latest` and the desired new image name `official:datacenter`.
    ```bash
    sudo docker commit ubuntu_latest official:datacenter
    ```

## Verification
To verify that the new image was successfully created and tagged, we listed all local Docker images:
```bash
sudo docker images
```
We confirmed that an image named `official:datacenter` was present in the output, indicating successful completion of the task.
