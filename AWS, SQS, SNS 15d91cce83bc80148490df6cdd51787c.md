# AWS, SQS, SNS

# Guided Lab: Building Decoupled Applications by Using Amazon SQS

## Lab overview and objectives

Building modern architectures involves decoupled applications, which are smaller, independent building blocks that are convenient to develop, deploy, and maintain. Message queues provide communication and coordination for these distributed applications. Message queues, along with notification systems, can significantly reduce the coding of decoupled applications while improving performance, reliability, and scalability.

In this lab, you work with an image processing application on an AWS Cloud9 instance and then use Amazon Simple Queue Service (Amazon SQS) and Amazon Simple Notification Service (Amazon SNS) to create a decoupled architecture.

After completing this lab, you should be able to do the following:

- Review how the image processing web application works.
- Configure Amazon Simple Storage Service (Amazon S3) bucket events to send messages to an SNS topic.
- Configure Amazon SQS to subscribe to an SNS topic and store the message.
- Use Amazon SQS and Amazon SNS to create a decoupled architecture.
- Implement polling to consume messages in an SQS queue.
- Use Amazon SNS to send an email notification.

## Duration

This lab requires approximately **60 minutes** to complete.

## Scenario

You work with an application that accepts images and processes them to create tinted images. You work on this application in the following phases:

- **Phase 1:** In this phase, you work with a NodeJs-based application through an AWS Cloud9 integrated development environment (IDE) wherein components such as the web server and application server are tightly coupled. Refer to the phase 1 architecture diagram for more information.
- **Phase 2:** In this phase, you work with an enhanced application through a different AWS Cloud9 IDE, and this application uses Amazon SQS and Amazon SNS to create a decoupled architecture wherein the web server and application server do not communicate directly. Refer to the phase 2 architecture diagram for more information.

You start with an architecture similar to the following:

