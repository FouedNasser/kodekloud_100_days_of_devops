# Day 8: Install Ansible on Jump Host

## Challenge Description

During the weekly meeting, the Nautilus DevOps team discussed about the automation and configuration management solutions that they want to implement. While considering several options, the team has decided to go with Ansible for now due to its simple setup and minimal pre-requisites. The team wanted to start testing using Ansible, so they have decided to use jump host as an Ansible controller to test different kind of tasks on rest of the servers.

Install ansible version 4.9.0 on Jump host using pip3 only. Make sure Ansible binary is available globally on this system, i.e all users on this system are able to run Ansible commands.

## Key Concepts

*   **Ansible:** An open-source automation tool for IT automation, configuration management, and application deployment.
*   **Jump Host:** A server that acts as an intermediary or gateway to other servers, often used for security and network segmentation. In this challenge, it serves as the Ansible controller.
*   **pip3:** The package installer for Python 3, used to install Python packages like Ansible.
*   **Global Installation:** Installing a package in a location accessible by all users on a system, typically by using `sudo`.

## Solution

We need to install Ansible version 4.9.0 on the Jump Host (`jump_host`) using `pip3` and ensure it's available globally.

1.  **Uninstall any existing local Ansible installation:**
    If a previous version of Ansible was installed locally (e.g., in the user's home directory), we remove it to avoid conflicts and ensure a clean global installation.
    ```bash
    pip3 uninstall ansible ansible-core -y
    ```

3.  **Install/Update `pip3` (if required):**
    We ensure `pip3` is installed on the system. The environment typically uses CentOS, so `yum` is used.
    ```bash
    sudo yum install python3-pip -y
    ```
    (For Debian/Ubuntu systems, we would use `sudo apt install python3-pip -y`)

4.  **Install Ansible 4.9.0 Globally:**
    We use `sudo pip3 install` to install Ansible version 4.9.0. This places the package and its executable in system-wide locations, making it accessible to all users.
    ```bash
    sudo pip3 install ansible==4.9.0
    ```

## Verification

We verify the Ansible installation by checking its version and executable location.

1.  **Check Ansible version:**
    ```bash
    ansible --version
    ```
    The output should show `ansible [core 2.11.12]` (which corresponds to Ansible 4.9.0) and the `ansible python module location` should be a global path (e.g., `/usr/local/lib/python3.9/site-packages/ansible`), not within a user's home directory (`.local`).

    **Example of successful output:**
    ```
    ansible [core 2.11.12]
      config file = None
      configured module search path = ['/home/thor/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
      ansible python module location = /usr/local/lib/python3.9/site-packages/ansible
      ansible collection location = /home/thor/.ansible/collections:/usr/share/ansible/collections
      executable location = /usr/local/bin/ansible
      python version = 3.9.18 (main, Jan 24 2024, 00:00:00) [GCC 11.4.1 20231218 (Red Hat 11.4.1-3)]
      jinja version = 3.1.6
      libyaml = True
    ```

2.  **Verify global accessibility (optional):**
    To further confirm global availability, we can switch to the root user and try running the command.
    ```bash
    sudo su -
    ansible --version
    ```
    This should produce similar output, confirming that Ansible is accessible system-wide.
