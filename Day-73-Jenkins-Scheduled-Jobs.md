# Day 73: Jenkins Scheduled Jobs

## Challenge Description
The goal is to create a Jenkins job named `copy-logs` that runs periodically every 5 minutes. The job's purpose is to copy Apache logs (`access_log` and `error_log`) from App Server 2 (`stapp02`) to the Storage Server (`ststor01`) at the location `/usr/src/devops`.

## Key Concepts
*   **Jenkins Build Triggers:** Configuring jobs to run automatically based on a schedule.
*   **Cron Syntax:** Using standard cron expressions (e.g., `*/5 * * * *`) to define the schedule.
*   **SSH/SCP Automation:** Using `sshpass` to automate file transfers and remote command execution requiring passwords.
*   **Sudo Privileges:** Handling permissions for reading logs and writing to restricted directories using `sudo -S`.

## Solution

1.  **Access Jenkins:**
    *   Logged in to the Jenkins UI (`admin` / `Adm!n321`).

2.  **Create the Job:**
    *   Created a new **Freestyle project** named `copy-logs`.

3.  **Configure Schedule:**
    *   In the **Build Triggers** section, checked **Build periodically**.
    *   Entered the schedule: `*/5 * * * *` (Every 5 minutes).

4.  **Configure Build Step:**
    *   Added an **Execute shell** build step.
    *   Used the following script to fetch logs from App Server 2 and push them to the Storage Server. Since direct file transfer between the two remote servers might be restricted or require key setup, we used the Jenkins server as an intermediary.

    ```bash
    # 1. Fetch logs from App Server 2 (stapp02)
    # Using 'sudo -S cat' to read restricted log files
    sshpass -p 'Am3ric@' ssh -o StrictHostKeyChecking=no steve@stapp02 "echo 'Am3ric@' | sudo -S cat /var/log/httpd/access_log" > access_log
    sshpass -p 'Am3ric@' ssh -o StrictHostKeyChecking=no steve@stapp02 "echo 'Am3ric@' | sudo -S cat /var/log/httpd/error_log" > error_log

    # 2. Transfer logs to Storage Server (ststor01) temp location
    sshpass -p 'Bl@kW' scp -o StrictHostKeyChecking=no access_log error_log natasha@ststor01:/tmp/

    # 3. Move logs to final destination /usr/src/devops/
    # Using 'sudo -S mv' because /usr/src/devops usually requires root permissions
    sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@ststor01 "echo 'Bl@kW' | sudo -S mv /tmp/access_log /tmp/error_log /usr/src/devops/"
    ```

5.  **Save:**
    *   Saved the job configuration.

## Verification

1.  **Manual Trigger:**
    *   Clicked **Build Now** to verify the script works immediately without waiting for the schedule.
    *   Verified the build status was **SUCCESS**.

2.  **Check Destination:**
    *   We can verify the files exist on the storage server (optional step for documentation, but good practice):
    *   `sshpass -p 'Bl@kW' ssh natasha@ststor01 "ls -l /usr/src/devops/"`

3.  **Schedule Verification:**
    *   The "Build periodically" setting ensures the job will continue to run every 5 minutes.
