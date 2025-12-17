# Day 49: Deploy Applications with Kubernetes Deployments

## Challenge Description
The Nautilus DevOps team is delving into Kubernetes for app management. We need to create a deployment named `httpd` to deploy the application using the image `httpd:latest`.

## Key Concepts
*   **Kubernetes Deployment:** A controller that provides declarative updates for Pods and ReplicaSets. It defines the desired state for an application.
*   **ReplicaSet:** A controller that maintains a stable set of replica Pods running at any given time.
*   **`kubectl`:** The command-line tool used to interact with Kubernetes clusters.
*   **`httpd:latest`:** A Docker image for the Apache HTTP Server.

## Solution

1.  **Login to Jump Host:**
    We logged into the jump host where `kubectl` is configured to manage the Kubernetes cluster.
    ```bash
    ssh thor@jump_host
    ```

2.  **Create the Deployment:**
    We used the `kubectl create deployment` command to create the deployment named `httpd` and specified the `httpd:latest` image.
    ```bash
    kubectl create deployment httpd --image=httpd:latest
    ```
    This command creates a Deployment object which, in turn, creates a ReplicaSet, and the ReplicaSet then creates the specified number of Pods (by default, one).

## Verification
To verify the successful creation and status of the deployment and its associated pods, we performed the following checks:

1.  **Check Deployment Status:**
    We verified that the deployment was successfully created by listing all deployments:
    ```bash
    kubectl get deployments
    ```
    The output confirmed that a deployment named `httpd` was present.

2.  **Check Pod Status:**
    We confirmed that the Pod(s) associated with the `httpd` deployment were running by listing pods with the `app=httpd` label:
    ```bash
    kubectl get pods -l app=httpd
    ```
    This showed the Pod(s) in a `Running` state, indicating a successful deployment.
