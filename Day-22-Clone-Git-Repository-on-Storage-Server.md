# Day 22: Clone Git Repository on Storage Server

## Challenge Description
The DevOps team established a new Git repository last week, which remains unused at present. However, the Nautilus application development team now requires a copy of this repository on the Storage Server in the Stratos DC.

The requirements are:

1) The repository to be cloned is located at `/opt/beta.git`.

2) Clone this Git repository to the `/usr/src/kodekloudrepos` directory. Perform this task using the `natasha` user, and ensure that no modifications are made to the repository or existing directories, such as changing permissions or making unauthorized alterations.

## Key Concepts
*   **Git Clone:** Creating a copy of an existing repository.
*   **Bare Repository:** The source `/opt/beta.git` is likely a bare repository (ending in .git), which contains version history but no working tree.
*   **File Permissions:** Ensuring the target directory exists and is writable.

## Solution

### 1. Connect to Storage Server
Login to the storage server (`ststor01`) as the `natasha` user.

```bash
ssh natasha@ststor01
```
*Password: `Bl@kW`*

### 2. Prepare Destination Directory
Create the destination directory. Since `/usr/src` is a system directory, we use `sudo` to create the subdirectory. We then ensure `natasha` can write to it.

```bash
sudo mkdir -p /usr/src/kodekloudrepos
sudo chmod 777 /usr/src/kodekloudrepos
```
*Note: We grant write permissions so the `natasha` user can clone into this directory without changing ownership of the parent `/usr/src`.*

### 3. Clone the Repository
Clone the repository from the local path `/opt/beta.git` into the target directory.

```bash
git clone /opt/beta.git /usr/src/kodekloudrepos
```

## Verification
Verify that the repository has been cloned successfully:

```bash
ls -l /usr/src/kodekloudrepos
```
You should see the repository contents (e.g., `.git` folder and project files).
