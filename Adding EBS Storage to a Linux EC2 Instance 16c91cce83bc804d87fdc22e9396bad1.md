# Adding EBS Storage to a Linux EC2 Instance

## Introduction

For this lab, you have been tasked to 
start an AWS EC2 instance running a specific Linux version and add 
additional EBS volumes to it. Upon completion of the lab, you will have 
the knowledge to start a new EC2 instance with additional storage, how 
to create an EBS volume, and attach an EBS volume to a running EC2 
instance.

## Solution

Log in to the AWS Management Console using the provided credentials. Make sure you're using the *us-east-1* region.

### Launch an EC2 Instance

1. Navigate to the **EC2 Dashboard**.
2. Click **Launch instance** and from the dropdown menu, select **Launch instance**.
3. For **Amazon Machine Image (AMI)**, select **Amazon Linux 2 AMI (HVM) - Kernel 5.10**.
4. Click **Select**.
5. For **Instance Type**, select **t2.micro**.
6. Click **Next: Configure Instance Details**, and leave the instance details as default.
7. Click **Next: Add Storage**.
8. Click **Add New Volume**.
9. Enter the following information:
    - **Volume Type:** *EBS*
    - **Device:** */dev/sdb*
    - **Size (GiB):** *5*
    - **Delete on Termination:** *check this box*
10. Click **Review and Launch**.
11. Review the instance settings and click **Launch**.
12. In the key pair dialog, select **Create a new key pair**.
13. Give it a *Key pair name* of *ebs demo*.
14. Click **Download Key Pair**.
15. Click **Launch Instances**.
16. Click **View Instances**, and give it a few minutes to enter the *Running* state.
17. Make note of the **Availability Zone** location for this instance.

### Create a New EBS Volume

1. On the left-side menu, select **Volumes**.
2. On the top right, click **Create volume**.
3. Enter the following information:
    - **Volume type:** *General Purpose SSD (gp2)*
    - **Size (GiB):** *8*
    - **Availability Zone:** *select the location of your instance*
4. Click **Create volume**, and you should see 3 volumes. *(**NOTE:** This process may take a few minutes to complete.)*

### Attach the Volume to the EC2 Instance

1. Select the newly created volume by checking the box. *(**NOTE:** Scroll to the left to verify the volume in the Available **Volume state**.)*
2. Click **Actions** and select **Attach volume**.
3. Click the dropdown arrow to select the **Instance**.
4. Select the running instance.
5. Click **Attach volume**.
6. On the left-side menu, select **Instances**.
7. Select the instance by checking the box.
8. On the top, click **Connect**.
9. Click **Connect** to use **EC2 Instance Connect**, and a new browser tab should open.
10. Verify the volume drives attached to the instance:
    
    ```
    lsblk
    
    ```
    

## Conclusion

Congratulations — you've completed this hands-on lab!