# Monitoring, Auditing, and Logging Users and Resource Usage in AWS IAM

## Introduction

In this hands-on lab scenario, you are
 a security engineer working for a new startup that's launching an 
online bookstore for rare and antique books. The founder, Kia, needs 
your help with monitoring and auditing the activities in her account. In
 order to provide access and ensure the proper security measures are in 
place, you will use AWS Identity & Access Management (IAM) and AWS 
CloudTrail. You will provide Kia with the credential report, the details
 from the Access Advisor tab, and you will create a trail using 
CloudTrail.

## Solution

Log in to the live AWS environment using the credentials provided. Make sure you're in the N. Virginia (`us-east-1`) region throughout the lab.

### Generate a Credential Report

1. Navigate to the IAM dashboard.
2. From the left menu, click **Credential report**.
3. Click **Download Report** and open the downloaded CSV file. *(NOTE: This file can be viewed using Excel or Numbers.)*

> Note: The downloading and viewing of the CSV file is not a critical step. You can see example formatting of the CSV file at 00:50 in the solution video.
> 

### Utilize the Access Advisor Tab

1. Navigate back to the IAM Dashboard.
2. From the left menu, click **Users**.
3. Click on **developer-1** user.
4. Click the **Access Advisor** tab to review allowed services for the user.

### Create a Trail using CloudTrail

1. Navigate back to the AWS Management Console for **CloudTrail**.
2. On the left menu, click **Event history** to review the event history for the account.
3. There are 2 ways to create trails, on the left menu, click **Dashboard** > **Create trail** or **CloudTrail** > **Create a trail**.
4. On the *Choose trail attributes* page, set the following values:
    - *Trail name*: "lab-trail"
    - *Storage location*: Create new S3 bucket
    - *Trail log bucket and folder*: default bucket name
    - *Log file SSE-KMS encryption*: Deselect **Enabled**
    - *Log file validation*: Deselect **Enabled**
    - *CloudWatch Logs*: Deselect **Enabled**
5. Click **Next**.
6. On the *Choose log events* page, select *Management events*, *Data events*, and *Insights events*.
7. In the *Management events* section, select *Read* and *Write*.
8. In *Data events* section, set the following vales:
    - *Data event: S3*: select *Read* and *Write*.
    - *Data event: Lambda*: *All regions* and *All functions*
9. In the *Insights events* section: select *API call rate*.
10. Click **Next**.
11. Review the trail and click **Create trail**.

## Conclusion

Congratulations — you've completed this hands-on lab!