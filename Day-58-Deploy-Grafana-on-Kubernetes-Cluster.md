# Day 58: Deploy Grafana on Kubernetes Cluster

## Challenge Description
The Nautilus DevOps team needs to set up Grafana on the Kubernetes cluster for analytics. The requirements are:
1.  **Deployment:** Create a Deployment named `grafana-deployment-datacenter` using a Grafana image.
2.  **Service:** Create a `NodePort` Service to expose the Grafana application on port `32000`.
3.  **Verification:** Ensure the Grafana login page is accessible.

## Key Concepts
- **Grafana:** An open-source platform for monitoring and observability.
- **Kubernetes Deployment:** Manages the deployment of the application (Grafana).
- **Kubernetes Service (NodePort):** Exposes the internal Pod port (default 3000 for Grafana) to an external port (32000) on the Kubernetes nodes.

## Solution

1.  **Create the Manifest File**
    We created a YAML file named `grafana.yaml` containing the Deployment and Service definitions:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: grafana-deployment-datacenter
      labels:
        app: grafana
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: grafana
      template:
        metadata:
          labels:
            app: grafana
        spec:
          containers:
          - name: grafana
            image: grafana/grafana:latest
            ports:
            - containerPort: 3000
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: grafana-service
    spec:
      type: NodePort
      selector:
        app: grafana
      ports:
      - port: 3000
        targetPort: 3000
        nodePort: 32000
    ```

2.  **Apply the Configuration**
    We applied the manifest to the Kubernetes cluster:
    ```bash
    kubectl apply -f grafana.yaml
    ```

3.  **Verify Resources**
    We verified that the deployment was running and the service was correctly configured:
    ```bash
    kubectl get deployments
    kubectl get svc
    ```

## Verification
To verify the deployment, we accessed the Grafana login page using the Node IP and the configured NodePort:
```bash
# Assuming we are checking from a browser or via curl
curl http://<Node-IP>:32000
```
We confirmed that the Grafana login page was successfully served.
