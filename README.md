# AWS Alerts

## Introduction

AWS Alerts is a monitoring and alerting solution for AWS cloud, designed to monitor resources across 30+ AWS services and send alerts related to failures, errors, warnings, and terminations to Slack.

By default, all AWS services are disabled, and you must explicitly enable the ones you wish to monitor.

**Why was this solution developed?**

Infrastructure-level alerts can sometimes be missed or be inefficient to manage manually. AWS CloudWatch/AWS EventBridge events, AWS CloudWatch alarms, and AWS service-specific event subscriptions can handle these alerts, but setting up all the necessary resources manually can be time-consuming and complex.

### Examples of AWS Alerts on Slack

- **AWS CloudWatch/AWS EventBridge Event**  
  ![image](https://github.com/user-attachments/assets/37ca3523-3f4d-430e-9f70-824def719fff)


- **AWS CloudWatch Alarm**  
  ![image](https://github.com/user-attachments/assets/13058285-4ece-4a89-a47a-db5504d6d9f0)

## Supported IaC (Infrastructure as Code) Tools

- Terraform
- AWS CloudFormation

## Supported AWS Services

You can enable alerts for the following 30+ AWS services:

- AWS Batch
- AWS CodeBuild (CB)
- AWS CodeDeploy (CD)
- AWS CodePipeline (CP)
- AWS Config
- AWS Data Lifecycle Manager (DLM)
- AWS Database Migration Service (DMS)
- AWS DataSync (DS)
- AWS Elastic Block Store (EBS)
- AWS Elastic Compute Cloud (EC2) Auto Scaling
- AWS Elastic Compute Cloud (EC2)
- AWS Elastic Container Service (ECS)
- AWS Elastic Map Reduce (EMR)
- AWS Elemental
- AWS GameLift (GL)
- AWS Glue
- AWS Health
- AWS Internet of Things (IoT)
- AWS Key Management Service (KMS)
- AWS Lambda
- AWS Macie
- AWS OpsWorks
- AWS Redshift
- AWS Relational Database Service (RDS)
- AWS SageMaker
- AWS Server Migration Service (SMS)
- AWS Signer
- AWS Step Functions (SF)
- AWS Systems Manager (SSM)
- AWS Transcribe
- AWS Trusted Advisor (TA)

## Components Used

- **Terraform template** for resource deployment (alternative to AWS CloudFormation).
- **AWS CloudFormation template** for stack deployment.
- **Python script** (Python 3.12) for sending formatted AWS Alerts to Slack.
- **Boto3** for AWS resources access in Python.
- **AWS Lambda function** for executing the Python script.
- **AWS IAM role** with least privileges used by the Lambda function.
- **AWS Lambda Invoke Permission** for AWS SNS topic.
- **AWS CloudWatch/AWS EventBridge events** for alerts.
- **AWS CloudWatch alarms** for Lambda function failures.
- **AWS RDS and DMS event subscriptions** for RDS and DMS alerts.
- **AWS SNS topic** for receiving and sending alerts to Slack.
- **AWS SNS topic policy** for publishing messages.

## Prerequisites

- Install the following tools on your system:
  - Git
  - AWS CLI
  - Terraform
  - Python 3.12 with pip

- Create a Slack Webhook URL for the channel to receive alerts (using general or app incoming webhook).
- Create a parameter on AWS SSM Parameter Store with the name of your choice and set its value to the Slack Webhook URL.

## Deployment and Usage Notes

### Using Terraform

1. Fork this repository from the master branch.
2. Use the `terraform-usage-example.tf` file to create your `main.tf` file as needed.
3. Set the value for `slack_webhook_url_aws_ssm_parameter_name` to the name of the AWS SSM Parameter from the Parameter Store with the Slack Webhook URL.
4. For each AWS service you want to enable alerts for, set the corresponding `enable_...` variable to `true` (e.g., `enable_rds_failure_warning_alerts`).
5. If `enable_lambda_failure_alerts` is set to `true`, specify the Lambda functions to monitor using `lambda_function_names`; otherwise, it will fetch all Lambda function names.
6. Configure AWS CLI, then run:
    ```sh
    terraform init
    terraform apply
    ```
7. Review the Terraform change plan and confirm to create the resources.
8. Wait for Terraform to finish creating all resources.

### Using AWS CloudFormation

1. Fork this repository from the master branch.
2. Install the Python libraries:
    ```sh
    pip3 install -r ./function/requirements.txt -t ./function --no-cache-dir --upgrade
    ```
3. Compress the contents of the `function` directory into a `.zip` file and upload it to an AWS S3 bucket.
4. Log in to the AWS console with IAM credentials that have permissions to create resources via AWS CloudFormation.
5. Go to AWS CloudFormation and click on "Create Stack," then select "With new resources (standard)."
6. Under "Choose a template," either upload the `aws_alerts_cft.yaml` file or provide its S3 URL.
7. Enter a suitable value for Stack Name.
8. Set the value for `SlackWebhookURLAWSSSMParameterName` to the AWS SSM Parameter Name with the Slack Webhook URL.
9. Set values for `AWSAlertsLambdaCodeS3Bucket` and `AWSAlertsLambdaCodeS3ObjectKey` (e.g., my-bucket and lambda/code/aws_alerts.zip).
10. Select "YES" for any AWS service alerts you want to enable.
11. Enter any additional tags or configurations as needed.
12. Acknowledge that AWS CloudFormation might create IAM resources with custom names, then click "Create."
13. Wait for the stack to reach `CREATE_COMPLETE` status.

**Note:** You can subscribe other endpoints to the AWS SNS topic created for alerts if needed.

## Troubleshooting Notes

- If no notifications are received or AWS CloudWatch alarms aren't working, try resubscribing to the AWS SNS topic or updating the notification action in AWS CloudWatch alarms.
- For other issues, please create an issue on this GitHub repository for assistance or resolution.

**Contributions, improvements, and suggestions are highly appreciated!**