![https://labs.vocareum.com/web/3683978/3711361.0/ASNLIB/public/docs/lang/en_us/images/20-lab-starting.png](https://labs.vocareum.com/web/3683978/3711361.0/ASNLIB/public/docs/lang/en_us/images/20-lab-starting.png)

**Phase 1:** Build a tightly coupled architecture. By the end of this phase, you will have created the following architecture:

![https://labs.vocareum.com/web/3683978/3711361.0/ASNLIB/public/docs/lang/en_us/images/20-lab-sqs-tightly-coupled.png](https://labs.vocareum.com/web/3683978/3711361.0/ASNLIB/public/docs/lang/en_us/images/20-lab-sqs-tightly-coupled.png)

| Step | Explanation |
| --- | --- |
| 1 | Start the web application, which checks for images. |
| 2 | The user uploads the image through the web application. |
| 3 | The
 web server stores the image in the S3 bucket and updates the image file
 information (metadata) in Amazon DynamoDB along with the initial 
status. |
| 4 | The web 
server initiates the processing on the application server synchronously 
and waits for the processed image to be available (tight coupling). 
While the web server is waiting, the application server does the 
processing, updates the DynamoDB table status, and sends the processed 
image to the S3 bucket. |
| 5 | A new tinted image is available to the user. |

**Phase 2:**  Build a decoupled architecture. By the end of this phase, you will have created the final architecture in the following diagram. This diagram shows some of the actions that you take and the communication that occurs between Amazon Elastic Compute Cloud (Amazon EC2), Amazon SQS, Amazon SNS, and other service components.

![https://labs.vocareum.com/web/3683978/3711361.0/ASNLIB/public/docs/lang/en_us/images/20-lab-sqs-final.png](https://labs.vocareum.com/web/3683978/3711361.0/ASNLIB/public/docs/lang/en_us/images/20-lab-sqs-final.png)

| Step | Explanation |
| --- | --- |
| 1 | The user starts the web application, which checks for images (image lookup). |
| 2 | The user uploads the image through the web application. |
| 3 | The
 web application writes the image directly to the S3 bucket and also 
sends a request to update the image file information (metadata) in 
DynamoDB along with the initial status. |
| 4 | As soon as the file is stored in the S3 bucket, a notification is sent to the SNS topic. |
| 5 | The
 SNS topic has two subscribers. The SNS topic publishes the message to 
Amazon SQS and sends an email notification to the subscribed users. |
| 6 | The
 user starts the processing mechanism for the application server. This 
mechanism allows the application server to poll for messages in the 
queue for processing. |
| 7 | The
 application server processes the message by looking up the 
corresponding image information in the DynamoDB table, pulls the image 
from Amazon S3, and subsequently begins processing the image for tinting
 and resizing. The status of the image is updated in the DynamoDB table 
until the process is complete. |
| 8 | Once the process is complete, the browser displays the new tinted image to the user. |

## Accessing the AWS Management Console

1. At the top of these instructions, choose  **Start Lab**.
    - The lab session starts.
    - A timer displays at the top of the page and shows the time remaining in the session.
        
        **Tip:** To refresh the session length at any time, choose  **Start Lab** again before the timer reaches 0:00.
        
    - Before you continue, wait until the circle icon to the right of the AWS  link in the upper-left corner turns green. When the lab environment is ready, the **AWS Details** panel displays.
2. To connect to the AWS Management Console, choose the **AWS** link in the upper-left corner above the terminal window.
    - A new browser tab opens and connects you to the console.
        
        **Tip:** If a new browser tab does not open, a banner or icon is usually at the top of your browser with a message that your browser is preventing the site from opening pop-up windows. Choose the banner or icon, and then choose **Allow pop-ups**.
        
3. Arrange the AWS Management Console tab so that it displays alongside these instructions. Ideally, you will have both browser tabs open at the same time so that you can follow the lab steps.

# Phase 1: Building a tightly coupled application

## Task 1: Installing the image processing application

In this task, you download the required files and install the image processing application on the AWS Cloud9 instance. Then you configure a security group and the S3 bucket permissions required for the communication between the application and AWS services.

### Task 1.1: Logging in to the AWS Cloud9 IDE

1. From the top of these instructions, choose **AWS Details**.
2. Copy all the values from the table into a text editor. You need this information later.
3. To close the **AWS Details** window and return to lab instructions, in the upper-right corner, choose **Close**.
4. From the values that you pasted into a text editor, copy the value for **Phase1Cloud9url**, and paste it into a new browser tab to open the AWS Cloud9 IDE.
5. On the AWS Cloud9 console, in the upper-right corner, choose **Preferences** (the gear icon).
6. On the **Preferences** tab, choose **AWS Settings**.
7. For **Credentials**, clear **AWS managed temporary credentials**.
8. To close the **Preferences** tab and return to AWS Cloud9 **Welcome** screen, choose **X** on the **Preferences** tab.

**Note:** You deactivated AWS managed credentials because you use the AWS Identity and Access Management (IAM) role provisioned for this lab.

### Task 1.2: Downloading files and installing the application

1. On the AWS Cloud9 IDE, to close the **Welcome** screen, choose **x**, and then go to the terminal window at the bottom. (You can resize the window by dragging the terminal window up.)
2. At the **voclabs:~/environment** prompt, to download the application files and install the web server, run the following commands:

```

wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACACAD-3-113230/17-lab-mod13-guided-SQS/code.zipunzip code.zipcd phase_1/web_server_1/npm install
```

1. In the same window, to open another terminal, choose **+** > **New Terminal**.
2. In the new terminal, run the following commands to install the application server:

```

cd phase_1/app_server_1/npm install
```

You have now installed the NodeJs module and the application, which has a web server and an application server. Next, you configure other services.

**Note:** Keep the AWS Cloud9 IDE open.

### Task 1.3: Configuring the security group and IAM role

1. On the AWS Management Console, in the search box, enter and choose `EC2` to open the Amazon EC2 console.
2. In the navigation pane, choose **Instances**.
3. From the list of EC2 instances, choose the one with **Phase1Server** in the **Name**.
4. Choose the **Security** tab.
5. Choose the **Security groups** link.
6. On the page for the security group, choose **Edit inbound rules**.
7. On the **Edit inbound rules** page, choose **Add rule**, and configure the following options:
    - For **Type**, choose **Custom TCP**.
    - For **Port range**, enter `8000-8100`.
    - For **Source**, choose **Anywhere-IPv4**.
8. Choose **Save rules**.
    
    **Note:** These ports are opened because your application uses this port range.
    
9. In the navigation pane, choose **Instances**, and then choose the **Phase1Server** instance again.
10. Choose the **Details** tab.
11. Copy the **Public IPv4 address** and paste it into a text editor. You need this address later.
12. While the instance is selected, choose **Actions** > **Security** > **Modify IAM role**.
13. On the **Modify IAM role** page, from the **IAM role** dropdown list, choose the IAM role with **Ec2InstanceProfile** in the name.
14. Choose **Update IAM role**.
15. Repeat this task for the **Phase2Server** EC2 instance. This step is required for phase 2.

### Task 1.4: Configuring the S3 bucket permissions

In this lab, the S3 bucket that you use for storing images is private by default. In this task, you adjust the permissions so that you can access the bucket from your local computer.

1. On the AWS Management Console, in the search bar at the top, enter and choose `S3` to open the Amazon S3 console.
2. In the navigation pane, choose **Buckets**.
3. Choose the link for the bucket with **phase1bucket** in the name.
4. Choose the **Permissions** tab.
5. For **Block public access (bucket settings)**, choose **Edit**.
6. Clear **Block *all* public access**.
7. Choose **Save changes**.
8. In the **Edit Block public access (bucket settings)** window, enter `confirm`, and then choose **Confirm**.
9. For **Bucket policy**, choose **Edit**.
10. In the **Policy** field, enter the following code.

```

{    "Version": "2012-10-17",    "Statement": [        {            "Sid": "PublicRead",            "Effect": "Allow",            "Principal": "*",            "Action": "s3:*",            "Resource": "[bucket ARN]/*",            "Condition": {                "IpAddress": {                    "aws:SourceIp": "[ServerIP]/32"                }            }        }    ]}
```

1. In the code that you entered, replace the following values:
    - Replace *[bucket ARN]* with the **Bucket ARN** on the same window.
    - Replace *[ServerIP]* with the public IP address of your local computer. You can use websites such as [WhatsMyIP](https://www.whatsmyip.org/) to find this information.
2. Ensure that the policy editor does not flagged any errors, and then choose **Save changes**.

You have now configured a security group and the S3 bucket permissions required for the image processing application. Next, you configure the application parameters and start the application.

### Task 1.5: Configuring and starting the application

In this task, you configure three separate configuration files for each application tier: browser, web application, and application server.

1. In the AWS Cloud9 IDE that you kept open, from the explorer on the left, expand the folder titled **phase_1**.

First, you make changes for the browser tier.

1. Open the following file for editing in the IDE: **web_server_1/static/js/config.js**
2. In the file, replace the following values with the variables that you copied into a text editor earlier.

**Note:** Replace the values and the brackets []. You can use the Find/Replace capability of the IDE (press Ctrl+f in the AWS Cloud9 IDE).

| Parameter Name | Value |
| --- | --- |
| CONFIG.FULL_URL_STR | http://[Public IPv4 address of Phase1 EC2]:8008/upload |
| CONFIG.FULL_IMAGE_PATH_STR | https://[Phase1bucket].s3.amazonaws.com |
| CONFIG.FULL_IMAGE_URL_STR | http://[Public IPv4 address of Phase1 EC2]:8008/get-image-urls |
1. After replacing the values, choose **File** > **Save**.
2. To close the file, on the tab with the file, choose **X**.

Next, you make configuration changes for the web tier.

1. Open the following file for editing in the IDE: **web_server_1/libs/config.js**
2. In the file, replace the following values with the variables that you copied into a text editor file earlier.

**Note:** Replace the values and the brackets []. You can use the Find/Replace capability of the IDE.

| Parameter Name | Value |
| --- | --- |
| CONFIG.PROCESSING_URL_STR | http://[Public IPv4 address of Phase1 EC2]::8009/process |
| CONFIG.S3.IMAGE_BUCKET_NAME_STR | [Phase1bucket] |
1. After replacing the values, choose **File** > **Save**.
2. To close the file, on the tab with the file, choose **X**.

Finally, you make configuration changes for the application server tier.

1. Open the following file for editing in the IDE: **app_server_1/libs/config.js**
2. In the file, replace each parameter name and the brackets with the corresponding value.

| Parameter Name | Value |
| --- | --- |
| CONFIG.S3.IMAGE_BUCKET_NAME_STR | [Phase1bucket] |
1. After replacing the value, choose **File** > **Save**.
2. To close the file, on the tab with the file, choose **X**.

**Note:** Be careful while assigning or replacing these values because they affect the running of the application.

Now that you have updated the application configuration, you are ready to start the servers.

1. In the AWS Cloud9 IDE window, on the first terminal, run the `pwd` command to ensure that the current working directory is **/phase_1/web_server_1/**.
2. To start the web server, run the following command:
    
    ```
    
    npm start
    ```
    

**Note:** A message is displayed that says the *App is listening on port 8008*.

1. In the AWS Cloud9 IDE terminal, on the other window, run the `pwd` command to ensure that the current working directory is **cd /phase_1/app_server_1/**.
2. To start the application server, run the following command:
    
    ```
    
    npm start
    ```
    

**Note:** A message is displayed that says the *App is listening on port 8009*.

Leave this window running. Your application is now ready to be tested.

## Task 2: Testing the application

1. To test the application, in the following link, replace *[Public IPv4 address of Phase1 EC2]* with the phase 1 public IPv4 address that you pasted into a text editor earlier: `http://[Public IPv4 address of Phase1 EC2]:8008`. Paste your revised link into a browser tab.
    
    The **Image Tinter** application is displayed in the browser.
    
2. To check for images that are processing, choose **Start checking for images**.
    
    Next, you upload an image.
    
3. While processing is happening in the background, choose **Choose File**, navigate to any file that you want to upload, select the file, and choose **Submit**.
    
    The processed image is displayed as it becomes available.
    
4. Once you are done, choose **Stop checking for images**.
5. To delete a processed image, choose the **Bin** icon on the image.

Congratulations! You have successfully completed phase 1 of the application building process.

## Analysis of phase 1

Review the phase 1 architecture diagram at the beginning of this lab. Once you upload images, the web server is communicating directly and regularly with the application server to process and display the images. This architecture uses tight coupling, which can create problems. In a tightly coupled architecture, if one component of the system breaks, the entire application breaks, and the application as a whole is unusable until you fix the broken component.

One of the solutions for such problems could be using decoupling to separate components.

In the next phase, you create a decoupled architecture by introducing AWS services such as Amazon SQS and Amazon SNS.

You can close the application and AWS Cloud9 IDE that you opened for phase 1. You use a new AWS Cloud9 IDE for phase 2.

# Phase 2: Building the application with a decoupled architecture

In this phase, you create a new version of your application based on a decoupled architecture, which improves the availability, scaling, and performance of the application in a high-volume scenario. You achieve this by using a combination of AWS services, such as Amazon SQS and Amazon SNS. Refer to the phase 2 architecture diagram for more information.

First, you install the application in a new AWS Cloud9 IDE and then configure the Amazon SQS and Amazon SNS services and Amazon S3 notification. Finally, you configure the application parameters required for running the application in a new architecture.

## Task 3: Installing the application

In this task, you download the required files and install the image processing application in the AWS Cloud9 IDE. Then you configure a security group to facilitate communication between the application running on the AWS Cloud9 instance and the user interface (browser).

### Task 3.1: Logging in to the AWS Cloud9 IDE

1. From your text editor, choose the value for **Phase2Cloud9url**, and paste it in a new browser tab to open the AWS Cloud9 IDE.
2. On the AWS Cloud9 console, in the upper-right corner, choose **Preferences** (the gear icon).
3. On the **Preferences** tab, choose **AWS Settings**.
4. For **Credentials**, clear **AWS managed temporary credentials**.
5. To close the **Preferences** tab and return to AWS Cloud9 **Welcome** screen, choose **X** on the **Preferences** tab.

**Note:** Make adjustments to the AWS Cloud9 window as you did in the previous tasks.

### Task 3.2: Downloading files and installing the application

1. In the AWS Cloud9 IDE terminal window, at the **voclabs:~/environment** prompt, to install the web server, run the following commands:
    
    ```
    
    wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACACAD-3-113230/17-lab-mod13-guided-SQS/code.zipunzip code.zipcd phase_2/web_server_2/npm install
    ```
    
2. In the same window, to open another terminal, choose **+** > **New Terminal**.
3. In the new terminal, run the following commands to install the
    
    ```
    
    cd phase_2/app_server_2/npm install
    ```
    

You have now installed the NodeJs module and the application, which has a web server and an application server. Next, you configure other services.

Keep the AWS Cloud9 IDE open.

1. On the AWS Management Console, in the search box, enter choose `EC2` to open the Amazon EC2 console.
2. In the navigation pane, choose **Instances**.
3. From the list of EC2 instances, choose the one with **Phase2Server** in the name.
4. Choose the **Details** tab.
5. Copy the **Public IPv4 address** and paste it into a text editor. You need this address later.

## Task 4: Configuring Amazon SQS

1. On the AWS Management Console, in the search box, enter and choose `SQS` to open the Amazon SQS console.
2. Choose **Create queue**.
3. On the **Create queue** page, for **Name**, enter `ImageApp`.
    
    **Note:** Leave the other properties unchanged.
    
4. Choose **Create queue**.
5. On the **ImageApp** page, in the **Details** panel, copy the **URL** into a text editor for later use.

## Task 5: Configuring Amazon SNS

1. On the AWS Management Console, in the search box, enter and choose `SNS` to open the Amazon SNS console.
2. In the **Create topic** section, in the **Topic name** box, enter `uploadnotification`.
3. Choose **Next step**.
4. On the **Create topic** page, expand **Access policy - *optional***, and then choose **Advanced**.
    
    You now configure the policy to provide access to the phase 2 S3 bucket to send a notification for every upload.
    
5. In the **JSON editor**, delete the existing policy.
6. To add a new policy, copy and paste the following code into the **JSON editor**.

```

{    "Version": "2012-10-17",    "Id": "S3UploadNotification",    "Statement": [        {            "Sid": "S3 SNS topic policy",            "Effect": "Allow",            "Principal": {                "Service": "s3.amazonaws.com"            },            "Action": [                "SNS:Publish"            ],            "Resource": "[SNS-topic-ARN]",            "Condition": {                "ArnLike": {                    "aws:SourceArn": "arn:aws:s3:*:*:[Phase2bucket]"                },                "StringEquals": {                    "aws:SourceAccount": "[Lab-account]"                }            }        }    ]}
```

1. In this code, replace the following values:
    - Replace *[Phase2bucket]* with value for the **Phase2bucket** you copied into the text editor at the beginning of the lab.
    - Replace *[Lab-account]* with the 12-digit **Account ID** from the upper-right corner of your AWS console.
2. Choose **Create topic**.
3. From the **Details** pane, copy the **ARN** for the topic, and choose **Edit**.
4. Expand **Access policy - *optional***.
5. In the JSON editor, replace *[SNS-topic-ARN]* with the ARN that you just copied.
6. Choose **Save changes**.

Now that you have provided the necessary permissions for the S3 bucket to send an SNS notification, you configure the phase 2 S3 bucket event notification.

## Task 6: Configuring Amazon S3 permissions and event notifications

In this task, you configure the S3 bucket to send a notification to the SNS topic that you created as soon as your application uploads an image.

1. On the AWS Management Console, in the search box, enter and choose `S3` to open the Amazon S3 console.
2. Choose the bucket with **phase2bucket** in the name.
3. Choose the **Permissions** tab.
4. For **Block public access (bucket settings)**, choose **Edit**.
5. Clear **Block *all* public access**.
6. Choose **Save changes**.
7. In the **Edit Block public access (bucket settings)** window, enter `confirm`, and then choose **Confirm**.
8. For **Bucket policy**, choose **Edit**.
9. In the **Policy** window, copy and paste the following code:

```

{    "Version": "2012-10-17",    "Statement": [        {            "Sid": "PublicRead",            "Effect": "Allow",            "Principal": "*",            "Action": "s3:*",            "Resource": "[bucket ARN]/*",            "Condition": {                "IpAddress": {                    "aws:SourceIp": "[ServerIP]/32"                }            }        }    ]}
```

1. In this code, replace the following values:
- Replace *[bucket ARN]* with the **Bucket ARN** on the same window.
- Replace *[ServerIP]* with the public IP address of your local computer. You can use websites such as [WhatsMyIP](https://www.whatsmyip.org/) to find this information.
1. Choose **Save changes**.

Next, you choose the properties for the bucket.

1. Choose the **Properties** tab.
2. In the **Event notifications** section, choose **Create event notification**, and configure the following options:
- **Event name**: Enter `SendtoSns`.
- **Object creation**: Select **All object create events**.
- **Destination**: Choose **SNS topic**.
- **SNS topic**: From the dropdown list, choose the **uploadnotification** topic that you created earlier.
1. Choose **Save changes**.

### Task 7: Creating Amazon SNS subscriptions

Now that you have created an Amazon S3 event to send a notification to Amazon SNS, you create a subscription for the SNS topic to send a message to the queue. You also configure email subscriptions for the user.

### Task 7.1: Configuring an Amazon SQS subscription

1. On the AWS Management Console, in the search box, enter and choose `SQS` to open the Amazon SQS console.
2. Choose the **ImageApp** queue that you created.
3. For **SNS Subscriptions**, choose **Subscribe to Amazon SNS topic**.
4. On the **Subscribe to Amazon SNS topic** page, from the dropdown list, choose the **uploadnotification** SNS topic.
5. Choose **Save**.

### Task 7.2: Configuring an Amazon SNS email subscription

1. On the AWS Management Console, in the search box, enter and choose `SNS` to open the Amazon SNS console.
2. In the navigation pane, choose **Topics**, and then choose **uploadnotification**.
3. From **Subscriptions**, choose **Create subscription**.
4. On the **Create subscription** page, configure the following options:
- **Protocol:** Choose **Email**.
- **Endpoint:** Enter an email address that you can access now.
1. Choose **Create subscription**.

An email is sent to your email address for confirmation.

1. Go to your email account, and open the email message with the subject **AWS Notification - Subscription Confirmation.**
2. In the email, choose the **Confirm subscription** link.

You have now configured all the required services for the image processing application. Next, you configure the application parameters and start the application.

## Task 8: Configuring parameters and starting the application

In this task, you configure three separate configuration files for each application tier: browser, web application, and application server.

1. In the AWS Cloud9 IDE that you kept open, from the explorer on the left, expand the folder titled **phase_2**.

First, you make changes for the browser tier.

1. Open the following file for editing in the IDE: **web_server_2/static/js/config.js**
2. Assign or replace the following values with the variables that you copied into a text editor earlier.

**Note:** Replace the values and the brackets []. You can use the Find/Replace capability of the IDE (press Ctrl+f in the AWS Cloud9 IDE).

| Parameter Name | Value |
| --- | --- |
| CONFIG.FULL_IMAGE_URL_STR | http://[Public IPv4 address of Phase2 EC2]:8007/get-image-urls |
| CONFIG.FULL_IMAGE_PATH_STR | https://[Phase2bucket].s3.amazonaws.com |
| CONFIG.ARN_TRUNC_URL_STR | [Phase2bucket] |
| CONFIG.APP_SERVER_URL_STR | http://[Public IPv4 address of Phase2 EC2]:8010 |
| CONFIG.WEB_SERVER_URL_STR | http://[Public IPv4 address of Phase2 EC2]:8007 |
1. After replacing the values, choose **File** > **Save**.
2. To close the file, on the tab with the file, choose **X**.

Next, you make configuration changes for the web tier.

1. Open the following file for editing in the IDE: **web_server_2/libs/config.js**
2. In the file, replace each parameter name and the brackets with the corresponding value.

| Parameter Name | Value |
| --- | --- |
| CONFIG.S3.IMAGE_BUCKET_NAME_STR | [Phase2bucket] |
1. After replacing the value, choose **File** > **Save**.
2. To close the file, on the tab with the file, choose **X**.

Finally, you make configuration changes for application server tier.

1. Open the following file for editing in the IDE: **app_server_2/libs/config.js**
2. In the file, replace each parameter name and the brackets with the corresponding value.

| Parameter Name | Value |
| --- | --- |
| CONFIG.S3.IMAGE_BUCKET_NAME_STR | [Phase2bucket] |
| CONFIG.SQS.HTTPS_URL_STR | [Phase2 SQS URL] |

**Note:** The **Phase2 SQS URL** is the URL for the **ImageApp**.

1. After replacing the values, choose **File** > **Save**.
2. To close the file, on the tab with the file, choose **X**.

**Note:** Be careful while assigning or replacing these values because they affect the running of application.

Now that you have updated the application configuration, you are ready to start the servers.

1. In the AWS Cloud9 IDE window, on the first terminal, run the `pwd` command to ensure that the current working directory is **/phase_2/web_server_2/**.
2. To start the web server, run the following command:

```

npm start
```

**Note:** A message is displayed that says the *App is listening on port 8007*.

1. In the AWS Cloud9 IDE terminal, on the other window, run the `pwd` command to ensure that the current working directory is **cd /phase_2/app_server_2/**.
2. To start the application server, run the following command:

```

npm start
```

**Note:** A message is displayed that says the *App is listening on port 8010.*

Your application is now ready to be tested.

## Task 9: Testing the application

1. To test the application, in the following link, replace *[Public IPv4 address of Phase2 EC2]* with the phase 2 public IPv4 address that you pasted into a text editor earlier: `http://[Public IPv4 address of Phase2 EC2]:8007`. Paste your revised link into a browser tab.
    
    The **Image Tinter (Improved)** application is displayed on the browser.
    
2. Choose **Images Lookup**.

Images that are uploaded to the S3 bucket and ready to be processed are displayed. (Notice that processing has not yet started.) Images are still in their original appearance and display the current processing status.

1. To upload additional images while processing is happening in the background, choose **Choose File**, navigate to any file that you want to upload, select the file, and choose **Submit**.
    
    Notice the email notification that you receive when an image is uploaded.
    
2. To poll the queue for processing images, choose **Poll SQS**.

The processed images are displayed to the user as processing occurs.

Notice the email notification that you receive when a processed image is uploaded.

1. To stop polling the queue, choose **Stop Polling SQS**.
2. When you are done, choose **Stop Images Lookup**.
3. To delete a processed image, choose the **Bin** icon on the image.

Congratulations! You have successfully completed phase 2 of the application building process.

## Analysis

Observe the phase 2 architecture diagram. Once you upload the image, the web server updates the DynamoDB table. It does not maintain a continuous communication with the application server like the phase 1 design. The application server performs the processing on demand. The web server renders images as they are available. Both servers are working independently of each other. This is loose coupling, or decoupling. With loose coupling, each application component is working independently and interacting with other services only as needed. If the application server is down for some reason, messages would remain in the queue until it is up again and will then resume processing. This is the advantage of decoupling. This design leads to better scaling, availability, and performance.

## Conclusion

Congratulations! You now have successfully done the following:

- Reviewed how the image processing web application works
- Configured S3 bucket events to send messages to an SNS topic
- Configured Amazon SQS to subscribe to an SNS topic and store the message
- Used Amazon SQS and Amazon SNS to create a decoupled architecture
- Implemented polling to consume messages in an SQS queue
- Used Amazon SNS to send an email notification

## Submitting your work

1. To record your progress, at the top of these instructions, choose **Submit**.
2. When prompted, choose **Yes**.

After a couple of minutes, the **Grades** panel appears and shows you how many points you earned for each task. If the results don't display after a couple of minutes, choose **Grades** at the top of these instructions.

**Tip:** You can submit your work multiple times. After you change your work, choose **Submit** again. Your last submission is recorded for this lab.

1. To find detailed feedback about your work, choose **Submission Report**.

## Lab complete

Congratulations! You have completed the lab.

1. At the top of this page, choose  **End Lab**, and then choose Yes to confirm that you want to end the lab.

The following message appears: **Ended AWS Lab Successfully**

© 2023, Amazon Web Services, Inc. and its affiliates. All rights reserved. This work may not be reproduced or redistributed, in whole or in part, without prior written permission from Amazon Web Services, Inc. Commercial copying, lending, or selling is prohibited.