# Setting Up Lambda Functions with S3 Event Triggers

## Introduction

Lambda event triggers are extremely 
useful for automating serverless workflow, as they help trigger Lambda 
code/logic and have use cases from monitoring to processing online 
purchase orders and emailing receipts.

You've been tasked with notifying your
 on-call teams when a certain alarming action occurs within your 
application. This notification method has to be completely automated and
 should trigger a function within your environment to take the proper 
actions to rectify it. To solve for this, you'll create a Lambda 
function from scratch and create an S3 event trigger to execute the 
Lambda logic.

## Solution

Open a terminal session and log in to server using the provided lab credentials:

```
ssh cloud_user@<PUBLIC_IP_ADDRESS>

```

### Create an IAM Role for Lambda

1. Change to the directory where the necessary files are located:
    
    ```
    cd exercise_files/Section4-AppLayer/Lab1-LambdaS3EventTrigger/
    
    ```
    
2. Create an IAM role for Lambda:
    
    ```
    aws iam create-role --role-name LambdaIAMRole --description "Lambda Role" --assume-role-policy-document file://lambda_assume_role_policy.json
    
    ```
    
3. In the output under `"Arn"`, copy the role ARN and paste it into a text file for later use.

### Create a Policy for the Lambda Function and Attach It to Role

1. Create a Lambda policy:
    
    ```
    aws iam create-policy --policy-name LambdaRolePolicy --policy-document file://lambda_execution_policy.json
    
    ```
    
2. In the output under `"Arn"`, copy the policy ARN for use in the next set of commands.
3. Attach the IAM policy to the IAM role, replacing `<POLICY_ARN>` with the ARN previously copied:
    
    ```
    aws iam attach-role-policy --role-name "LambdaIAMRole" --policy-arn <POLICY_ARN>
    
    ```
    

### Create an SNS Topic and Subscribe Your Email Address to It

1. Create an SNS topic:
    
    ```
    aws sns create-topic --name LambdaTopic --region us-east-1
    
    ```
    
2. In the output under `"TopicArn"`, copy the topic ARN for use in the next set of commands.
3. Subscribe an endpoint (e.g., email address) to your topic, replacing `<TOPIC_ARN>` with the previously copied ARN and `<EMAIL_ADDRESS>` with your own email:
    
    ```
    aws sns subscribe --protocol "email" --topic-arn <TOPIC_ARN> --notification-endpoint <EMAIL_ADDRESS> --region us-east-1
    
    ```
    
    You should receive the status message `"SubscriptionArn": "pending confirmation"`.
    
4. To confirm the subscription, access the email previously used, open the SNS email, and click **Confirm subscription**.

### Modify the Lambda Function with the SNS Topic ARN and Zip It into a Lambda Deployment Package

1. Open the `lambda_function` file:
    
    ```
    vim lambda_function.py
    
    ```
    
2. To enable sending SNS notifications, uncomment the line `client = boto3.client('sns')` and the section below:
    
    ```
    response = client.publish(
    TopicArn='<SNS-TOPIC-ARN>',
    Message= payload_str,
    Subject='My Lambda S3 event')
    
    ```
    
3. In `TopicArn`, replace <SNS-TOPIC-ARN> with the topic ARN previously copied.
4. To save and exit the file, press **ESC**, type `:wq`, and press **Enter**.
5. Zip the file into a deployment package:
    
    ```
    zip lambda_function.zip lambda_function.py
    
    ```
    

### Create a Lambda Function

1. Create a Lambda function, replacing `<ROLE_ARN>` with the role ARN previously copied into a text file:
    
    ```
    aws lambda create-function --memory-size 128 --function-name my-lambda --runtime python3.7 --handler lambda_function.lambda_handler --zip-file fileb://lambda_function.zip --role <ROLE_ARN>
    
    ```
    
2. In the output under `"FunctionArn"`, copy the function ARN to a text file for later use.

### Add Lambda Permission for the S3 Service to Invoke the Function

1. Add Lambda permission, replacing `<S3_BUCKET_NAME>` with the S3 bucket name provided on the lab credentials page:
    
    ```
    aws lambda add-permission --action lambda:InvokeFunction --principal s3.amazonaws.com --statement-id LabS3Trigger --function-name my-lambda --source-arn arn:aws:s3:::<S3_BUCKET_NAME>
    
    ```
    

### Enable and Add Notification Configuration to the S3 Bucket

1. Open the `bucket-trigger-notification.json` file:
    
    ```
    vim bucket-trigger-notification.json
    
    ```
    
2. In the output under `"LambdaFunctionArn"`, delete the existing text and replace it with the function ARN previously copied into a text file:
    
    ```
    "LambdaFunctionArn": "<FUNCTION_ARN>"
    
    ```
    
3. To save and exit the file, press **ESC**, type `:wq`, and press **Enter**.
4. Enable the notification configuration on the S3 website bucket, replacing `<S3_BUCKET_NAME>` with the bucket name provided on the lab credentials page:
    
    ```
    aws s3api put-bucket-notification-configuration --bucket <S3_BUCKET_NAME> --notification-configuration file://bucket-trigger-notification.json
    
    ```
    

### Verify Configuration by Uploading a File to the Provided S3 Bucket

1. Upload a file to the bucket, replacing `<S3_BUCKET_NAME>` with the bucket name provided on the lab page:
    
    ```
    aws s3 cp lambda_function.py s3://<S3_BUCKET_NAME>
    
    ```
    
2. Once successfully uploaded, check your email.
    
    You should receive a notification email with details of the file uploaded to the S3 bucket.