# Day 81: Jenkins Multistage Pipeline

## Challenge Description
The xFusionCorp Industries development team is deploying a new static website using a Jenkins pipeline. The requirements involve updating the website content in a Gitea repository and creating a Jenkins pipeline job named `deploy-job` with two stages: `Deploy` and `Test`. The deployment happens on a shared storage location (`/var/www/html` on the Storage Server) which is mounted to all application servers.

## Key Concepts
*   **Jenkins Pipeline:** A suite of plugins which supports implementing and integrating continuous delivery pipelines into Jenkins.
*   **Declarative Pipeline:** A more recent and simplified way to define Jenkins pipelines using a structured syntax.
*   **Multi-stage Pipeline:** Organizing the pipeline into logical stages (e.g., Build, Deploy, Test) for better visibility and management.
*   **Git Integration:** Automating git operations like `pull` to ensure the latest code is deployed.
*   **Health Checks:** Using tools like `curl` to verify the application's availability and correctness after deployment.

## Solution

### 1. Update Content and Push to Gitea
First, we updated the `index.html` file on the Storage Server and pushed the changes to the central repository.

```bash
# SSH into the Storage Server
ssh natasha@ststor01

# Navigate to the repository
cd /var/www/html

# Update the content
echo "Welcome to xFusionCorp Industries" > index.html

# Configure git to avoid ownership issues and push changes
git config --global --add safe.directory /var/www/html
git add index.html
git commit -m "Update welcome message for Day 81"
git push origin master
```

### 2. Create Jenkins Pipeline Job
1.  We logged into Jenkins and created a new **Pipeline** job named `deploy-job`.
2.  In the **Pipeline** section, we used the following script:

```groovy
pipeline {
    agent any
    stages {
        stage('Deploy') {
            steps {
                // Execute git pull on the storage server via SSH
                sh "sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@ststor01 'cd /var/www/html && git config --global --add safe.directory /var/www/html && git pull origin master'"
            }
        }
        stage('Test') {
            steps {
                // Verify the application is accessible via the Load Balancer
                sh 'curl -f http://stlb01:8091'
            }
        }
    }
}
```

### 3. Execution and Result
*   We triggered the build for `deploy-job`.
*   The **Deploy** stage successfully executed the remote `git pull`.
*   The **Test** stage successfully ran the `curl` command against the LBR URL.
*   The pipeline finished with a **SUCCESS** status.

## Verification
*   We accessed `http://stlb01:8091` in the browser and confirmed the page displayed: `Welcome to xFusionCorp Industries`.
*   We verified the Jenkins pipeline visualization showed both stages as green.
