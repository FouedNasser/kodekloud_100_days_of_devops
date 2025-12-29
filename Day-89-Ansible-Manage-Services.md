# Day 89: Ansible Manage Services

## Challenge Description
Developers are looking for dependencies to be installed and run on Nautilus app servers in Stratos DC. They have shared some requirements with the DevOps team. Because we are now managing packages installation and services management using Ansible, some playbooks need to be created and tested. As per details mentioned below please complete the task:

a. On jump host create an Ansible playbook `/home/thor/ansible/playbook.yml` and configure it to install `vsftpd` on all app servers.

b. After installation make sure to start and enable `vsftpd` service on all app servers.

c. The inventory `/home/thor/ansible/inventory` is already there on jump host.

d. Make sure user `thor` should be able to run the playbook on jump host.

## Key Concepts
*   **Ansible Playbook:** A configuration management script written in YAML that defines tasks to be executed on remote hosts.
*   **yum Module:** An Ansible module used to manage packages using the `yum` package manager (common on RHEL/CentOS systems).
*   **service Module:** An Ansible module used to manage system services, allowing us to start, stop, restart, enable, or disable them.
*   **Privilege Escalation (become):** Allows the playbook to execute tasks with administrative (root) privileges on the managed nodes.

## Solution
1.  We log in to the **Jump Server** as the `thor` user.
2.  We navigate to the Ansible configuration directory:
    ```bash
    cd /home/thor/ansible
    ```
3.  We create the `playbook.yml` file with the following content to install, start, and enable the `vsftpd` service:
    ```yaml
    - name: Manage vsftpd Service on App Servers
      hosts: all
      become: yes
      tasks:
        - name: Install vsftpd package
          yum:
            name: vsftpd
            state: present

        - name: Start and enable vsftpd service
          service:
            name: vsftpd
            state: started
            enabled: yes
    ```
4.  We execute the playbook using the provided inventory:
    ```bash
    ansible-playbook -i inventory playbook.yml
    ```

## Verification
To verify that the service is installed and running on the application servers, we can run the following commands:

*   **App Server 1:**
    ```bash
    ssh tony@stapp01 "rpm -q vsftpd && systemctl status vsftpd"
    ```
*   **App Server 2:**
    ```bash
    ssh steve@stapp02 "rpm -q vsftpd && systemctl status vsftpd"
    ```
*   **App Server 3:**
    ```bash
    ssh banner@stapp03 "rpm -q vsftpd && systemctl status vsftpd"
    ```
