# Create and configure S3 file Gateway and create S3 Accross regions

# Guided Lab: Configuring Hybrid Storage and Migrating Data with AWS Storage Gateway S3 File Gateway

## Lab overview and objectives

In this lab, you use AWS Storage Gateway Amazon Simple Storage Service (Amazon S3) File Gateway to attach a network file system (NFS) mount to an on-premises data store. You then replicate that data to an S3 bucket on Amazon Web Services (AWS). Additionally, you configure advanced Amazon S3 features, such as Amazon S3 lifecycle policies and cross-Region replication.

After completing this lab, you should be able to do the following:

- Configure an S3 File Gateway with an NFS file share and attach it to a Linux instance.
- Migrate a set of data from the Linux instance to an S3 bucket.
- Create and configure a primary S3 bucket to migrate on-premises server data to AWS.
- Create and configure a secondary S3 bucket to use for cross-Region replication.
- Create an S3 lifecycle policy to automatically manage data in a bucket.

## Duration

This lab requires approximately **90 minutes** to complete.

## AWS service restrictions

In this lab environment, access to AWS services and service actions might be restricted to the ones that are needed to complete the lab instructions. You might encounter errors if you attempt to access other services or perform actions beyond the ones that this lab describes.

## Accessing the AWS Management Console

1. At the top of these instructions, choose  **Start Lab**.
    - The lab session starts.
    - A timer displays at the top of the page and shows the time remaining in the session.
        
        **Tip:** To refresh the session length at any time, choose  **Start Lab** again before the timer reaches 0:00.
        
    - Before you continue, wait until the circle icon to the right of the AWS  link in the upper-left corner turns green. When the lab environment is ready, the **AWS Details** panel displays.
2. To connect to the AWS Management Console, choose the **AWS** link in the upper-left corner, above the terminal window.
    - A new browser tab opens and connects you to the console.
        
        **Tip:** If a new browser tab does not open, a banner or icon is usually at the top of your browser with a message that your browser is preventing the site from opening pop-up windows. Choose the banner or icon, and then choose **Allow pop-ups**.
        
3. Arrange the AWS Management Console so that it appears alongside these instructions. Ideally, you should be able to see both browser tabs at the same time to follow the lab steps.
    
    Do not change the Region unless specifically instructed to do so.
    

## Task 1: Reviewing the lab architecture

This lab environment uses three AWS Regions. A Linux Amazon Elastic Compute Cloud (Amazon EC2) instance that emulates an on-premises server is deployed to the US East (N. Virginia) us-east-1 Region. The Storage Gateway virtual appliance is deployed to the same Region as the Linux server. In a real-world scenario, the appliance would be deployed in a VMware vSphere environment, in a Microsoft Hyper-V environment, or as a physical Storage Gateway appliance.

The primary S3 bucket is created in the US East (Ohio) us-east-2 Region. Data from the Linux host is copied to the primary S3 bucket. This bucket can also be called the source.

The secondary S3 bucket is created in the US West (Oregon) us-west-2 Region. This secondary bucket is the target for the cross-Region replication policy. It can also be called the destination.

The following architecture diagram shows the deployment of S3 File Gateway by using Storage Gateway services, an Amazon EC2 gateway appliance, and Amazon S3 cross-Region replication.

