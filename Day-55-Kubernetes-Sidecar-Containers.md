# Day 55: Kubernetes Sidecar Containers

## Challenge Description
We need to implement the Sidecar pattern in Kubernetes to separate concerns between a web server and its logging mechanism. The goal is to deploy a Pod named `webserver` containing:
1.  **Main Container:** An `nginx:latest` web server (`nginx-container`).
2.  **Sidecar Container:** A `ubuntu:latest` container (`sidecar-container`) responsible for shipping/displaying logs.
3.  **Shared Volume:** An `emptyDir` volume (`shared-logs`) mounted at `/var/log/nginx` in both containers. The sidecar container should periodically read and print the contents of `access.log` and `error.log`.

## Key Concepts
- **Sidecar Pattern:** A design pattern where a helper container runs alongside the main application container in the same Pod to extend its functionality (e.g., logging, monitoring, proxying) without altering the main application code.
- **Shared Storage:** Using `emptyDir` volumes to allow multiple containers within a Pod to read and write to the same directory.
- **Pod Lifecycle:** Understanding that containers in a Pod share the same network namespace and storage volumes.

## Solution

1.  **Define the Pod Manifest**
    We created a YAML file named `webserver-pod.yaml` to define the Pod resource. This manifest specifies the `shared-logs` volume and mounts it to `/var/log/nginx` for both the Nginx and Ubuntu containers. The Ubuntu container is configured with a loop to print logs every 30 seconds.

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: webserver
    spec:
      volumes:
      - name: shared-logs
        emptyDir: {}
      containers:
      - name: nginx-container
        image: nginx:latest
        volumeMounts:
        - name: shared-logs
          mountPath: /var/log/nginx
      - name: sidecar-container
        image: ubuntu:latest
        command: ["sh", "-c", "while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log; sleep 30; done"]
        volumeMounts:
        - name: shared-logs
          mountPath: /var/log/nginx
    ```

2.  **Deploy the Pod**
    We applied the manifest to the cluster to create the Pod:
    ```bash
    kubectl apply -f webserver-pod.yaml
    ```

3.  **Verify Deployment**
    We checked the status of the Pod to ensure both containers were up and running:
    ```bash
    kubectl get pod webserver
    ```
    Expected output:
    ```
    NAME        READY   STATUS    RESTARTS   AGE
    webserver   2/2     Running   0          1m
    ```

## Verification

To verify that the sidecar container is successfully reading logs from the shared volume:

1.  **Generate Traffic:**
    If feasible, we can generate traffic to the Nginx server (e.g., using `curl` if the IP is reachable or via `kubectl port-forward`).

2.  **Check Sidecar Logs:**
    We can inspect the logs of the sidecar container to see the output of the `cat` command:
    ```bash
    kubectl logs webserver -c sidecar-container
    ```
    This confirms that the sidecar has access to the logs written by the Nginx container.
