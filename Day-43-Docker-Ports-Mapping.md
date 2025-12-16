# Day 43: Docker Ports Mapping

## Challenge Description
The Nautilus DevOps team needed to host an application on an Nginx-based container. A ticket was assigned to set up an Nginx container on Application Server 2 in the Stratos Datacenter. We were asked to pull the `nginx:alpine-perl` image, create a container named `games`, and map host port 5001 to container port 80.

## Key Concepts
*   **Docker Image:** A lightweight, standalone, executable package of software that includes everything needed to run an application.
*   **Docker Container:** A runtime instance of a Docker image.
*   **Port Mapping:** The process of mapping a port on the host machine to a port inside the Docker container, allowing external traffic to access services running inside the container.

## Solution
To complete this task, we performed the following steps:

1.  **SSH into Application Server 2:**
    We connected to `stapp02` using the `steve` user credentials.
    ```bash
    ssh steve@stapp02.stratos.xfusioncorp.com
    ```

2.  **Pull the Docker Image:**
    We pulled the required Nginx image from the Docker Hub.
    ```bash
    sudo docker pull nginx:alpine-perl
    ```

3.  **Create and Run the Container:**
    We created a container named `games` using the pulled image, mapping host port 5001 to container port 80, and running it in detached mode.
    ```bash
    sudo docker run -d -p 5001:80 --name games nginx:alpine-perl
    ```
    *   `-d`: Runs the container in background (detached mode).
    *   `-p 5001:80`: Maps host port 5001 to container port 80.
    *   `--name games`: Names the container `games`.

## Verification
To verify the setup, we performed the following checks:

1.  **Check Running Containers:**
    We listed the running containers to confirm `games` was up.
    ```bash
    sudo docker ps
    ```

2.  **Verify Port Accessibility:**
    We used `curl` to verify that Nginx was serving content on port 5001.
    ```bash
    curl http://localhost:5001
    ```
    The command returned the default Nginx HTML content, confirming the port mapping was successful.