# Day 12: Linux Network Services

## Challenge Description
Our monitoring tool has reported an issue in Stratos Datacenter. One of our app servers has an issue, as its Apache service is not reachable on port 5001 (which is the Apache port). The service itself could be down, the firewall could be at fault, or something else could be causing the issue. We need to use tools like `telnet`, `netstat`, etc., to find and fix the issue. We also need to make sure Apache is reachable from the jump host without compromising any security settings.

## Key Concepts
*   **`systemctl`**: A utility for controlling the `systemd` system and service manager. Used to start, stop, enable, and check the status of services.
*   **`journalctl`**: A utility for querying and displaying logs from the `systemd` journal. Useful for diagnosing service-related issues.
*   **`netstat`**: A command-line utility that displays network connections (both incoming and outgoing), routing tables, and a number of network interface statistics. Used to find out which process is listening on a specific port.
*   **Apache HTTP Server (`httpd`)**: A widely used open-source web server software.
*   **Port Conflict**: Occurs when two or more applications try to listen on the same network port simultaneously.
*   **`iptables`**: A command-line firewall utility that uses policy chains to allow or block traffic.

## Solution

1.  **Diagnose Apache Service Logs**:
    We started by checking the `httpd` service logs using `journalctl` to understand why it wasn't starting or was unreachable.

    ```bash
    journalctl -u httpd
    ```

    The logs revealed the following error:
    ```
    (98)Address already in use: AH00072: make_sock: could not bind to address 0.0.0.0:5001
    ```
    This indicated a port conflict, meaning another service was already using port 5001.

2.  **Identify the Conflicting Service**:
    To find out which process was using port 5001, we used `netstat`.

    ```bash
    sudo netstat -tulnp | grep 5001
    ```

    The output showed that the `sendmail` service was listening on port 5001.

3.  **Stop the Conflicting Service**:
    Since `sendmail` was using the required port for Apache, we stopped the `sendmail` service.

    ```bash
    sudo systemctl stop sendmail
    ```

4.  **Start the Apache Service**:
    With the conflicting service stopped, we could now successfully start the Apache `httpd` service.

    ```bash
    sudo systemctl start httpd
    sudo systemctl status httpd
    ```

    The service started without issues.

5.  **Configure Firewall to Allow Port 5001**:
    Even after the Apache service was running and listening on `stapp01`, we found that `curl` from the jump host was still failing with "No route to host". This indicated a firewall on `stapp01` was blocking external access to port 5001. We added an `iptables` rule to explicitly allow TCP traffic on port 5001.

    ```bash
    sudo iptables -I INPUT 1 -p tcp --dport 5001 -j ACCEPT
    sudo service iptables save # To make the rule persistent across reboots
    ```
    *Note: If `firewalld` were active, the commands would typically be:*
    ```bash
    # sudo firewall-cmd --permanent --add-port=5001/tcp
    # sudo firewall-cmd --reload
    ```

## Verification

From the jump host, we verified that the Apache service on `stapp01` was now reachable on port 5001 using `curl`.

```bash
curl http://stapp01:5001
```

This command now successfully connects and retrieves content from the Apache server.
