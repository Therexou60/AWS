# Creating an IAM Role and Configuring an EC2 Instance for AWS Systems Manager via the AWS Management Console

## Introduction

In this hands-on lab, you will learn 
about the IAM role necessary for configuring an EC2 instance with the 
AWS Systems Manager service. We'll create and attach a role to an EC2 
instance via the AWS Management Console (GUI) and confirm that it is 
configured with the SSM service by checking in the Systems Manager 
console as a managed instance.

## Solution

Log in to the AWS environment using 
the credentials provided. Make sure you're in the N. Virginia 
(us-east-1) Region throughout the lab.

### Create an EC2 IAM Role with the Systems Manager Policy for EC2

1. Navigate to **IAM**.
2. From the left-side menu, under *Access management*, select **Roles**.
3. Click **Create role**.
4. For *Trusted entity type*, select **AWS service**.
5. For *Use case*, select **EC2**.
6. Select the **EC2 Role for AWS Systems Manager** option.
7. Click **Next**.
8. For *Role name*, enter **MySSMRole**.
9. Click **Create role**.

### Launch an EC2 Instance

1. Navigate to **EC2**.
2. From the left-side menu, select **Instances**.
3. Click **Launch instances**.
4. Under **Name** use **MySSMInstance**.
5. On the AMI page, select the latest version of the **Amazon Linux 2 AMI**.
6. Select **t2.micro** as the instance type.
7. In the key pair dialog, select **Proceed without a key pair**.
8. Under **Firewall (security groups)** choose **Select existing security group** and choose the **SG** security group only.
9. In the **Advanced details** section, under **IAM instance profile**, select **MySSMRole**.
10. Click **Launch instance**.
11. Click the instance id link, and give it a few minutes to enter the *Running* state. Keep this tab open.

### Verify the EC2 Instance Is Properly Configured with Systems Manager

1. Go back to **IAM** > **Roles**.
2. Search for and select **MySSMRole**.
3. Click the link for the attached policy under *Policy name* to view more information.
4. Click the **{}JSON** button, and view the items under `Action` to get an idea of how the policy is set up.
5. Go back to the tab with EC2 open to view the instance in a *Running* state.
6. Navigate to **Systems Manager**.
7. From the left-side menu, select **Fleet Manager**.
8. Confirm that **MySSMInstance** is correctly displayed in the *Managed nodes* list.