![https://labs.vocareum.com/web/3683978/3711369.0/ASNLIB/public/docs/lang/en_us/images/mod16-arc.png](https://labs.vocareum.com/web/3683978/3711369.0/ASNLIB/public/docs/lang/en_us/images/mod16-arc.png)

## Task 2: Creating the primary and secondary S3 buckets

Before you configure S3 File Gateway, you must create the primary S3 bucket (the source) where you replicate the data. You must also create the secondary S3 bucket (the destination) that is used for cross-Region replication.

1. On the AWS Management Console, in the search box, enter and choose `S3` to open the Amazon S3 console.
2. Choose **Create bucket**.
3. To create the primary (or source) bucket, on the **Create bucket** page, configure the following settings:
    - **AWS Region:** Choose the **US East (Ohio) us-east-2** Region.
    - **Bucket name:** Create a name that you can remember. It must be globally unique.
    - **Bucket Versioning:** Choose **Enable**.
    
     **Tip:** For cross-Region replication, you must enable versioning for both the source and destination buckets.
    
4. Choose **Create bucket**.
5. To create the secondary (or destination) bucket, repeat the previous steps in this task to configure the following options:
    - **AWS Region:** Choose the **US West (Oregon) us-west-2** Region.
    - **Bucket name:** Create a name that you can remember. It must be globally unique.
    - **Bucket Versioning:** Choose **Enable**.
6. Choose **Create bucket**.

## Task 3: Enabling cross-Region replication

Now that you have created your two S3 buckets and have enabled versioning on them, you create a replication policy.

1. Choose the name of the source bucket that you created in the US East (Ohio) us-east-2 Region.
2. Choose the **Management** tab, and in the **Replication rules** section, choose Create replication rule.
3. On the **Create replication rule** page, configure the following options:
    - In the **Replication rule configuration** section, configure the following options:
        - For **Replication rule name**, enter `crr-full-bucket`.
        - For **Status**, choose **Enabled**.
    - In the **Source bucket** section, for **Choose a rule scope**, choose **Apply to all objects in the bucket**.
    - In the **Destination** section, configure the following options:
        - Choose **Choose a bucket in this account**.
        - Choose Browse S3, and then choose the destination bucket that you created in the US West (Oregon) us-west-2 Region.
        - Choose Choose path.
    - In the **IAM role** section, in the search box, enter `S3-CRR` and choose **S3-CRR-Role**. (This role was pre-created with the required permissions for this lab.)
4. Choose **Save**.

**Note:** You might see a **Replicate existing objects?** window with a message about enabling a one-time batch operations job from this replication configuration. Leave the default option selected, and choose **Submit**.

1. Return to the source bucket that you created in the US East (Ohio) us-east-2 Region.
2. To upload a file from your local computer to the bucket, choose Upload.
    
    For this lab, use a small file that does not contain sensitive information, such as a blank text file.
    
3. Choose **Add files**, navigate to the file, and choose your file.
4. Choose **Upload**.
5. Wait for the file to upload, and then return to the destination bucket that you created in the US West (Oregon) us-west-2 Region. The file that you uploaded should be copied to this bucket.
    
    **Note:** For the object to appear, you might need to refresh  the console.
    

## Task 4: Configuring the S3 File Gateway and creating an NFS file share

In this task, you deploy the S3 File Gateway appliance as an EC2 instance. You then configure a cache disk, choose an S3 bucket to synchronize your on-premises files to, and choose an AWS Identity and Access Management (IAM) policy to use. Finally, you create an NFS file share on the S3 File Gateway.

1. On the AWS Management Console, in the search box, enter and choose `Storage Gateway` to open the Storage Gateway console.
2. In the upper-right of the console, verify that the current Region is **N. Virginia**.
3. Choose **Create gateway**, and then configure the following settings for **Step 1: Set up gateway**:
    - **Gateway name:** Enter `File Gateway`.
    - **Gateway time zone:** Choose **GMT -5:00 Eastern Time (US & Canada), Bogota, Lima**.
    - **Gateway type:** Choose **Amazon S3 File Gateway**.
    - **Host platform:** Choose **Amazon EC2**, and then for **Launch EC2 instance** choose **Customize your settings**.
4. In the **Set up gateway on Amazon EC2** section, choose **Launch instance**.
    
    A new browser tab opens to the Amazon EC2 launch instance wizard on the Amazon EC2 console. This link automatically chooses the correct Amazon Machine Image (AMI) that must be used for the S3 File Gateway appliance.
    
5. On the **Launch an instance** page, configure the following options:
    - In the **Name and tags** section, for **Name**, enter `File Gateway Appliance`.
    - In the **Application and OS Images (Amazon Machine Image)** section, for **AMI from catalog**, accept the default **aws-storage-gateway** AMI.
    - In the **Instance type** section, from the **Instance type** dropdown list, choose the **t2.xlarge** instance type.
        
        **Note:** t2.xlarge is the only instance type that you can choose in this lab environment. Choosing any other instance type results in an error message when you attempt to launch the instance.
        
        **Tip:** The t2.xlarge instance type is used only as an example in this lab. For more information about correct appliance sizing when you deploy a Storage Gateway appliance, see the [Storage Gateway documentation](https://docs.aws.amazon.com/storagegateway/latest/userguide/Requirements.html#requirements-hardware-storage).
        
    - In the **Key pair (login)** section, for **Key pair name - *required***, choose the existing **vockey** key pair.
        
        **Note:** This SSH key pair is provided with the instructions for this lab. To see the kay pair, at the top of these instructions, choose **Details**, and then choose **Show**.
        
        Next, you configure the network and security group settings for the instance.
        
6. On the same page, in the **Network settings** section, choose **Edit**, and then configure the following options:
    - For **VPC - *required***, choose **On-Prem-VPC**.
    - For **Subnet**, choose **On-Prem-Subnet**.
    - For **Auto-assign public IP**, choose **Enable**.
    - For **Firewall (security groups)**, choose **Select existing security group**.
    - For **Common security groups**, configure the following options:
        - From the dropdown list, choose the security group with **FileGatewayAccess** in the name.
        
        **Note**: This security group is configured to allow traffic through ports 80 (HTTP), 443 (HTTPS), 53 (DNS), 123 (NTP), and 2049 (NFS). These ports enable the activation of the S3 File Gateway appliance. They also enable connectivity from the Linux server to the NFS share that you create on the S3 File Gateway.
        
        For more information about the ports used by Storage Gateway, see the [Storage Gateway documentation](https://docs.aws.amazon.com/storagegateway/latest/userguide/Requirements.html#networks).
        
        - Also choose the security group with **OnPremSshAccess** in the name.
        
        **Note**: This security group is configured to allow Secure Shell (SSH) connections on port 22.
        
        - Verify that you have chosen both security groups. Details on each security group appear in boxes in the console.
        
        **Tip**: To see both security groups, you might need to choose **Show all selected**.
        
        Next, you configure the storage settings for the instance.
        
7. On the same page, in the **Configure storage** section, notice that there is already an entry to create one 80 GiB gp3 root volume, and configure the following options:
    - Choose **Add new volume**.
    - For the size of the volume, enter `150` GiB.
8. On the same page, in the **Summary** panel, keep the number of instances set to 1, and choose **Launch instance**.
    
    A *Success* message displays.
    
9. Choose View all instances.
    
    Your **File Gateway Appliance** instance will take a few minutes to initialize.
    
    Monitor the status of the deployment, and wait for the **Status Check** to complete and say *2/2 checks passed*.
    
    **Tip:** To check the status of the instance, choose refresh .
    
10. Choose your **File Gateway Appliance** instance.
11. On the **Details** tab, locate the **Public IPv4 address**, and copy it.
    
    You use this IP address when you complete the S3 File Gateway deployment.
    
12. Return to the Storage Gateway browser tab. It should still be on the **Set up gateway on Amazon EC2** section.
13. At the bottom of the page, for **Confirm set up gateway**, select **I completed all the steps above and launched the EC2 instance**.
14. Choose Next.
15. For **Step 2: Connect to AWS**, configure the following options:
    - In the **Gateway connection options** section, for **IP address**, enter the public IPv4 address that you copied in the previous steps from your **File Gateway Appliance** instance.
    - In the **Endpoint options** section, for **Service endpoint**, choose **Publicly accessible**.
16. Choose Next.
17. For **Step 3: Review and activate**, choose **Activate gateway**.
    
    A *Successfully activated gateway File Gateway* message displays.
    
    On the **Step 4: Configure gateway** page, in the **Configure cache storage** panel, you see a message indicating that the local disks are loading.
    
    Wait for the local disks status to show that it finished processing (approximately 1 minute) before continuing to the next step.
    
18. For **Step 4: Configure gateway**, configure the following options:
    - **CloudWatch log group:** Choose **Deactivate logging**.
    - **CloudWatch alarms:** Choose **No alarm**.
19. Choose Configure.
    
    A *Successfully created gateway File Gateway* message displays.
    
    Wait for the **Status** of the File Gateway to change to *Running* (approximately 1–2 minutes). Once the status is *Running*, you create a file share.
    
20. Select **File Gateway**, and then choose **Create file share**.
21. On the **Create file share** configuration page, choose **Customize configuration**.
22. For **Step 1: File share settings**, configure the following options:
    - For **Gateway**, choose the name of the S3 File Gateway that you just created (which should be **File Gateway**).
    - For **Amazon S3 bucket name**, enter the name of the source bucket that you created in the US East (Ohio) us-east-2 Region.
    - For **AWS Region**, choose **US East (Ohio) us-east-2**.
    - For **Access objects using**, choose **Network File System (NFS)**.
23. Choose **Next**.
24. For **Step 2: Amazon S3 storage settings**, configure the following options:
    - **Storage class for new objects:** Choose **S3 Standard**.
    - **Object metadata**: Select only **Guess MIME type** and **Gateway files acccessible to S3 bucket owner**.
    - **Access your S3 bucket**: Choose **Use an existing IAM role**.
    - **IAM role:** To enter the value for **FgwIamPolicyARN**, follow these instructions:
        - At the top of these instructions, choose Details.
        - Choose Show.
        - Copy the **FgwIamPolicyARN** value, and paste it in the **IAM role** box on the **Step 2: Amazon S3 storage settings** page.
25. Choose **Next**.
    
    **Note:** On the **Step 3: File access settings** page, you might get a warning message indicating that the file share is accessible from anywhere. For this lab, you can safely disregard this warning. In a production environment, you should always create policies that are as restrictive as possible to prevent unwanted or malicious connections to your instances.
    
26. On the **Step 3: File access settings** page, accept the default settings, and choose **Next**.
27. At the bottom of the **Step 4: Review and create** page, choose **Create**.
    
    Monitor the status of the deployment, and wait for the **Status** to change to *Available*, which takes less than 1 minute.
    
    **Note:** To check the status, choose refresh .
    
28. Choose the file share that you just created.
29. From **Example Commands**, copy the command for **On Linux**, which is similar to the following:
    
    `mount -t nfs -o nolock,hard 10.10.1.124:/lab36ohiouseast2 [MountPath]*`
    
    You need this command in the next task.
    

## Task 5: Mounting the file share to the Linux instance and migrating the data

Before you can migrate data to the NFS share that you created, you must first mount the share. In this task, you mount the NFS share on a Linux server and then copy data to the share.

1. To connect to the **On-Prem Linux Server** instance, at the top of these instructions, choose **Details** and then choose **Show**.
2. Copy the value for **InstanceSessionURL**, and paste it into a new browser tab to connect to the EC2 instance by using Session Manager, a capability of AWS Systems Manager.
    
    You should now be connected to the instance.
    
3. On the Linux instance, to view the data that exists on this server, run the following command:
    
    ```
    
    sudo su -l ec2-userls /media/data
    ```
    
    You should see 20 image files in the .png format.
    
4. To create the directory that will be used to synchronize data with your S3 bucket, run the following command:
    
    `sudo mkdir -p /mnt/nfs/s3`
    
5. To mount the file share on the Linux instance, enter the command that you copied at the end of the previous task. In the command, replace the value for *[MountPath]* with the following:
    
    `/mnt/nfs/s3*`
    
6. Use `sudo` to run your adjusted command. Your adjusted command should look similar to the following:
    
    `sudo mount -t nfs -o nolock,hard 10.10.1.33:/lab-nfs-bucket /mnt/nfs/s3`
    
7. To verify that the share was mounted correctly, run the following command:
    
    `df -h`
    
    The output of the command should be similar to the following example:
    
    ```
    
    [ec2-user@ip-10-10-1-210 ~]$ df -hFilesystem                  Size  Used Avail Use% Mounted ondevtmpfs                    483M   64K  483M   1% /devtmpfs                       493M     0  493M   0% /dev/shm/dev/xvda1                  7.8G  1.1G  6.6G  14% /10.10.1.33:/lab-nfs-bucket  8.0E     0  8.0E   0% /mnt/nfs/s3
    ```
    
    You have now created the mount point.
    
8. To copy the data that you want to migrate to Amazon S3 into the share, run the following command:
    
    `cp -v /media/data/*.png /mnt/nfs/s3`
    

## Task 6: Verifying that the data is migrated

You have finished configuring the gateway and copying data into the NFS share. Now, you verify that the configuration works as intended.

1. On the AWS Management Console, in the search box, enter and choose `S3` to go to the Amazon S3 console.
2. Choose the source bucket that you created in the US East (Ohio) us-east-2 Region, and verify that the 20 image files are listed.
    
    **Note:** In the console, you might need to choose refresh .
    
3. Return to the S3 buckets page, and choose the destination bucket that you created in the US East (N. Virginia) us-east-1 Region. Verify that the images files replicated to this bucket based on the policy that you created earlier.
    
    **Note:** Amazon S3 object replication can take up to 15 minutes to complete. Continue to refresh the page until you see the replicated objects.
    

## Conclusion

Congratulations! You now have successfully done the following:

- Configured an S3 File Gateway with an NFS file share and attached it to a Linux instance
- Migrated a set of data from the Linux instance to an S3 bucket
- Created and configured a primary S3 bucket to migrate on-premises server data to AWS
- Created and configured a secondary S3 bucket to use for cross-Region replication
- Created an S3 lifecycle policy to automatically manage data in a bucket

## Submitting your work

1. At the top of these instructions, choose Submit to record your progress.
2. When prompted, choose **Yes**.
3. If the results don't display after a couple of minutes, return to the top of these instructions, and choose Grades.
    
    **Tip**: You can submit your work multiple times. After you change your work, choose **Submit** again. Your last submission is what will be recorded for this lab.
    
4. To find detailed feedback on your work, choose Details, and then choose  **View Submission Report**.

## Lab complete

1. At the top of this page, choose  **End Lab**, and then choose Yes to confirm that you want to end the lab.
    
    The following message briefly appears: *Ended AWS Lab Successfully*
    

For more information about AWS Training and Certification, see [AWS Training and Certification](http://aws.amazon.com/training/).

*Your feedback is welcome and appreciated.*

If you would like to share any suggestions or corrections, provide the details in the [AWS Training and Certification Contact Form](https://support.aws.amazon.com/#/contacts/aws-training).

*© 2023, Amazon Web Services, Inc. or its affiliates. All rights reserved. This work may not be reproduced or redistributed, in whole or in part, without prior written permission from Amazon Web Services, Inc. Commercial copying, lending, or selling is prohibited.*