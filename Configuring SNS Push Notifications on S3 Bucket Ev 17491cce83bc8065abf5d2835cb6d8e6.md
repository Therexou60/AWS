# Configuring SNS Push Notifications on S3 Bucket Events Inside of the AWS Console

## Introduction

In this live AWS environment, you will
 configure an S3 bucket to trigger AWS Simple Notification Service 
notifications whenever an object is added to an S3 bucket. This scenario
 will help you understand how you can architect your application to 
respond to S3 bucket events using other services such as SNS, AWS, 
Lambda, and others.

## Solution

Log in to the AWS Management Console using the credentials provided on the lab instructions page. Make sure you're using the *us-east-1* region.

### Create an S3 Bucket

1. Navigate to the S3 portion of the console, click **S3** under *Storage*.
2. Click **Create bucket**.
3. Provide a unique name for *Bucket name*. We used "sns-notifications-s3-test-errol".
4. The *Region* should already be set to US East (N.Virgina) us-east-1.
5. Click **Add tag**. Insert "CreatedBy" for *Key* and "Errol" for *Value-optional*.
6. Click **Create bucket**.

### Create an SNS Topic

1. Navigate to the main page of the AWS Management Console.
2. Type "Simple" under *Find Services*.
3. Select **Simple Notification Service**.
4. Click the three lines located on the top left of the page to expand the sidebar.
5. Click **Topics** in the sidebar.
6. Click **Create topic**.
7. Select **Standard** for *Type*.
8. Give it a *Name* of "S3Events".
9. Under *Tags-optional*, Insert "CreatedBy" for *Key* and "Errol" for *Value-optional*.
10. Click **Create topic**.
11. On the *Topic details* page, click **Create subscription**.
12. Copy the *ARN* number located under *S3Events Details* to your clipboard.

### Configure the Bucket — Part 1

1. Navigate back to AWS Management Console and click **S3**.
2. Click on your newly created S3 bucket, `sns-notifications-s3-test-errol`.
3. Click **Properties**.
4. Scroll down to *Event notifications*.
5. Click **Create event notifications**.
6. Give it an *Event name* of "S3ObjectCreated".
7. Select **All object create events** under *Event types*.
8. Select **SNS topic** under *Destination*.
9. Choose *SNS topic* by clicking the dropdown menu.
10. Select topic **S3Events**.
11. Click **Save changes**. **(Note: Will receive an Unknown Error.)**

### Modify the SNS Topic Policy

1. Navigate to AWS Management Console and open **Simple Notifications Services** in a new browser.
2. Click **1** under *Topics*.
3. Click **S3Events** under *Name*.
4. Click **Edit**.
5. Update *Access policy* by clicking the dropdown menu to access JSON editor.
6. Find line 4 and at the end of line 4, press `Enter`.
7. Add the following code at line 5, after `"Statement": [`:

```
{
      "Effect": "Allow",
      "Principal": {
          "AWS": "*"
  },
      "Action": "SNS:Publish",
      "Resource": "SNS_ARN_REPLACE_ME",
      "Condition": {
          "StringEquals": {
              "aws:SourceArn": "S3_BUCKET_ARN_REPLACEME"
          }
      }
  },

```

1. Replace this line of code `S3_BUCKET_ARN_REPLACEME`, by navigating to S3 and copying the **Amazon resource name (ARN)** under *Bucket overview* to your clipboard.
2. Replace this line of code `SNS_ARN_REPLACE_ME`, by navigating to Amazon SNA tab and copying the **ARN** under *Topics*.
3. Scroll down and click **Save changes**.

### Configure the Bucket — Part 2

1. Navigate to Amazon S3 Bucket.
2. Click on your newly created S3 bucket, `sns-notifications-s3-test-errol`.
3. Click **Properties**.
4. Scroll down to *Event notification*.
5. Click **Create event notification**.
6. Give it an *Event name* of "S3CreatedObjects".
7. Select **All object create events** under *Event types*.
8. Select **SNS topic** under *Destination*.
9. Choose *SNS topic* by clicking the dropdown menu.
10. Select topic **S3Events**.
11. Click **Save changes**. **(Note: Should now be able to successfully save changes.)**

### Create the Email Subscription

1. Navigate to AWS Management Console.
2. Click **Simple Notification Service**.
3. In the left menu pane, click **Subcriptions**.
4. Click **Create subscription**.
5. Click the search field under *Topic ARN*.
6. Your existing Events Topic ARN should automatically populate.
7. Click the dropdown menu under *Protocol* and select **Email**.
8. Enter your email address under *Endpoint*.
9. Click **Create subscription**. **(Note: Status will show *Pending confirmation*).**
10. Navigate to your email inbox to accept and confirm AWS notification subscription, clicking **Confirm subscription**.
11. Subscription confirmed page will appear if successful.
12. Navigate back to Amazon SNS details page and reload to confirm status.

### Create the SMS Subscription

1. Go back to SNS Topics by clicking **Topics**.
2. Click the radio button next to *S3Events*.
3. Click **Publish message**.
4. Insert "HELLO" under *Subject-optional*.
5. Insert "This is a test message" under *Message body*.
6. Scroll down and click **Publish message**.
7. Navigate to your email inbox to confirm if AWS Notification email was received.
8. Navigate to Amazon S3 Bucket.
9. Click on your S3 bucket, `sns-notifications-s3-test-errol`.
10. Click **Upload** under *Objects*.
11. Click **Add files**.
12. Select a file and click **Upload**.
13. Navigate to your email inbox for the new Amazon S3 Notification email to verify SNS push notifications are working correctly.
14. Please delete your email in the SNS section before closing the lab.