# Day 38: Pull Docker Image

## Challenge Description
The Nautilus project developers are preparing for a new project and require specific Docker images for testing containerized application features. Our task was to pull the `busybox:musl` Docker image on App Server 2 (`stapp02`) and then re-tag this image as `busybox:local`.

## Key Concepts
*   **Docker Image Pull:** The operation of downloading a Docker image from a remote registry (such as Docker Hub) to the local Docker host.
*   **Docker Image Retagging:** Creating a new tag or alias for an existing Docker image. This allows the same image (identified by its Image ID) to be referred to by multiple names and versions.
*   **`docker pull`:** The command-line utility used to fetch a Docker image from a registry.
*   **`docker tag`:** The command-line utility used to assign a new tag to a Docker image.

## Solution

We followed these steps to pull and re-tag the Docker image on `stapp02`:

1.  **SSH into App Server 2 (`stapp02`):**
    We connected to `stapp02` using the `steve` user.
    ```bash
    ssh steve@stapp02
    # Password: Am3ric@
    ```

2.  **Pull the `busybox:musl` Docker image:**
    We downloaded the specified image from its registry.
    ```bash
    sudo docker pull busybox:musl
    ```

3.  **Re-tag the image as `busybox:local`:**
    After the image was pulled, we created a new tag for it.
    ```bash
    sudo docker tag busybox:musl busybox:local
    ```

## Verification
To verify that both the original and the newly tagged images were present on the Docker host, we listed all local Docker images:
```bash
sudo docker images
```
We confirmed that `busybox:musl` and `busybox:local` (both pointing to the same Image ID) were listed in the output.
