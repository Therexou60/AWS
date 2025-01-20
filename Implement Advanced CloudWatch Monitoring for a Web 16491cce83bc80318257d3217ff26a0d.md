# Implement Advanced CloudWatch Monitoring for a Web Server

## Introduction

CloudWatch Logs centralizes the logs 
from all of your systems, applications, and AWS services that you use, 
in a single, highly scalable service. In this lab, you will configure an
 EC2 instance to stream its Apache web server error logs to CloudWatch 
Logs. You will configure the agent and then log in to the CloudWatch 
Logs console to make sure the logs are streamed correctly. By the end of
 this lab, you will understand how to install the CloudWatch Logs agent 
and configure it to stream a log to the service.

## Solution

Log in to the AWS Management Console using the credentials provided on the lab instructions page. Make sure you're using the `us-east-1` Region.

### Download and Run the CloudWatch Logs Installer

1. From the AWS Management Console, navigate to EC2.
2. Click **Instances (running)**.
3. Click the checkbox next to **webserver-01**. Please give the lab an extra few minutes before connecting to webserver-01.
4. At the top, click **Connect**.
5. At the bottom of the page, click **Connect** to access the CLI.
6. Run the following commands to install the CloudWatch Logs agent:
    
    ```
    wget -O awslogs-agent-setup.py https://s3.amazonaws.com/aws-cloudwatch/downloads/latest/awslogs-agent-setup.py
    
    ```
    
    ```
    sudo python ./awslogs-agent-setup.py --region us-east-1
    
    ```
    

### Configure CloudWatch Logs

1. Go back to the AWS Management Console, and open IAM in a separate tab.
2. Click **Users**.
3. Click **cloud_user**.
4. Select the **Security credentials** tab.
5. Click **Create access key**.
6. Select **Command Line Interface (CLI)**.
7. Check the acknowledgment checkbox at the bottom of the page and click **Next**.
8. For **Description tag value**, enter *clouduseraccesskey*.
9. Click **Create access key**.
10. Copy the **Access key**.
11. Go back to the CLI (keep IAM open in the other tab), paste the **Access key ID**, and press **Enter**.
12. Go back to IAM and, in the **Secret access key** on the right, click **Show**. Then, copy the **Secret access key**.
13. Go back to the CLI, paste the **Secret access key**, and press **Enter**.
14. Press **Enter** twice to accept the default region name and output format.
15. Copy and paste the below path for the log file to upload:
    
    ```
    /var/log/apache2/error.log
    
    ```
    
16. Press **Enter** to keep the current Destination Log Group name.
17. Press **Enter** to accept the default Log Stream name.
18. Press **Enter** to keep the default Log Event timestamp format.
19. Press **Enter** to select **From start of file** as the initial position of upload.
20. Type `N` to complete the configuration.

### Log In to the CloudWatch Logs Website

1. In the search bar, enter **CloudWatch**, and right-click to open it in a new tab.
2. Click **Log groups**.
3. Click `/var/log/apache2/error.log`; if you don't see it yet, click the **Refresh** button.
4. Under **Log streams**, click the link for the instance identifier. You will see the contents of your error log with two events logged.
5. You can also view the contents of the error log in the CLI by running the following command:
    
    ```
    sudo cat /var/log/apache2/error.log
    
    ```
    

## Conclusion

Congratulations on successfully completing this hands-on lab!