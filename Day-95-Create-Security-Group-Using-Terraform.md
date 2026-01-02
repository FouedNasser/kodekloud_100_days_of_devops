# Day 95: Create Security Group Using Terraform

## Challenge Description
The Nautilus DevOps team is continuing their AWS migration by setting up network security. The task is to create a security group named `datacenter-sg` in the `us-east-1` region using Terraform, with specific inbound rules for web and administrative access.

## Key Concepts
*   **Security Group:** A virtual firewall for your EC2 instances to control inbound and outbound traffic.
*   **Ingress Rules:** Rules that define allowed incoming traffic (e.g., HTTP on port 80, SSH on port 22).
*   **Egress Rules:** Rules that define allowed outgoing traffic. By default, security groups allow all outbound traffic.
*   **Stateful Filtering:** Security groups are stateful; if you send a request from your instance, the response traffic for that request is allowed to flow in regardless of inbound security group rules.

## Solution
1.  We navigate to the Terraform directory:
    ```bash
    cd /home/bob/terraform
    ```

2.  We create the `main.tf` file with the security group resource and the required rules:

    ```hcl
    resource "aws_security_group" "datacenter-sg" {
      name        = "datacenter-sg"
      description = "Security group for Nautilus App Servers"

      # Inbound HTTP rule
      ingress {
        from_port   = 80
        to_port     = 80
        protocol    = "tcp"
        cidr_blocks = ["0.0.0.0/0"]
      }

      # Inbound SSH rule
      ingress {
        from_port   = 22
        to_port     = 22
        protocol    = "tcp"
        cidr_blocks = ["0.0.0.0/0"]
      }

      # Outbound rule to allow all traffic
      egress {
        from_port   = 0
        to_port     = 0
        protocol    = "-1"
        cidr_blocks = ["0.0.0.0/0"]
      }

      tags = {
        Name = "datacenter-sg"
      }
    }
    ```

3.  We initialize the Terraform working directory:
    ```bash
    terraform init
    ```

4.  We verify the plan:
    ```bash
    terraform plan
    ```

5.  We apply the configuration to create the security group:
    ```bash
    terraform apply
    ```

## Verification
1.  We check the created security group in Terraform state:
    ```bash
    terraform show
    ```
2.  We can also verify via the AWS CLI (using the correct endpoint):
    ```bash
    aws --endpoint-url=http://aws:4566 ec2 describe-security-groups --group-names datacenter-sg
    ```
