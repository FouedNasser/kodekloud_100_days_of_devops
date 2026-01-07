# Day 99: Attach IAM Policy for DynamoDB Access Using Terraform

## Challenge Description
The goal of this challenge is to use Terraform to create a secure DynamoDB table and enforce fine-grained access control using AWS IAM. We need to:
1.  Create a DynamoDB table named `datacenter-table`.
2.  Create an IAM role named `datacenter-role`.
3.  Create an IAM policy named `datacenter-readonly-policy` that grants read-only access (`GetItem`, `Scan`, `Query`) strictly to the created DynamoDB table.
4.  Attach the policy to the role.

## Key Concepts
*   **Terraform Resources:** `aws_dynamodb_table`, `aws_iam_role`, `aws_iam_policy`, `aws_iam_role_policy_attachment`.
*   **AWS IAM:** Managing permissions via Roles and Policies.
*   **Principle of Least Privilege:** Granting only necessary permissions (Read-Only) to specific resources.
*   **Infrastructure as Code (IaC):** defining infrastructure in `main.tf`, variables in `variables.tf`, and outputs in `outputs.tf`.

## Solution

We created the following Terraform configuration files.

### 1. `variables.tf`
Defines the variables for resource names.

```hcl
variable "KKE_TABLE_NAME" {
  description = "name of the DynamoDB table"
  type        = string
}

variable "KKE_ROLE_NAME" {
  description = "name of the IAM role"
  type        = string
}

variable "KKE_POLICY_NAME" {
  description = "name of the IAM policy"
  type        = string
}
```

### 2. `main.tf`
Defines the DynamoDB table, IAM Role, IAM Policy, and the attachment.

```hcl
resource "aws_dynamodb_table" "datacenter_table" {
  name           = var.KKE_TABLE_NAME
  billing_mode   = "PAY_PER_REQUEST"
  hash_key       = "LockID"

  attribute {
    name = "LockID"
    type = "S"
  }
}

resource "aws_iam_role" "datacenter_role" {
  name = var.KKE_ROLE_NAME

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = "sts:AssumeRole"
        Effect = "Allow"
        Principal = {
          Service = "ec2.amazonaws.com"
        }
      },
    ]
  })
}

resource "aws_iam_policy" "datacenter_readonly_policy" {
  name        = var.KKE_POLICY_NAME
  description = "Read-only access to DynamoDB table"

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = [
          "dynamodb:GetItem",
          "dynamodb:Scan",
          "dynamodb:Query"
        ]
        Effect   = "Allow"
        Resource = aws_dynamodb_table.datacenter_table.arn
      },
    ]
  })
}

resource "aws_iam_role_policy_attachment" "attach_readonly" {
  role       = aws_iam_role.datacenter_role.name
  policy_arn = aws_iam_policy.datacenter_readonly_policy.arn
}
```

### 3. `outputs.tf`
Outputs the names of the created resources.

```hcl
output "kke_dynamodb_table" {
  value = aws_dynamodb_table.datacenter_table.name
}

output "kke_iam_role_name" {
  value = aws_iam_role.datacenter_role.name
}

output "kke_iam_policy_name" {
  value = aws_iam_policy.datacenter_readonly_policy.name
}
```

### 4. `terraform.tfvars`
Sets the specific values for the variables.

```hcl
KKE_TABLE_NAME  = "datacenter-table"
KKE_ROLE_NAME   = "datacenter-role"
KKE_POLICY_NAME = "datacenter-readonly-policy"
```

## Verification

1.  **Initialize Terraform:**
    ```bash
    terraform init
    ```

2.  **Plan the deployment:**
    ```bash
    terraform plan
    ```
    Verify that 4 resources are to be added.

3.  **Apply the configuration:**
    ```bash
    terraform apply --auto-approve
    ```

4.  **Verify resources in AWS:**
    *   **DynamoDB:** Check for `datacenter-table`.
    *   **IAM Role:** Check for `datacenter-role`.
    *   **IAM Policy:** Check for `datacenter-readonly-policy` and ensure it has the correct JSON permission block allowing `GetItem`, `Scan`, and `Query` only on the `datacenter-table` ARN.
    *   **Attachment:** Verify the policy is attached to the role.
