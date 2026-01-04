# Day 97: Create IAM Policy Using Terraform

## Challenge Description
When establishing infrastructure on the AWS cloud, Identity and Access Management (IAM) is critical. The Nautilus DevOps team needs to configure IAM resources.

For this task, we need to create an IAM policy named `iampolicy_yousuf` in the `us-east-1` region using Terraform. This policy must allow read-only access to the EC2 console, specifically allowing users to view all instances, AMIs, and snapshots.

The Terraform working directory is `/home/bob/terraform`.

## Key Concepts
*   **IAM Policy:** A document (typically JSON) that defines permissions to allow or deny actions on AWS resources.
*   **Terraform `aws_iam_policy` Resource:** The Terraform resource type used to manage IAM policies.
*   **`jsonencode` Function:** A Terraform function used to encode a Map or Object into a JSON string, ensuring valid JSON syntax for the policy document.
*   **Principle of Least Privilege:** Granting only the permissions necessary to perform a task (in this case, read-only access).

## Solution
1.  We navigated to the working directory:
    ```bash
    cd /home/bob/terraform
    ```

2.  We created the `main.tf` file to define the IAM policy. We used the `aws_iam_policy` resource and the `jsonencode` function to define the policy document which grants `Describe*` permissions on EC2 resources.

    ```hcl
    resource "aws_iam_policy" "ec2_readonly_policy" {
      name        = "iampolicy_yousuf"
      path        = "/"
      description = "Read-only access to EC2 instances, AMIs, and snapshots"

      policy = jsonencode({
        Version = "2012-10-17"
        Statement = [
          {
            Action = [
              "ec2:DescribeInstances",
              "ec2:DescribeImages",
              "ec2:DescribeSnapshots",
              "ec2:DescribeTags"
            ]
            Effect   = "Allow"
            Resource = "*"
          },
        ]
      })
    }
    ```

3.  We initialized Terraform:
    ```bash
    terraform init
    ```

4.  We generated an execution plan:
    ```bash
    terraform plan
    ```

5.  We applied the configuration to create the policy:
    ```bash
    terraform apply
    ```

## Verification
1.  We verified the policy creation using the AWS CLI:
    ```bash
    aws iam list-policies --scope Local --query 'Policies[?PolicyName==`iampolicy_yousuf`]'
    ```
2.  We could also inspect the policy document itself to confirm the permissions:
    ```bash
    POLICY_ARN=$(aws iam list-policies --scope Local --query 'Policies[?PolicyName==`iampolicy_yousuf`].Arn' --output text)
    aws iam get-policy-version --policy-arn $POLICY_ARN --version-id v1
    ```
