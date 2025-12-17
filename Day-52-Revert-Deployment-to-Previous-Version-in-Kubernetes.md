# Day 52: Revert Deployment to Previous Version in Kubernetes

## Challenge Description
Earlier today, the Nautilus DevOps team deployed a new release for an application. However, a customer has reported a bug related to this recent release. Consequently, the team aims to revert to the previous version. There exists a deployment named `nginx-deployment`; we need to initiate a rollback to the previous revision.

## Key Concepts
*   **Kubernetes Deployments:** Managing a set of identical pods.
*   **Rollback:** Reverting a deployment to a previous stable state.
*   **`kubectl rollout history`:** A command to view the history of deployments, showing each revision and its associated change.
*   **`kubectl rollout undo`:** A command used to revert a deployment to a previous revision. By default, it reverts to the immediately preceding revision.

## Solution
We need to connect to the `jump_host` and use `kubectl` to manage the Kubernetes deployment.

1.  **Access the `jump_host`:**
    We connect to the `jump_host` using the provided credentials.
    ```bash
    ssh thor@jump_host
    ```

2.  **Check Rollout History:**
    Once on the `jump_host`, we inspect the rollout history of the `nginx-deployment` to identify the revisions. This helps us understand the deployment's change log.
    ```bash
    kubectl rollout history deployment nginx-deployment
    ```

3.  **Undo Rollout:**
    We initiate a rollback of the `nginx-deployment` to the immediately previous version. This command tells Kubernetes to revert the deployment to its last stable state.
    ```bash
    kubectl rollout undo deployment nginx-deployment
    ```
    *If we needed to revert to a specific older revision (e.g., to skip the immediate previous one), we would use:*
    ```bash
    kubectl rollout undo deployment nginx-deployment --to-revision=<REVISION_NUMBER>
    ```

## Verification
After performing the undo, we can check the deployment status and its history again to confirm the rollback was successful.

1.  **Check Deployment Status:**
    We verify the current status of the `nginx-deployment` to see if the changes have been applied.
    ```bash
    kubectl get deployment nginx-deployment
    ```

2.  **Review Updated Rollout History:**
    We review the rollout history again to confirm that a new revision representing the rollback has been created and that the deployment is now running the desired previous version.
    ```bash
    kubectl rollout history deployment nginx-deployment
    ```
