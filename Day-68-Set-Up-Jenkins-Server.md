# Day 68: Set Up Jenkins Server

The DevOps team at xFusionCorp Industries is initiating the setup of CI/CD pipelines and has decided to utilize Jenkins as their server. We need to install and configure Jenkins on the dedicated server.

## Challenge Description

*   Install Jenkins on the `jenkins` server using the `yum` utility only.
*   Start the Jenkins service.
*   Configure the initial admin user with specific details:
    *   **Username:** `theadmin`
    *   **Password:** `Adm!n321`
    *   **Full name:** `Rose`
    *   **Email:** `rose@jenkins.stratos.xfusioncorp.com`

## Key Concepts

*   **Jenkins:** An open-source automation server for CI/CD pipelines.
*   **Package Management (yum):** RedHat-based package manager used for installing software.
*   **Java Runtime:** Jenkins requires a Java Runtime Environment (JRE) to run.
*   **Jenkins Initial Setup:** Unlocking Jenkins using the initial admin password and setting up the first admin user.

## Solution

### 1. Access the Jenkins Server

We connect to the Jenkins server from the jump host using the root user:

```bash
ssh root@172.16.238.19
# Password: S3curePass
```

### 2. Install Java and Jenkins

Jenkins is a Java-based application, so we must install the Java Development Kit (JDK) first. Then, we add the Jenkins repository and install the package.

```bash
# Install wget to download the repository file
yum install wget -y

# Install OpenJDK 17
yum install java-17-openjdk -y

# Add the Jenkins repository
wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo

# Import the GPG key for the repository
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key

# Install Jenkins
yum install jenkins -y
```

### 3. Start and Enable Jenkins Service

We start the Jenkins service and enable it to start automatically on boot.

```bash
systemctl daemon-reload
systemctl enable jenkins
systemctl start jenkins

# Verify the service status
systemctl status jenkins
```

### 4. Retrieve Initial Admin Password

To unlock the Jenkins web interface, we need the auto-generated password.

```bash
cat /var/lib/jenkins/secrets/initialAdminPassword
```

### 5. Complete Setup via Jenkins UI

1.  We access the Jenkins UI by clicking the **Jenkins** button on the top bar or navigating to `http://172.16.238.19:8080`.
2.  We paste the password retrieved in the previous step and click **Continue**.
3.  We select **Install suggested plugins**.
4.  Once the plugins are installed, we create the admin user with the following details:
    *   **Username:** `theadmin`
    *   **Password:** `Adm!n321`
    *   **Full Name:** `Rose`
    *   **E-mail address:** `rose@jenkins.stratos.xfusioncorp.com`
5.  We click **Save and Continue**, then **Save and Finish**, and finally **Start using Jenkins**.

## Verification

To verify the installation:

1.  Check that the Jenkins service is active: `systemctl is-active jenkins`.
2.  Ensure we can log in to the Jenkins dashboard using the username `theadmin` and the password `Adm!n321`.
3.  Verify that the "Rose" full name is displayed in the top-right corner of the Jenkins UI.
