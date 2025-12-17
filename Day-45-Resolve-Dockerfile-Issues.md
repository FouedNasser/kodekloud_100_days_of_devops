# Day 45: Resolve Dockerfile Issues

## Challenge Description:
The Nautilus DevOps team is working to create new images as per requirements shared by the development team. One of the team members is working to create a Dockerfile on App Server 1 in Stratos DC. While working on it, she ran into issues in which the docker build is failing and displaying errors. We need to look into the issue and fix it to build an image as per details mentioned below:
a. The Dockerfile is placed on App Server 1 under /opt/docker directory.
b. We need to fix the issues with this file and make sure it is able to build the image.
c. We should not change the base image, any other valid configuration within the Dockerfile, or any of the data being used — for example, index.html.

## Key Concepts:
*   **Dockerfile:** A text file that contains all the commands a user could call on the command line to assemble an image.
*   **`RUN` instruction:** Executes commands in a new layer on top of the current image and commits the results.
*   **`COPY` instruction:** Copies new files or directories from `<src>` and adds them to the filesystem of the container at the path `<dest>`.
*   **Build Context:** The set of files at a specified `PATH` or `URL` that are sent to the Docker daemon during a build.

## Solution:
The original `Dockerfile` was failing because it used `RUN cp` commands to copy `server.crt` and `server.key` from the `certs` directory. However, `RUN` commands execute *inside* the container, and at that point, the `certs` directory from the build context (host) was not yet copied into the container's filesystem.

To resolve this, we need to use the `COPY` instruction to bring the `certs` directory and its contents from the build context into the container. As instructed, we add `COPY . .` to copy all files and directories from the build context into the working directory of the image. This makes the `certs` directory accessible for the subsequent `RUN cp` commands.

The original `Dockerfile` was:

```dockerfile
FROM httpd:2.4.43

RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf

RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' conf/httpd.conf

RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' conf/httpd.conf

RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' conf/httpd.conf

RUN cp certs/server.crt /usr/local/apache2/conf/server.crt

RUN cp certs/server.key /usr/local/apache2/conf/server.key
```

The corrected `Dockerfile` is:

```dockerfile
FROM httpd:2.4.43

COPY . .

RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf

RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' conf/httpd.conf

RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' conf/httpd.conf

RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' conf/httpd.conf

RUN cp certs/server.crt /usr/local/apache2/conf/server.crt

RUN cp certs/server.key /usr/local/apache2/conf/server.key
```

To build the image, we would navigate to the `/opt/docker` directory on `stapp01` and run the following command:

```bash
docker build -t my-httpd .
```

## Verification:
After applying the fix, we would verify the solution by building the Docker image using the command `docker build -t my-httpd .` from within the `/opt/docker` directory. A successful build without errors would indicate that the issue is resolved. We would then be able to run the container and confirm the configuration changes (e.g., port 8080 and SSL).
