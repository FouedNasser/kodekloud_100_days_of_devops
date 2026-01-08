# Day 100: Create and Configure Alarm Using CloudWatch Using Terraform

## Challenge Description

The Nautilus DevOps team has been tasked with setting up an EC2 instance for their application. To ensure the application performs optimally, we also need to create a CloudWatch alarm to monitor the instance's CPU utilization. The alarm should trigger if the CPU utilization exceeds 90% for one consecutive 5-minute period. To send notifications, use the SNS topic named `xfusion-sns-topic`, which is already created.

**Requirements:**

1.  **Launch EC2 Instance:** Create an EC2 instance named `xfusion-ec2` using any appropriate Ubuntu AMI (e.g., `ami-0c02fb55956c7d316`).
2.  **Create CloudWatch Alarm:** Create a CloudWatch alarm named `xfusion-alarm` with the following specifications:
    *   Statistic: Average
    *   Metric: CPU Utilization
    *   Threshold: >= 90% for 1 consecutive 5-minute period
    *   Alarm Actions: Send a notification to the `xfusion-sns-topic` SNS topic.
3.  **Update `main.tf`:** Create the EC2 Instance and CloudWatch Alarm in the `main.tf` file.
4.  **Create `outputs.tf`:** Output `KKE_instance_name` and `KKE_alarm_name`.

## Key Concepts

*   **Terraform Resource `aws_instance`**: Used to provision EC2 instances.
*   **Terraform Resource `aws_cloudwatch_metric_alarm`**: Used to create CloudWatch alarms for monitoring metrics.
*   **Terraform Data Source `aws_sns_topic`**: Used to fetch information about an existing SNS topic.
*   **CloudWatch Metrics**: Standard metrics provided by AWS services (like `CPUUtilization` for EC2).

## Solution

We utilized Terraform to define the infrastructure.

### 1. `main.tf`

We defined the provider, the data source for the SNS topic, the EC2 instance, and the CloudWatch alarm.

```hcl
provider "aws" {
  region = "us-east-1"
}

# Data source to fetch the existing SNS topic ARN
data "aws_sns_topic" "xfusion_sns" {
  name = "xfusion-sns-topic"
}

# Resource for the EC2 Instance
resource "aws_instance" "xfusion_ec2" {
  ami           = "ami-0c02fb55956c7d316"
  instance_type = "t2.micro"

  tags = {
    Name = "xfusion-ec2"
  }
}

# Resource for the CloudWatch Metric Alarm
resource "aws_cloudwatch_metric_alarm" "xfusion_alarm" {
  alarm_name          = "xfusion-alarm"
  comparison_operator = "GreaterThanOrEqualToThreshold"
  evaluation_periods  = "1"
  metric_name         = "CPUUtilization"
  namespace           = "AWS/EC2"
  period              = "300" # 5 minutes
  statistic           = "Average"
  threshold           = "90"
  alarm_description   = "This metric monitors ec2 cpu utilization"
  
  dimensions = {
    InstanceId = aws_instance.xfusion_ec2.id
  }

  alarm_actions = [data.aws_sns_topic.xfusion_sns.arn]
}
```

### 2. `outputs.tf`

We defined the required outputs.

```hcl
output "KKE_instance_name" {
  value = aws_instance.xfusion_ec2.tags["Name"]
}

output "KKE_alarm_name" {
  value = aws_cloudwatch_metric_alarm.xfusion_alarm.alarm_name
}
```

## Verification

1.  **Initialize Terraform:**
    ```bash
    terraform init
    ```

2.  **Validate the configuration:**
    ```bash
    terraform validate
    ```

3.  **Plan the deployment:**
    ```bash
    terraform plan
    ```

4.  **Apply the changes:**
    ```bash
    terraform apply
    ```

5.  **Verify in AWS Console:**
    *   Go to the EC2 console and verify that `xfusion-ec2` is running.
    *   Go to the CloudWatch console > Alarms and verify that `xfusion-alarm` is created and correctly linked to the EC2 instance and SNS topic.
