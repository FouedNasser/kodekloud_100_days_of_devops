# Day 88: Ansible Blockinfile Module

## Challenge Description
The Nautilus DevOps team wants to install and set up a simple httpd web server on all app servers in Stratos DC. Additionally, they want to deploy a sample web page using Ansible's `blockinfile` module. We need to create a playbook to:
1. Install `httpd` on all app servers.
2. Ensure the `httpd` service is up and running.
3. Add specific content to `/var/www/html/index.html` using the `blockinfile` module.
4. Set ownership to `apache` and permissions to `0644`.

## Key Concepts
*   **Ansible Playbooks:** Defining configurations in YAML.
*   **Package Management:** Using the `yum` module to install software.
*   **Service Management:** Using the `service` module to manage daemons.
*   **File Manipulation:** Using the `blockinfile` module to insert/update text blocks in files with markers.

## Solution

1.  Navigate to the ansible directory on the jump host:
    ```bash
    cd /home/thor/ansible
    ```

2.  Create the playbook file `playbook.yml`:
    ```yaml
    ---
    - name: Setup Httpd Web Server
      hosts: all
      become: yes
      tasks:
        - name: Install httpd package
          yum:
            name: httpd
            state: present

        - name: Start and enable httpd service
          service:
            name: httpd
            state: started
            enabled: yes

        - name: Add content to index.html using blockinfile
          blockinfile:
            path: /var/www/html/index.html
            create: yes
            owner: apache
            group: apache
            mode: '0644'
            block: |
              Welcome to XfusionCorp!

              This is  Nautilus sample file, created using Ansible!

              Please do not modify this file manually!
    ```

3.  Run the playbook:
    ```bash
    ansible-playbook -i inventory playbook.yml
    ```

## Verification

To verify the changes, we can SSH into one of the app servers (e.g., `stapp01`) and check the file content:

```bash
ssh tony@stapp01
cat /var/www/html/index.html
```

The output should show the content wrapped in Ansible default markers:

```text
# BEGIN ANSIBLE MANAGED BLOCK
Welcome to XfusionCorp!

This is  Nautilus sample file, created using Ansible!

Please do not modify this file manually!
# END ANSIBLE MANAGED BLOCK
```
