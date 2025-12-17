# Day 61: Init Containers in Kubernetes

## Challenge Description
The Nautilus DevOps team needs to test a scenario for applications that require pre-deployment configuration changes that cannot be embedded within the container image. The solution proposed is to use Init Containers. The task is to create a Deployment with the following specifications:
-   **Deployment Name:** `ic-deploy-nautilus`
-   **Replicas:** 1
-   **Labels:** `app: ic-nautilus` for both Deployment and Pod template.
-   **Init Container:**
    -   Name: `ic-msg-nautilus`
    -   Image: `fedora:latest`
    -   Command: `'/bin/bash', '-c', 'echo Init Done - Welcome to xFusionCorp Industries > /ic/beta'`
    -   Volume Mount: `ic-volume-nautilus` at `/ic`
-   **Main Container:**
    -   Name: `ic-main-nautilus`
    -   Image: `fedora:latest`
    -   Command: `'/bin/bash', '-c', 'while true; do cat /ic/beta; sleep 5; done'`
    -   Volume Mount: `ic-volume-nautilus` at `/ic`
-   **Volume:**
    -   Name: `ic-volume-nautilus`
    -   Type: `emptyDir`

## Key Concepts
-   **Init Containers:** Specialized containers that run to completion before app containers in a Pod are started. They are used for setup scripts, configuration generation, database schema upgrades, or waiting for external services.
-   **Shared Volumes:** Init Containers can share `emptyDir` volumes with app containers, enabling them to pass configuration or data needed by the main application.
-   **Deployment Resource:** Manages the desired state of a replicated application.

## Solution

1.  **Create the Deployment Manifest**
    We created a YAML file named `ic-deployment.yaml` with the Deployment definition including the Init Container, main application container, and the shared `emptyDir` volume.

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: ic-deploy-nautilus
      labels:
        app: ic-nautilus
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: ic-nautilus
      template:
        metadata:
          labels:
            app: ic-nautilus
        spec:
          initContainers:
          - name: ic-msg-nautilus
            image: fedora:latest
            command: ["/bin/bash", "-c", "echo Init Done - Welcome to xFusionCorp Industries > /ic/beta"]
            volumeMounts:
            - name: ic-volume-nautilus
              mountPath: /ic
          containers:
          - name: ic-main-nautilus
            image: fedora:latest
            command: ["/bin/bash", "-c", "while true; do cat /ic/beta; sleep 5; done"]
            volumeMounts:
            - name: ic-volume-nautilus
              mountPath: /ic
          volumes:
          - name: ic-volume-nautilus
            emptyDir: {}
    ```

2.  **Deploy the Pod**
    We applied the manifest to the Kubernetes cluster:
    ```bash
    kubectl apply -f ic-deployment.yaml
    ```

## Verification

1.  **Check Pod Status:**
    We verified that the Pod created by the Deployment was in a `Running` state:
    ```bash
    kubectl get pods -l app=ic-nautilus
    ```

2.  **Inspect Logs:**
    We checked the logs of the main container to confirm that the message written by the Init Container was being read and displayed:
    ```bash
    kubectl logs -f <pod-name> -c ic-main-nautilus
    ```
    The expected output was `Init Done - Welcome to xFusionCorp Industries` repeating every 5 seconds, confirming the successful execution of the Init Container and shared volume functionality.
