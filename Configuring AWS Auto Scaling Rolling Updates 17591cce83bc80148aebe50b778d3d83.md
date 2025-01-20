# Configuring AWS Auto Scaling Rolling Updates

## Introduction

Welcome to this hands-on lab, where 
you will learn how to perform Auto Scaling rolling updates. A common 
scenario is the need to perform updates on the instances in an Auto 
Scaling group. It is not possible to update launch configurations 
directly, which complicates updating Auto Scaling groups without 
incurring downtime. Using Auto Scaling rolling updates is the answer. 
Rolling updates allow you to update instances (where the instances are 
actually replaced), while allowing you to specify that at least 1 
instance is always active. In this way, downtime can be avoided while 
the update is taking place.

## Solution

1. Log in to AWS Management Console using the provided `cloud_user` credentials.
2. Once logged in, make sure you are using `us-east-1` (N. Virginia) as the selected region.
3. Download the [rolling update template](https://github.com/linuxacademy/content-aws-devops-pro/blob/master/Labs/AutoScalingRolling/rolling.json) from GitHub.

### Create a CloudFormation Stack

1. Navigate to EC2.
2. In the navigation pane, under **Network & Security**, choose **Key Pairs**.
3. Select **Create key pair**.
4. For **Name**, enter a name (for example, *rolling*).
5. For **Type**, select **RSA**.
6. Select `.pem` or `.ppk` as your private key file format.
7. Select **Create key pair**.
8. Save your key pair.
9. Navigate to CloudFormation.
10. Click **Create stack** > **With new resources (standard)**.
11. In the **Prepare template** section, select **Choose an existing template** and **Upload a template file**.
12. Click the **Choose file** button.
13. Select the `rolling.json` file that you previously downloaded (or if not, download it from [GitHub](https://github.com/linuxacademy/content-aws-devops-pro/blob/master/Labs/AutoScalingRolling/rolling.json) now).
14. Click **View in Application Composer** and select **Template** to look at the template.
15. Click **Create Template**.
16. Click **Confirm and continue to CloudFormation**.
17. On the **Create stack** page, click **Next**.
18. On the **Specify stack details** screen, for *Stack name*, enter `rollingupdatebaseline`.
19. Under **Ec2InstanceKeyName**, select your **rolling** key pair.
20. Under **VpcEc2Subnet1**, **VpcEc2Subnet2**, and **VpcEc2Subnet3**, choose each of the three subnets available.
21. Under **VpcId**, choose the only available option.
22. Click **Next**.
23. On the **Configure stack options** page, click **Next**.
24. On the **Review** page, click **Submit**.
25. Click the **Events** tab to monitor its progress. It will take about 5 minutes or so to finish creation.

### Update Auto Scaling Group

1. On the CloudFormation **rollingupdatebaseline** page, click the **Update** button.
2. On the **Update stack** page, under **Prepare template**, select **Edit in Application Composer**.
3. Click 'Edit in Application Composer' button
4. Click **Template**.
5. Insert the following `UpdatePolicy` in the template on line 111:
    
    ```
    "UpdatePolicy": {
        "AutoScalingRollingUpdate": {
            "MaxBatchSize": "4",
            "MinInstancesInService": "1",
            "PauseTime": "PT2M"
        }
    },
    
    ```
    
6. Click **Update Template**.
7. Click **Confirm and continue to CloudFormation**.
8. On the **Update stack** page, click **Next**.
9. On the **Specify stack details** page, under **Ec2InstanceType**, enter `t2.medium`.
10. Click **Next**.
11. On the **Configure stack options** page, click **Next**.
12. On the **Review** screen, click **Submit**.
13. Click the **Events** tab to monitor its progress. It will take about five minutes or so to finish updating.
14. In a new browser tab, navigate to **EC2** > **Instances**.
15. Observe what's happening with the listed instances. You may need to refresh the console by clicking the circular arrow button next to the **Connect** button.
16. In the CloudFormation console, verify the stack update is now complete.
17. In the EC2 console, verify there are now four running t2.medium instances.