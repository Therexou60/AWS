# Creating an AWS CodeCommit Repository That Triggers Email Notifications

## Introduction

This lab will consist of creating a 
new AWS CodeCommit repository and adding our source control. We will use
 a trigger in CodeCommit to listen for any new commit to our source code
 to trigger an email notification via Amazon Simple Notification Service
 (SNS).

## Solution

Log in to the live AWS environment using the credentials provided. Make sure you're in the N. Virginia (`us-east-1`) region throughout the lab.

Please find the index.html file used for this lab in the [GitHub repository](https://github.com/natonic/Developer-Tools-Deep-Dive/tree/master/Labs/CodeCommitTriggersSNS):

### Create a CodeCommit Repository

1. Open the CodeCommit console.
2. On the **Repositories** page, click **Create repository**.
3. On the **Create repository** page, set the following values:
- **Repository name**: **codecommitnotifications**
- **Description**: Leave as default.
1. Click **Create**.

### Create an Amazon SNS topic

1. Navigate to Amazon SNS.
2. On the homepage, enter the topic name **CodeCommitNotify** in the **Create Topic** section.
3. Click **Next step**.
4. On the **Create topic** page, select the **Standard** option and leave everything else as the default.
5. Click **Create topic**.

### Subscribe to the Topic

1. From the **CodeCommitNotify** page, click **Create subscription**.
2. On the **Create subscriptions** page, set the following values:
    - **Protocol**: **Email**
    - **Endpoint**: Enter your email address.
3. Click **Create subscription** to send a confirmation message to the registered email.
4. Open the AWS Subscription Confirmation email and click **Confirm subscription**.
5. Return to the Amazon SNS page and check that the confirmation went through.

### Create an Event

1. Navigate to EventBridge.
2. On the landing page, under **Get Started** on the right side of the page, ensure *EventBridge Rule* is selected and click **Create Rule**.
3. On the **Create rule** page, set the following values:
    - **Name**: **codecommitrule**
    - **Event bus**:**default**
4. Select **Rule with an event pattern**
5. Click **Next**.
6. In **Event source**, select **AWS events or EventBridge partner events**.
7. In the Event Pattern section, choose the Event source of **AWS Services**.
8. Choose the **CodeCommit service**.
9. Choose the event type of **All Events**.
10. Click **Next**.
11. Choose the Target type of **AWS service**.
12. Select a target of **SNS topic**.
13. Choose your Topic name.
14. Click **Next**.
15. Click **Next**.
16. Click **Create rule**.

### Add a File to the Repository

1. Navigate back to CodeCommit.
2. On the **Repositories** page, select the newly created repository.
3. Click **Add file** > **Upload file**.
4. Upload a file from your local computer.
5. In the **Commit changes to master** section, set the following values:
    - **Author name**: Enter your name.
    - **Email address**: Enter your email address.
    - **Commit message**: Select **Added file.**.
6. Click **Commit changes**.
7. Check your email to see if you received a notification of a change to the repository.

## Conclusion

Congratulations — you've completed this hands-on lab!