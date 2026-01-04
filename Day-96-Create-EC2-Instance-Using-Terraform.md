# Day 96: Create EC2 Instance Using Terraform

## Challenge Description
The Nautilus DevOps team is strategizing the migration of a portion of their infrastructure to the AWS cloud. For this task, we need to create an EC2 instance using Terraform with the following requirements:
*   The EC2 instance must use the value `datacenter-ec2` as its Name tag.
*   Use the Amazon Linux AMI `ami-0c101f26f147fa7fd`.
*   The Instance type must be `t2.micro`.
*   Create a new RSA key named `datacenter-kp`.
*   Attach the `default` security group.
*   The Terraform working directory is `/home/bob/terraform`.

## Key Concepts
*   **Terraform Resources:** Defining infrastructure components like `aws_instance` and `aws_key_pair`.
*   **Terraform Data Sources:** Fetching information about existing resources (like the default security group) using `data`.
*   **TLS Provider:** Generating cryptographic keys (RSA) within Terraform using `tls_private_key`.
*   **Local File Resource:** Saving generated content (like the private key) to a local file.

## Solution
1.  We navigated to the Terraform working directory:
    ```bash
    cd /home/bob/terraform
    ```

2.  We created the `main.tf` file to define the required resources. We used the `tls_private_key` resource to generate the RSA key, `aws_key_pair` to import it to AWS, `data` to find the default security group, and `aws_instance` to launch the server.

    ```hcl
    # Generate a new RSA private key
    resource "tls_private_key" "pk" {
      algorithm = "RSA"
      rsa_bits  = 4096
    }

    # Create the Key Pair in AWS using the generated public key
    resource "aws_key_pair" "kp" {
      key_name   = "datacenter-kp"
      public_key = tls_private_key.pk.public_key_openssh
    }

    # Save the private key to a local file
    resource "local_file" "ssh_key" {
      filename        = "${path.module}/datacenter-kp.pem"
      content         = tls_private_key.pk.private_key_pem
      file_permission = "0400"
    }

    # Get the default Security Group
    data "aws_security_group" "default" {
      name = "default"
    }

    # Create the EC2 Instance
    resource "aws_instance" "web" {
      ami                    = "ami-0c101f26f147fa7fd"
      instance_type          = "t2.micro"
      key_name               = aws_key_pair.kp.key_name
      vpc_security_group_ids = [data.aws_security_group.default.id]

      tags = {
        Name = "datacenter-ec2"
      }
    }
    ```

3.  We initialized Terraform to configure the backend and install plugins:
    ```bash
    terraform init
    ```

4.  We generated an execution plan to verify what resources would be created:
    ```bash
    terraform plan
    ```

5.  We applied the configuration to provision the infrastructure:
    ```bash
    terraform apply
    ```

## Verification
1.  We verified the instance creation by listing the instances using the AWS CLI:
    ```bash
    aws ec2 describe-instances --filters "Name=tag:Name,Values=datacenter-ec2"
    ```
2.  We verified that the key pair `datacenter-kp` was created:
    ```bash
    aws ec2 describe-key-pairs --key-names datacenter-kp
    ```
3.  We checked for the local private key file:
    ```bash
    ls -l datacenter-kp.pem
    ```
