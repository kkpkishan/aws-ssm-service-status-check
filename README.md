# Service Status Lambda CloudFormation Template

This repository contains a CloudFormation template to deploy a Lambda function that checks the status of specified services (e.g., `nginx`, `mysql`) on EC2 instances managed by AWS Systems Manager (SSM). The results are sent as notifications to an SNS topic.

## Features
- Retrieves EC2 instances connected to SSM.
- Executes `systemctl` commands to check the status of services.
- Gathers instance metadata (e.g., name, region, IP addresses).
- Sends status reports to an SNS topic.
- Supports concurrent execution using threading.

## Prerequisites
1. **SNS Topic**: Create an SNS topic for receiving notifications.
2. **IAM Role**: Ensure the Lambda execution role has permissions for:
   - `ssm:DescribeInstanceInformation`
   - `ssm:SendCommand`
   - `ssm:GetCommandInvocation`
   - `ec2:DescribeInstances`
   - `sns:Publish`
   - `logs:CreateLogGroup`, `logs:CreateLogStream`, `logs:PutLogEvents`

## Parameters
The CloudFormation template accepts the following parameters:

- **SNSArn**: ARN of the SNS topic where notifications will be sent.
- **ServiceNames**: Comma-separated list of services to check (e.g., `nginx,mysql`).

## Deployment
1. Clone this repository:
   ```bash
   git clone <repository-url>
   cd <repository-directory>
   ```
2. Deploy the CloudFormation stack:
   ```bash
   aws cloudformation create-stack \
       --stack-name ServiceStatusCheckStack \
       --template-body file://ssm_sns_cf_template.yaml \
       --parameters ParameterKey=SNSArn,ParameterValue=<Your-SNS-Topic-ARN> \
                    ParameterKey=ServiceNames,ParameterValue="nginx,mysql" \
       --capabilities CAPABILITY_IAM
   ```

## Outputs
- **LambdaFunctionName**: Name of the deployed Lambda function.
- **SNSTopicArn**: ARN of the SNS topic for notifications.

## Lambda Function
The Lambda function:
- Fetches all EC2 instances connected to SSM.
- Checks the status of specified services on Linux instances.
- Sends results as a detailed report to the SNS topic.

## Example SNS Notification
```plaintext
Instance Name: WebServer1
Instance ID: i-0123456789abcdef0
Region: ap-south-1
Private IP: 10.0.0.1
Public IP: 52.0.0.1
Service nginx: active
Service mysql: inactive
```
