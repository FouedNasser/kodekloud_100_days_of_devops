# Day 36: Deploy Nginx Container on Application Server

## Challenge Description
The Nautilus DevOps team required an Nginx container deployment on Application Server 1 (`stapp01`). The task specified that we needed to create a container named `nginx_1` using the `nginx:alpine` image and ensure that this container was in a running state.

## Key Concepts
*   **Docker Container:** A lightweight, standalone, executable software package that bundles an application and all its dependencies (libraries, system tools, code, runtime) into a single unit.
*   **Docker Image:** A read-only template used to create Docker containers. Images are built from a Dockerfile and can be stored in a registry like Docker Hub.
*   **`docker run`:** The Docker command used to create and start a new container from a given image. The `-d` flag runs the container in detached mode (in the background).
*   **`nginx:alpine`:** Refers to a specific version of the Nginx Docker image that is based on Alpine Linux, known for its small size and security.
*   **`docker ps`:** A Docker command used to list all currently running containers.

## Solution

We followed these steps to deploy the Nginx container on `stapp01`:

1.  **SSH into Application Server 1 (`stapp01`):**
    We connected to `stapp01` using the `tony` user.
    ```bash
    ssh tony@stapp01
    # Password: Ir0nM@n
    ```

2.  **Run the Nginx container:**
    We used the `docker run` command to create and start the `nginx_1` container. We specified the detached mode (`-d`) so the container runs in the background.
    ```bash
    sudo docker run -d --name nginx_1 nginx:alpine
    ```

## Verification
To verify that the `nginx_1` container was running successfully, we used the `docker ps` command:
```bash
sudo docker ps
```
We observed `nginx_1` listed among the running containers, with a status indicating it was `Up` for a certain duration, confirming the successful deployment.
