# Configure At Rest Encryption for EFS

## Introduction

AWS Elastic File System (EFS) provides
 incredible scalability for your storage requirements, making it easy to
 share data across hundreds or even thousands of EC2 instances. In this 
lab, students will create an Amazon Elastic File System in the US-East-1
 Region with encryption enabled. Students will also configure mount 
targets for this file system, enforce encryption in-flight, and finally 
mount the file system to a pre-created EC2 instance.

## Solution

Log in to the AWS Management Console using the credentials provided on the lab instructions page. Make sure you're using the *us-east-1* region.

### Create an EFS File System Named `devshare` with Encryption Enabled

1. In the search bar at the top, enter "efs".
2. Select **EFS** in the search results dropdown.
3. Select **Create file system** in the top-right corner.
4. Name the EFS file system `devshare`, leave other settings as default, and click **Create**.

### Create Mount Targets for EFS Using the EFSMountSecurityGroup in Each Availability Zone

1. Click on the `devshare` file system and navigate to the **Network** tab. Under *Mount target state*, you may see that the mount targets are still being created. It may take a few minutes until the state is *Available*.
2. Once the *Mount target state* is *Available*, click **Manage**.
3. Unassociate the default security group and select the **EFSMountSecurityGroup**.
4. Click **Save**.

### Apply a File System Policy to the EFS Volume to Enforce In-Transit Encryption for All Clients

1. Navigate to the **File system policy** tab and click **Edit** next to *Policy*.
2. Under *Policy options*, select **Enforce in-transit encryption for all clients**.
3. Click **Save**.

### Mount the EFS Volume to the EC2 Instance and Create a File on the Elastic File System

1. In the search bar at the top, enter "ec2".
2. Select **EC2** in the search results dropdown.
3. Click **Instances (Running)**.
4. Select **EFSManagementInstance** and click **Connect** in the upper-right.
5. Under **EC2 Instance Connect**, click **Connect** (leave *User name* as "root").

**Note:** If you encounter issues accessing EC2 Instance Connect, please log in to the server using the `cloud_user` credentials provided on the lab instructions page:

```
ssh cloud_user@<PUBLIC_IP_ADDRESS>

```

1. Create a mount point for the volume:
    
    ```
    sudo mkdir /efs
    
    ```
    
2. List the files:
    
    ```
    ls
    
    ```
    
3. Navigate to the root directory:
    
    ```
    cd /
    
    ```
    
4. List the files in the root directory:
    
    ```
    ls
    
    ```
    
5. Proceed with the mount command (navigate to the Amazon EFS Dashboard to copy the file system ID):
    
    ```
    sudo mount -t efs -o tls <FILE_SYSTEM_ID> efs/
    
    ```
    
6. Create a file in `llama.txt`:
    
    ```
    sudo touch /efs/llama.txt
    
    ```
    
7. Navigate to the EFS directory:
    
    ```
    cd /efs
    
    ```
    
8. List the files:
    
    ```
    ls
    
    ```
    
9. Navigate to the `/etc/fstab`:
    
    ```
    vim /etc/fstab
    
    ```
    

## Conclusion

Congratulations — you've completed this hands-on lab!