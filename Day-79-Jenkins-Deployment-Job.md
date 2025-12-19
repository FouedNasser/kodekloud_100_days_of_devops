# Day 79: Jenkins Deployment Job

## Challenge Description
The Nautilus DevOps team needs to automate the deployment of a static web application. Any push to the `master` branch of the `web` repository should trigger an automatic build and deployment to the shared storage directory `/var/www/html` on the Storage Server (`ststor01`). This directory is shared across all application servers. Additionally, all application servers must serve the web content on port 8080.

## Key Concepts
- Jenkins Slave Nodes (Agents)
- Jenkins Pipeline
- SCM Polling (`pollSCM`)
- Git Integration in Jenkins
- Automated Web Deployment
- Apache HTTPD Configuration (Custom Ports)

## Solution

### 1. Configure Application Servers
On each application server (`stapp01`, `stapp02`, and `stapp03`), we installed and configured Apache:
```bash
sudo yum install -y httpd
sudo sed -i 's/Listen 80/Listen 8080/' /etc/httpd/conf/httpd.conf
sudo systemctl enable httpd
sudo systemctl start httpd
```

### 2. Prepare Storage Server Permissions
On the Storage Server (`ststor01`), we ensured the user `sarah` has ownership of the document root:
```bash
sudo chown -R sarah:sarah /var/www/html
```

### 3. Add Storage Server as Jenkins Node
We configured a new Jenkins agent:
- **Name**: `StorageServer`
- **Remote root directory**: `/home/sarah/jenkins`
- **Labels**: `ststor01`
- **Launch method**: SSH
- **Host**: `ststor01`
- **Credentials**: `sarah` / `Sarah_pass123`
- **Verification Strategy**: Non-verifying Verification Strategy

### 4. Create Jenkins Pipeline Job
We created a pipeline job named `nautilus-app-deployment` with the following script:

```groovy
pipeline {
    agent {
        label 'ststor01'
    }
    triggers {
        pollSCM('* * * * *')
    }
    stages {
        stage('Deploy') {
            steps {
                checkout([$class: 'GitSCM', 
                    branches: [[name: '*/master']], 
                    userRemoteConfigs: [[
                        credentialsId: 'sarah', 
                        url: 'http://git.stratos.xfusioncorp.com/sarah/web.git'
                    ]]
                ])
                sh 'cp -r * /var/www/html/'
            }
        }
    }
}
```

### 5. Trigger Deployment
As user `sarah` on the Storage Server, we updated the local repository and pushed the changes:
```bash
cd ~/web
sed -i 's/.*/Welcome to the xFusionCorp Industries/' index.html
git add index.html
git commit -m "Update welcome message"
git push origin master
```

## Verification
1.  **Jenkins Build**: We verified that Jenkins automatically detected the push via SCM polling and triggered the build.
2.  **File Check**: We checked `/var/www/html/index.html` on `ststor01` to confirm the content was updated.
3.  **Web Access**: We accessed the Load Balancer URL and verified the message "Welcome to the xFusionCorp Industries" appeared correctly.
