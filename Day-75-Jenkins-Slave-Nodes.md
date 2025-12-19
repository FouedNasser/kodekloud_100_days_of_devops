# Day 75: Jenkins Slave Nodes

The Nautilus DevOps team has installed and configured a new Jenkins server in Stratos DC. There is a requirement to add all application servers as slave nodes in Jenkins to enable remote task execution.

## Challenge Description

*   Add all application servers (`stapp01`, `stapp02`, `stapp03`) as SSH build agent/slave nodes.
*   Node names: `App_server_1`, `App_server_2`, `App_server_3`.
*   Labels: `stapp01`, `stapp02`, `stapp03`.
*   Remote root directories:
    *   App_server_1: `/home/tony/jenkins`
    *   App_server_2: `/home/steve/jenkins`
    *   App_server_3: `/home/banner/jenkins`
*   Ensure all nodes are online and functional.

## Key Concepts

*   **Jenkins Distributed Architecture:** Using a Master node for management and Agent (Slave) nodes for task execution.
*   **SSH Build Agents Plugin:** A Jenkins plugin that allows the master to connect to agents over SSH.
*   **SSH Key Authentication:** Using private/public key pairs for secure, passwordless authentication between servers.
*   **Jenkins Credentials:** Storing sensitive data like SSH private keys securely within Jenkins.
*   **Java Runtime Environment (JRE):** Jenkins agents require Java to be installed on the remote machine to execute the `remoting.jar`.

## Solution

### 1. Establish SSH Trust and Install Java

Before configuring the Jenkins UI, we prepared the application servers by establishing SSH trust and installing the required Java environment.

1.  **SSH into the Jenkins server:**
    ```bash
    ssh jenkins@jenkins
    ```
2.  **Generate an SSH key pair (without a passphrase):**
    ```bash
    ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N ""
    ```
3.  **Copy the public key to all App Servers:**
    *   **App Server 1:** `ssh-copy-id tony@stapp01` (Pass: `Ir0nM@n`)
    *   **App Server 2:** `ssh-copy-id steve@stapp02` (Pass: `Am3ric@`)
    *   **App Server 3:** `ssh-copy-id banner@stapp03` (Pass: `BigGr33n`)
4.  **Install Java on all App Servers:**
    Jenkins agents require Java to run. We logged into each server and installed Java 17:
    ```bash
    sudo yum install -y java-17-openjdk
    ```

### 2. Install SSH Build Agents Plugin

1.  We logged into the Jenkins UI (`admin` / `Adm!n321`).
2.  We navigated to **Manage Jenkins** > **Plugins** > **Available**.
3.  We searched for and installed the **SSH Build Agents** plugin, then restarted Jenkins.

### 3. Add SSH Credentials to Jenkins

1.  We went to **Manage Jenkins** > **Credentials** > **System** > **Global credentials**.
2.  We added three separate credentials for each user (`tony`, `steve`, `banner`):
    *   **Kind:** SSH Username with private key.
    *   **Username:** (e.g., `tony`)
    *   **Private Key:** We pasted the content of the private key (`~/.ssh/id_rsa`) from the Jenkins server.

### 4. Configure Slave Nodes

For each application server, we performed the following:

1.  **Manage Jenkins** > **Nodes** > **New Node**.
2.  **Node Name:** `App_server_1` (then `App_server_2`, `App_server_3`).
3.  **Remote root directory:** (e.g., `/home/tony/jenkins`).
4.  **Labels:** (e.g., `stapp01`).
5.  **Launch method:** Launch agents via SSH.
6.  **Host:** (e.g., `stapp01`).
7.  **Credentials:** Selected the corresponding credential for the user.
8.  **Host Key Verification Strategy:** Non-verifying Verification Strategy.
9.  **Save** and **Launch Agent**.

## Verification

1.  **Node Status:** We navigated to **Manage Jenkins** > **Nodes** and confirmed that all three nodes (`App_server_1`, `App_server_2`, `App_server_3`) were listed as "In sync" and showed no errors.
2.  **Connection Logs:** We inspected the "Log" for each node to ensure the `remoting.jar` was successfully copied and started.
3.  **Remote Directory:** We verified that the remote root directory was created on the app servers:
    ```bash
    ssh tony@stapp01 "ls -ld /home/tony/jenkins"
    ```
