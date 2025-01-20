# AWS Access Control Alerts with CloudWatch and CloudTrail

## Introduction

In this hands-on lab, we will create 
and configure a CloudTrail trail and a CloudWatch Logs log stream in 
order to set up monitoring and access alerts for an S3 bucket. 
Specifically, we'll create the trail for monitoring access to the S3 
bucket, the CloudWatch Logs log stream to allow searching and filtering 
of the logs, and then a CloudWatch metric filter and alarm on that 
metric filter to generate an alert whenever there is any access to an S3
 bucket.

Log in to the AWS Management Console with the credentials provided on the lab instructions page. Make sure you are using the *us-east-1 (N. Virginia)* region.

## Solution

### Create an S3 Bucket

1. In the AWS Management Console, navigate to the S3 service.
2. Click **+ Create bucket**.
3. Name the bucket whatever you like (e.g., "top-secret-bucket-12345").
4. Click **Create bucket**.
5. Click the name of the bucket to open it.
6. Click **Upload**.
7. Click **Add files**.
8. Upload a file of your choosing to the bucket (e.g., a sample text file).
9. Click **Upload**.

### Create a CloudTrail Trail

1. In the AWS Management Console, navigate to the CloudTrail service.
2. Click **Create trail**.
3. On the *Choose trail attributes* page, configure the following settings:
    - *Name*: Enter "top-secret-bucket-trail".
    - *Storage location*: Choose **Create new S3 bucket**.
    - *Trail log bucket and folder*: Use the suggested bucket.
    - *Log file SSE-KMS encryption*: Uncheck the **Enabled** option.
4. Click **Next**.
5. On the *Choose log events* page, check the box next to **Data events** under *Event type* to log only data events in CloudTrail.
6. Under *S3 Bucket*, uncheck the boxes next to **Read** and **Write** for the *All current and future S3 buckets* option to allow us to select an individual bucket.
7. Under *Individual bucket selection*, select **Browse**.
8. In the new *Choose S3 bucket* window, select the bucket listed under *Name*.
9. Click **Choose**.
10. Next to the **Browse** button, check the boxes next to **Read** and **Write**.
11. Click **Next**.
12. Review the attributes for your trail and click **Create trail**.
13. Go back to the S3 Management Console.
14. Click **Upload**.
15. Click **Add files**.
16. Select a new file to upload and click **Choose for upload**.
17. Click **Upload**.

### Create a CloudWatch Log Group

1. Open the CloudTrail Management Console in a new browser window or tab.
2. Click **Trails** in the left sidebar.
3. Click **top-secret-bucket-trail** to open it.
4. Scroll down to the *CloudWatch Logs* header, and click **Edit**.
5. Under *CloudWatch Logs*, check the box next to **Enabled**.
6. Under *Log group*, select **New**.
7. Under *Log group name*, change the name to just "aws-cloudtrail-logs".
8. Under *IAM Role*, select **New**.
9. Under *Role name*, enter "CloudTrailRole".
10. Click **Save changes**.
11. Go back to the CloudWatch Management Console.
12. In the left navigation menu, click **Logs**.
13. Under *Logs*, click **Log groups**.
14. In the console, under *Log groups*, click the name of the log stream to open it.
15. Go back to the S3 Management Console.
16. Click **Upload**.
17. Click **Add files**.
18. Select another file from your computer.
19. Click **Upload**.
20. Click **Close**.
21. Click **More** > **Download**.
22. Go back to the CloudWatch Management Console.
23. Wait a few minutes and refresh the page until logged events appear.

### Set Up a CloudWatch Alarm

1. In the CloudWatch Management Console, click **Logs** in the left navigation bar.
2. Click on **Log groups** in the left navigation bar.
3. In the console, select the log group under *Log group*.
4. Click **Actions**.
5. From the dropdown menu, select **Create metric filter**.
6. Under *Filter Pattern*, enter the following:
    
    ```
    { ($.eventSource = s3.amazonaws.com) && (($.eventName = PutObject) || ($.eventName = GetObject)) }
    
    ```
    
7. Click **Test Pattern**.
8. Click **Show test results**.
9. Click **Next**.
10. Configure the following settings:
    - *Filter name*: Enter "MetricFilter".
    - *Metric namespace*: Enter "MetricFilters".
    - *Metric name*: Enter "LogMetric".
    - *Metric value*: Enter "1".
11. Click **Next**.
12. Click **Create metric filter**.
13. Under *Metric filters (1)*, select our newly created MetricFilter filter.
14. Click **Create alarm**.
15. On the *Specify metric and conditions* page, configure the following settings under *Conditions*:
    - *Threshold type*: Select **Static**.
    - *Whenever LogMetric is...*: Select **Greater**.
    - *than...*: Enter "1".
16. Click **Next**.
17. On the *Configure actions* page, configure the following settings under *Notification*:
    - *Alarm state trigger*: Select **In alarm**.
    - *Select an SNS topic*: Select **Create new topic**.
    - *Email endpoints that will receive the notification...*: Enter your email address.
18. Click **Create topic**.
19. Open your email client and check for an email from AWS Notifications.
20. Open the email, and click the **Confirm subscription** link.
21. Go back to the AWS Console.
22. Click **Next**.
23. Under *Alarm name*, enter a name, like "AccessS3Secrets". You can give the alarm a description as well, but this is optional.
24. Click **Next**.
25. Click **Create alarm**.
26. Go back to the S3 Management Console.
27. Select one of the files in the S3 bucket, and click **Download**.
28. Repeat these steps for the other two files in the S3 bucket.
29. Upload an additional test file by clicking **Upload**.
30. Click **Add files**.
31. Select a file from your computer and click **Upload**.
32. Click **Close**.
33. Go back to your email client and check for an email from AWS Notifications.