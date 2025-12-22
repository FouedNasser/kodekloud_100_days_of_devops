# Day 84: Copy Data to App Servers using Ansible

## Challenge Description

The task for Day 84 was to use Ansible to copy a file from the jump host to all application servers in the Stratos Datacenter. This involved creating a comprehensive inventory file that included all app servers and writing a playbook to handle the file transfer, including ensuring the destination directory exists.

## Key Concepts

-   **Ansible Inventory Groups:** We can group hosts in an inventory file (e.g., `[appservers]`) to target multiple servers with a single command or playbook `hosts` directive. This simplifies managing different environments or server roles.
-   **Ansible `file` Module:** This module is used to manage file system objects like files, directories, and symbolic links. We used it with `state: directory` to ensure the destination directory `/opt/data` existed before attempting to copy the file. This makes the playbook more robust.
-   **Ansible `copy` Module:** This module copies a file from the local Ansible control node (the jump host in this case) to a remote location on the managed nodes.
-   **Permissions (`mode`):** Explicitly setting file and directory permissions is a security best practice. We used `mode: '0755'` for the directory to make it readable and traversable by others, and `mode: '0644'` for the file, making it readable by others but only writable by the owner.
-   **`become: yes`:** This directive is used to execute tasks with privilege escalation (e.g., `sudo`), which is necessary for writing to system directories like `/opt`.

## Solution

1.  **Log into the Jump Host**

    We first connected to the jump host as the `thor` user to set up our Ansible files.

    ```bash
    ssh thor@jump_host
    ```

2.  **Create the Inventory File**

    We created the inventory file at `/home/thor/ansible/inventory`. We defined an `[appservers]` group and added all three application servers (`stapp01`, `stapp02`, `stapp03`) with their respective connection and authentication details.

    ```ini
    [appservers]
    stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass='Ir0nM@n' ansible_ssh_common_args='-o StrictHostKeyChecking=no'
    stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass='Am3ric@' ansible_ssh_common_args='-o StrictHostKeyChecking=no'
    stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass='BigGr33n' ansible_ssh_common_args='-o StrictHostKeyChecking=no'
    ```

3.  **Create the Ansible Playbook**

    Next, we created the playbook `/home/thor/ansible/playbook.yml`. It targets the `appservers` group and uses privilege escalation (`become: yes`). The playbook has two tasks: first, to create the `/opt/data` directory, and second, to copy the `index.html` file into it.

    ```yaml
    - name: Copy index.html to App Servers
      hosts: appservers
      become: yes
      tasks:
        - name: Ensure /opt/data directory exists
          ansible.builtin.file:
            path: /opt/data
            state: directory
            mode: '0755'
            owner: root
            group: root

        - name: Copy index.html to /opt/data
          ansible.builtin.copy:
            src: /usr/src/data/index.html
            dest: /opt/data/index.html
            mode: '0644'
    ```

4.  **Execute the Playbook**

    With the inventory and playbook ready, we executed it from the `/home/thor/ansible` directory.

    ```bash
    ansible-playbook -i inventory playbook.yml
    ```

## Verification

To confirm success, we logged into each application server and checked for the existence of the `/opt/data/index.html` file.

```bash
ssh tony@stapp01 "ls /opt/data/index.html"
ssh steve@stapp02 "ls /opt/data/index.html"
ssh banner@stapp03 "ls /opt/data/index.html"
```

Each command successfully returned the file path, confirming the playbook worked as expected across all servers.
