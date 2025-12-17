# Day 50: Deploy Pods with Resource Limits in Kubernetes Cluster

## Challenge Description
The Nautilus DevOps team has noticed performance issues in some Kubernetes-hosted applications due to resource constraints. To address this, we need to create a pod named `httpd-pod` with a container named `httpd-container`. We will use the `httpd:latest` image and set the following resource limits:

*   **Requests:** Memory: `15Mi`, CPU: `100m`
*   **Limits:** Memory: `20Mi`, CPU: `100m`

## Key Concepts
*   **Resource Requests:** The minimum amount of CPU and memory guaranteed to a container. Kubernetes schedules pods based on these requests.
*   **Resource Limits:** The maximum amount of CPU and memory a container is allowed to use. If a container exceeds its memory limit, it will be terminated. If it exceeds its CPU limit, its CPU usage will be throttled.
*   **CPU Units:** In Kubernetes, `100m` (millicpu) represents 0.1 of a CPU core. `1` cpu is equivalent to 1 core.
*   **Memory Units:** `Mi` stands for Mebibytes (1 Mi = 1024^2 bytes).
*   **Pod Manifest (YAML):** A declarative configuration file used to define Kubernetes objects like Pods.

## Solution

1.  **Login to Jump Host:**
    We SSHed into the `jump_host` where the `kubectl` utility is configured to interact with the Kubernetes cluster.
    ```bash
    ssh thor@jump_host
    ```

2.  **Create Pod Manifest:**
    We created a YAML file named `httpd-pod-resources.yaml` that defines the `httpd-pod` with the specified container name, image, and resource requests/limits.

    ```bash
    vi httpd-pod-resources.yaml
    ```

    Content of `httpd-pod-resources.yaml`:
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: httpd-pod
    spec:
      containers:
      - name: httpd-container
        image: httpd:latest
        resources:
          requests:
            memory: "15Mi"
            cpu: "100m"
          limits:
            memory: "20Mi"
            cpu: "100m"
        ports:
        - containerPort: 80
    ```

3.  **Apply the Manifest:**
    We used `kubectl apply` to create the pod in the Kubernetes cluster from the manifest file.
    ```bash
    kubectl apply -f httpd-pod-resources.yaml
    ```

## Verification
To verify that the pod was created with the correct resource configurations, we performed the following checks:

1.  **Check Pod Status:**
    We confirmed that the `httpd-pod` was created and transitioned to a `Running` state.
    ```bash
    kubectl get pods httpd-pod
    ```

2.  **Describe the Pod for Resource Details:**
    We used `kubectl describe` to inspect the pod's details and verify that the `requests` and `limits` for CPU and memory were correctly applied to the `httpd-container`.
    ```bash
    kubectl describe pod httpd-pod
    ```
    We specifically looked for the `Requests` and `Limits` sections in the output for the `httpd-container`.
