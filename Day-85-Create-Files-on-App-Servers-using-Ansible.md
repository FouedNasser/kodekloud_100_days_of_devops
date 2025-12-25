# Day 85: Create Files on App Servers using Ansible

## Challenge Description
The Nautilus DevOps team is testing various Ansible modules on servers in Stratos DC. They're currently focusing on file creation on remote hosts using Ansible. 

We are tasked with:
1. Creating an inventory file `~/playbook/inventory` on the jump host including all app servers.
2. Creating a playbook `~/playbook/playbook.yml` to create a blank file `/opt/nfsshare.txt` on all app servers.
3. Setting the permissions of the file to `0655`.
4. Ensuring the user/group owner is:
    - `tony` on `stapp01`
    - `steve` on `stapp02`
    - `banner` on `stapp03`

## Key Concepts
- **Ansible File Module:** Used to manage files and file properties (state, permissions, ownership).
- **Jinja2 Templating:** Used to dynamically assign values (like usernames) based on the hostname.
- **Ansible Facts/Variables:** Using `inventory_hostname` to determine the current host context.
- **Privilege Escalation:** Using `become: yes` to create files in restricted directories like `/opt`.

## Solution

1.  **Create the Inventory File:**
    We create the inventory file defining the three application servers.

    ```ini
    # ~/playbook/inventory
    stapp01 ansible_host=172.16.238.10 ansible_ssh_pass=Ir0nM@n
    stapp02 ansible_host=172.16.238.11 ansible_ssh_pass=Am3ric@
    stapp03 ansible_host=172.16.238.12 ansible_ssh_pass=BigGr33n
    ```

2.  **Create the Playbook:**
    We create `~/playbook/playbook.yml`. To handle the different owners for different servers without creating three separate tasks, we can use a Jinja2 conditional statement for the `owner` and `group` parameters.

    ```yaml
    ---
    - name: Create NFS Share file with specific ownership
      hosts: all
      become: yes
      tasks:
        - name: Create /opt/nfsshare.txt
          file:
            path: /opt/nfsshare.txt
            state: touch
            mode: '0655'
            owner: "{{ 'tony' if inventory_hostname == 'stapp01' else 'steve' if inventory_hostname == 'stapp02' else 'banner' }}"
            group: "{{ 'tony' if inventory_hostname == 'stapp01' else 'steve' if inventory_hostname == 'stapp02' else 'banner' }}"
    ```

3.  **Execute the Playbook:**
    Run the playbook using the inventory file.

    ```bash
    ansible-playbook -i inventory playbook.yml
    ```

## Verification
1.  **Check File Existence and Permissions:**
    We can verify the file was created correctly on one of the servers (e.g., stapp01).

    ```bash
    ssh tony@stapp01 "ls -l /opt/nfsshare.txt"
    ```

    **Expected Output (stapp01):**
    ```text
    -rwxr-xr-x 1 tony tony 0 ... /opt/nfsshare.txt
    ```

    **Expected Output (stapp02):**
    ```text
    -rwxr-xr-x 1 steve steve 0 ... /opt/nfsshare.txt
    ```

    **Expected Output (stapp03):**
    ```text
    -rwxr-xr-x 1 banner banner 0 ... /opt/nfsshare.txt
    ```
