# Day 42: Create a Docker Network

## Challenge Description
The Nautilus DevOps team needed to set up several Docker environments for different applications. A team member was assigned a ticket to create some Docker networks. We were asked to create a Docker network named `beta` on App Server 3 in Stratos DC, configure it to use `macvlan` drivers, and set it to use subnet `192.168.30.0/24` and IP range `192.168.30.0/24`.

## Key Concepts
*   **Docker Networks:** Docker's networking system allows containers to communicate with each other and with the outside world.
*   **Macvlan Driver:** A Docker network driver that assigns a MAC address to each container's virtual network interface, making the container appear as a physical device on the network. This is often used for directly exposing containers to a physical network without NAT.
*   **Subnet:** A logical subdivision of an IP network.
*   **IP Range:** The range of IP addresses within a subnet that can be assigned to devices (in this case, Docker containers).

## Solution
To address this challenge, we performed the following steps:

1.  **SSH into App Server 3:**
    We connected to `stapp03` using the provided credentials.
    ```bash
    ssh banner@stapp03.stratos.xfusioncorp.com
    ```

2.  **Create the Docker Network:**
    Once connected to `stapp03`, we executed the following command to create the `macvlan` network named `beta` with the specified subnet and IP range:
    ```bash
    sudo docker network create -d macvlan \
      --subnet=192.168.30.0/24 \
      --ip-range=192.168.30.0/24 \
      beta
    ```
    *   `sudo docker network create`: The command to create a Docker network.
    *   `-d macvlan`: Specifies the `macvlan` driver.
    *   `--subnet=192.168.30.0/24`: Sets the subnet for the network.
    *   `--ip-range=192.168.30.0/24`: Specifies the IP address range for containers within the network.
    *   `beta`: The name of the Docker network.

## Verification
To verify the successful creation and configuration of the Docker network, we performed the following checks:

1.  **List Docker Networks:**
    We listed all Docker networks to confirm that the `beta` network was created with the `macvlan` driver.
    ```bash
    sudo docker network ls
    ```
    We looked for an entry similar to this:
    ```
    NETWORK ID     NAME      DRIVER    SCOPE
    <some_id>      beta      macvlan   local
    ```

2.  **Inspect the Network:**
    We inspected the `beta` network for detailed configuration, ensuring the subnet and IP range matched the requirements.
    ```bash
    sudo docker network inspect beta
    ```
    We verified that the output contained the correct `Subnet` and `IPAM` (IP Address Management) configuration, showing `192.168.30.0/24` for both.
    ```json
    [
        {
            ...
            "IPAM": {
                "Driver": "default",
                "Config": [
                    {
                        "Subnet": "192.168.30.0/24",
                        "IPRange": "192.168.30.0/24"
                    }
                ],
                "Options": {}
            },
            ...
        }
    ]
    ```