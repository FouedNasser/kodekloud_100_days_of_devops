# Day 77: Jenkins Deploy Pipeline

The Nautilus DevOps team is transitioning to an automated deployment process for a new static website. We were tasked with creating a Jenkins pipeline that pulls code from a Gitea repository and deploys it to a shared storage location accessible by multiple application servers.

## Challenge Description

*   Add the Storage Server (`ststor01`) as a Jenkins slave node with the remote root directory `/var/www/html`.
*   Create a Jenkins pipeline job named `datacenter-webapp-job`.
*   The pipeline must have a single stage named `Deploy` (case-sensitive).
*   The pipeline must deploy the `web_app` repository from Gitea to `/var/www/html` on the Storage Server.
*   The website must be accessible at the root LBR URL without any subdirectories.

## Key Concepts

*   **Jenkins Pipeline:** A suite of plugins which supports implementing and integrating continuous delivery pipelines into Jenkins.
*   **Distributed Builds:** Using agent (slave) nodes to offload work from the Jenkins master and execute tasks on specific infrastructure.
*   **Shared Storage Deployment:** Using a central network-attached storage (NAS) to serve content to multiple web servers simultaneously.
*   **Shell Scripting in Pipeline:** Using `sh` steps to execute manual commands for flexibility when standard plugins face permission or environment constraints.

## Solution

### 1. Prepare the Storage Server

Before adding the server as a node, we established SSH trust and installed the necessary environment.

1.  **SSH into Jenkins:**
    ```bash
    ssh jenkins@jenkins
    ```
2.  **Establish SSH Trust:**
    ```bash
    ssh-copy-id natasha@ststor01
    ```
3.  **Install Java:**
    We installed Java 17 on the storage server to support the Jenkins agent.
    ```bash
    ssh natasha@ststor01 "sudo yum install -y java-17-openjdk"
    ```

### 2. Configure Jenkins Slave Node

1.  Navigated to **Manage Jenkins** > **Nodes** > **New Node**.
2.  **Node Name:** `Storage Server`
3.  **Remote root directory:** `/var/www/html`
4.  **Labels:** `ststor01`
5.  **Launch method:** Launch agents via SSH.
    *   **Host:** `ststor01`
    *   **Credentials:** Added `natasha`'s SSH credentials.
    *   **Host Key Verification Strategy:** Non-verifying Verification Strategy.

### 3. Create the Deployment Pipeline

1.  Created a new **Pipeline** job named `datacenter-webapp-job`.
2.  Defined the following pipeline script, which manually clones the repository to a temporary location and copies the files to the web root using `sudo` to ensure permissions are handled correctly:

```groovy
pipeline {
    agent { label 'ststor01' }

    stages {
        stage('Deploy') {
            steps {
                script {
                    sh '''
                        rm -rf /tmp/web_app
                        git clone http://git.stratos.xfusioncorp.com/sarah/web_app.git /tmp/web_app
                        ls -la /tmp/web_app
                        echo 'Bl@kW' | sudo -S cp -r /tmp/web_app/* /var/www/html/
                        rm -rf /tmp/web_app
                    '''
                }
            }
        }
    }
}
```

### 4. Execute and Verify

1.  We triggered the build by clicking **Build Now**.
2.  We verified the console output showed a successful clone and copy operation.

## Verification

1.  **LBR Access:** We accessed the Load Balancer URL and confirmed the website content loaded correctly at the root level.
2.  **Persistence:** We confirmed that the files were physically present on the shared storage at `/var/www/html`.