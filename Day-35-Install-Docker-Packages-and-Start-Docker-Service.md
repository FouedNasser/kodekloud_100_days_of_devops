# Day 35: Install Docker Packages and Start Docker Service

## Challenge Description
The Nautilus DevOps team aims to containerize various applications. As part of this initiative, we needed to install `docker-ce`, `docker-ce-cli`, `containerd.io`, `docker-buildx-plugin`, and `docker-compose-plugin` packages on App Server 3 (`stapp03`). After installation, we were required to initiate and enable the Docker service.

## Key Concepts
*   **Docker Engine:** A robust platform that enables us to develop, ship, and run applications in isolated environments called containers.
*   **Docker Compose:** A tool for defining and running multi-container Docker applications, allowing us to manage application services with a single configuration file.
*   **`dnf`:** The `Dandified YUM` package manager, commonly used on RPM-based Linux distributions like CentOS, which `stapp03` is based on.
*   **`systemctl`:** A command-line utility used to control the systemd system and service manager, allowing us to start, stop, enable, or disable services.

## Solution

We followed these steps to install Docker and start its service on `stapp03`:

1.  **SSH into App Server 3 (`stapp03`):**
    We connected to `stapp03` using the `banner` user.
    ```bash
    ssh banner@stapp03
    # Password: BigGr33n
    ```

2.  **Set up the Docker repository:**
    Before installing Docker Engine for the first time, we set up the Docker repository by installing `dnf-plugins-core` and adding the Docker CE repository.
    ```bash
    sudo dnf -y install dnf-plugins-core
    sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    ```

3.  **Install Docker Engine and Docker Compose:**
    We then installed the necessary Docker packages including `docker-ce`, `docker-ce-cli`, `containerd.io`, `docker-buildx-plugin`, and `docker-compose-plugin`.
    ```bash
    sudo dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```
    If prompted to accept the GPG key, we verified that the fingerprint matched `060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35` and accepted it. This command installs Docker, but does not start the service.

4.  **Start and enable Docker Engine service:**
    Finally, we started the Docker service and enabled it to start automatically upon system boot.
    ```bash
    sudo systemctl enable --now docker
    ```

## Verification
To verify the installation and that the Docker service is running, we can check its status:
```bash
sudo systemctl status docker
```
We expected to see output indicating that the Docker service is `active (running)`. We could also check the installed versions:
```bash
docker --version
docker compose version
```
