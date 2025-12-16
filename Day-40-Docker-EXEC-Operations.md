# Day 40: Docker EXEC Operations

## Challenge Description
The Nautilus DevOps team needed to configure services on a `kkloud` container running on App Server 1 (`stapp01`). The specific task was to configure the Apache web server (assumed to be already installed) within the `kkloud` container to listen on port `5003` instead of its default HTTP port. This configuration should ensure Apache listens on all interfaces (localhost, container IP, etc.), and the Apache service must remain up and running after the configuration change.

## Key Concepts
*   **`docker exec`:** A Docker command that allows us to execute commands inside a running container, making it possible to interact with its environment without entering an interactive shell.
*   **Apache Configuration Files:** Apache's behavior is controlled by configuration files, primarily `ports.conf` (to define listening ports) and virtual host configuration files (like `000-default.conf` to define how Apache responds to requests on specific ports/hosts).
*   **`sed` command:** A powerful stream editor used here for non-interactive text replacement within configuration files, which is ideal for scripting changes within containers.

## Solution

We followed these steps to configure Apache within the `kkloud` container:

1.  **SSH into Application Server 1 (`stapp01`):**
    We connected to `stapp01` using the `tony` user.
    ```bash
    ssh tony@stapp01
    # Password: Ir0nM@n
    ```

2.  **Configure Apache to listen on port `5003`:**
    We used `docker exec` with the `sed` command to modify the Apache configuration files. This changed the `Listen` directive in `ports.conf` and the `VirtualHost` directive in `000-default.conf` to use port `5003`.
    ```bash
    sudo docker exec kkloud sed -i 's/Listen 80/Listen 5003/' /etc/apache2/ports.conf
    sudo docker exec kkloud sed -i 's/<VirtualHost \*:80>/<VirtualHost \*:5003>/' /etc/apache2/sites-available/000-default.conf
    ```

3.  **Restart the Apache service inside the container:**
    After modifying the configuration, we restarted the Apache service to apply the changes.
    ```bash
    sudo docker exec kkloud service apache2 restart
    ```

## Verification
To verify that Apache was successfully configured to listen on port `5003` and was running, we performed these checks:

*   **Check Apache service status:**
    ```bash
    sudo docker exec kkloud service apache2 status
    ```
    We expected the output to show Apache running.

*   **Check if Apache is listening on port `5003`:**
    (Note: `net-tools` package, which provides `netstat`, might need to be installed inside the container if it's not present).
    ```bash
    sudo docker exec kkloud apt update && sudo docker exec kkloud apt install net-tools -y # Install net-tools if needed
    sudo docker exec kkloud netstat -tuln | grep 5003
    ```
    We expected to see an entry indicating Apache listening on `:::5003` or `0.0.0.0:5003`.

*   **Confirm the container is still running:**
    ```bash
    sudo docker ps
    ```
    We confirmed that the `kkloud` container was still listed as `Up ...`.
