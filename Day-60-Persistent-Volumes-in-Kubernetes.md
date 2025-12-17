# Day 60: Persistent Volumes in Kubernetes

## Challenge Description
The Nautilus DevOps team requires a Kubernetes template to deploy a web application, specifically needing persistent storage for application code. The task is to create:
1.  A PersistentVolume (PV) named `pv-devops` with `storageClassName: manual`, `capacity: 4Gi`, `accessModes: ReadWriteOnce`, `hostPath: /mnt/security`.
2.  A PersistentVolumeClaim (PVC) named `pvc-devops` with `storageClassName: manual`, `request: 2Gi`, `accessModes: ReadWriteOnce`.
3.  A Pod named `pod-devops` with a container `container-devops` using `httpd:latest`, mounting the `pvc-devops` to the web server's document root (`/usr/local/apache2/htdocs/`).
4.  A NodePort Service named `web-devops` with `nodePort: 30008` to expose the web server.

## Key Concepts
-   **PersistentVolume (PV):** A piece of storage in the cluster that has been provisioned by an administrator. It is a resource in the cluster just like a node is a cluster resource. PVs are volume plugins like `hostPath`, `NFS`, and `iSCSI`.
-   **PersistentVolumeClaim (PVC):** A request for storage by a user. It is similar to a Pod. Pods consume Node resources and PVCs consume PV resources.
-   **StorageClass:** Provides a way for administrators to describe the "classes" of storage they offer. When a PVC requests a certain class, a PV belonging to that class is dynamically provisioned. Here, `manual` indicates explicit provisioning.
-   **`hostPath` Volume:** Mounts a file or directory from the host node's filesystem into a Pod. Used here for simplicity, but generally not suitable for production in multi-node clusters as it ties the Pod to a specific node.
-   **NodePort Service:** Exposes a Service on a static port on each Node's IP.

## Solution

1.  **Create the Manifest File**
    We created a YAML file named `day60-solution.yaml` encompassing the PersistentVolume, PersistentVolumeClaim, Pod, and Service definitions.

    ```yaml
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: pv-devops
    spec:
      storageClassName: manual
      capacity:
        storage: 4Gi
      accessModes:
        - ReadWriteOnce
      hostPath:
        path: /mnt/security
    ---
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: pvc-devops
    spec:
      storageClassName: manual
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 2Gi
    ---
    apiVersion: v1
    kind: Pod
    metadata:
      name: pod-devops
      labels:
        app: web-devops
    spec:
      volumes:
        - name: storage-volume
          persistentVolumeClaim:
            claimName: pvc-devops
      containers:
        - name: container-devops
          image: httpd:latest
          ports:
            - containerPort: 80
          volumeMounts:
            - mountPath: /usr/local/apache2/htdocs/
              name: storage-volume
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: web-devops
    spec:
      type: NodePort
      selector:
        app: web-devops
      ports:
        - port: 80
          targetPort: 80
          nodePort: 30008
    ```

2.  **Apply the Configuration**
    We applied this manifest to the Kubernetes cluster:
    ```bash
    kubectl apply -f day60-solution.yaml
    ```

## Verification

We performed the following checks to ensure all resources were correctly provisioned and configured:

1.  **Verify PV and PVC Status:**
    ```bash
    kubectl get pv pv-devops
    kubectl get pvc pvc-devops
    ```
    We confirmed that both the PV and PVC showed a `STATUS` of `Bound`, indicating successful binding.

2.  **Verify Pod Status:**
    ```bash
    kubectl get pod pod-devops
    ```
    We ensured that `pod-devops` was in a `Running` state.

3.  **Verify Service Status:**
    ```bash
    kubectl get svc web-devops
    ```
    We checked that `web-devops` Service was created, was of type `NodePort`, and exposed on port `30008`.

4.  **Access the Web Server:**
    We accessed the web server via a Node IP and the NodePort (e.g., `http://<Node-IP>:30008`) to ensure the Apache default page or any content placed on `/mnt/security` was served correctly.
