# Configure and Work with CodeCommit from the CLI

## Introduction

In this hands-on lab, you go through 
the process of configuring a CodeCommit repository from the AWS Command 
Line Interface. You also go through some key prerequisite steps, such as
 installing Git, configuring HTTPS credentials, and preparing the 
environment to work with Git and CodeCommit. This lab covers the steps 
that need to be taken any time you work with CodeCommit from the CLI.

## Solution

Log in to the AWS Management Console using the credentials provided on the lab instructions page. Make sure you're using the `us-east-1` Region.

### Create a Server from an Amazon Linux 2 AMI

1. Navigate to the EC2 dashboard.
2. Click on the **Launch Instance** dropdown menu > **Launch Instance**.
3. Under **Name and tags**, name the instance *webserver*.
4. Under **Application and OS Images (Amazon Machine Image)**, ensure the **Amazon Linux AWS** tile is selected and **Amazon Linux 2 AMI** is selected from the dropdown menu directly underneath the tile.
5. Under **Instance type**, click on the dropdown menu and select **t3.micro**.
6. Under **Key pair (login)** > **Key pair name**, click on the dropdown menu and select **Proceed without a key pair (Not recommended)**.
7. Under **Network Settings**, click **Edit**.
8. Under **Auto-assign public IP**, click on the dropdown menu and select **Enable**.
9. Leave the other settings at default, and click **Launch instance**.
10. Once the instance is launched, click on the instance ID to navigate to the instance; ensure the **Instance state** is running.
    
    > Note: It my take a few minutes for the instance to deploy.
    > 

### Install Git and Create IAM CodeCommit Credentials

1. Click on the checkbox next to **webserver** and, in the upper right, click **Connect**.
2. Click **Connect** again.
3. Once connected to the instance, update the server:
    
    ```
    sudo yum update -y
    
    ```
    
4. Install Git:
    
    ```
    sudo yum install git -y
    
    ```
    
5. Return to the the AWS Management Console.
6. Navigate to **IAM** (don't worry about the errors).
7. In the left navigation menu, under **Access management**, click **Users**.
8. Click on **cloud_user**.
9. In the **Permissions** tab under **Policy name**, note the you already have a policy granting permission to work with CodeCommit.
10. Click **Add permissions** > **Add permissions**
11. Under **Permission options**, select **Attach policies directly**.
12. Type *CodeCommit* in the filter search box, and take note of the`AWSCodeCommitFullAccess` policy.
13. Click **Cancel**. (We are clicking cancel here because the `cloud_user` already has the needed permissions, and the lab's `cloud_user` account cannot be modified).

### Generate an Access Key

1. Click the **Security credentials** tab.
2. Under **Access keys**, click **Create access key**.
3. Under **Access key best practices & alternatives**, select **Command Line Interface (CLI)**.
4. Scroll down and click on the checkbox next to **I understand the above recommendation and want to proceed to create an access key**.
5. Click **Next**.
6. Click **Create access key**.
7. Copy the access key and secret access key, and save them to a separate text file; you'll need them later.
8. Click **Done**, then click **Continue** on the pop-up.

### Generate HTTPS Git Credentials for AWS CodeCommit

1. Under **HTTPS Git credentials for AWS CodeCommit**, click **Generate credentials**.
2. In the **Generate credentials** pop-up, copy the user name and password and save them to a separate text file; you'll need them later.
3. Click **Close**.

### Create a CodeCommit Repository

1. Return to the terminal window.
2. Configure the AWS CLI credentials:
    
    ```
    aws configure
    
    ```
    
3. When prompted, enter the AWS access key, then the secret access key you copied earlier.
4. For the `Default region name`, enter `us-east-1`.
5. For the `Default output format`, enter `json`.
6. Run the following command to create a CodeCommit repository from the AWS CLI:
    
    ```
    aws codecommit create-repository --repository-name RepoFromCLI
    
    ```
    

### Clone the Repo

1. Back in the AWS console, In the search bar at the top, enter *CodeCommit*; right-click on it and open in a new tab.
2. Click on the **RepoFromCLI** repository link to open it.
3. Click **Add file**.
4. Click **Upload file**.
5. Click **Choose file**.
6. Select any small file from your machine you don't mind uploading.
7. Enter your name under *Author name* and your email under *Email address*.
8. Click **Commit changes**.
9. Return to your terminal window.
    
    > Note: If your connection timed out, go back to the Connect to instance browser tab and click Connect again.
    > 
10. Back in the **CodeCommit** browser tab, click on **Repositories** in the breadcrumb trail.
11. Under **Clone URL**, copy the **HTTPS** hyperlink.
12. Return to your terminal window.
13. Run the following command to clone the repository to the server:
    
    ```
    git clone <PASTE_THE_URL>
    
    ```
    
14. For the username prompt, enter the Git credentials username you copied earlier.
15. For the password prompt, enter the Git credentials password you copied earlier.
16. Check the clone:
    
    ```
    ls
    
    ```
    
17. Go into the repo directory:
    
    ```
    cd RepoFromCLI/
    
    ```
    
18. Confirm the file you uploaded earlier is in the repo:
    
    ```
    ls
    
    ```
    

### Test the Repo

1. Run the following command to create a local text file:
    
    ```
    vim test.txt
    
    ```
    
2. Press `i` to enter insert mode and type the text:
    
    ```
    This is a test file.
    
    ```
    
3. Press the **Escape** key, type `:wq!`, and press **Enter**.
4. Confirm the `test.txt` file was created:
    
    ```
    ls
    
    ```
    
5. Add the file to Git:
    
    ```
    git add test.txt
    
    ```
    
6. Commit the file to the local repository:
    
    ```
    git commit -m "added test.txt"
    
    ```
    
7. Verify the file was committed:
    
    ```
    git log
    
    ```
    
8. Push the change to the CodeCommit repository:
    
    ```
    git push -u origin main
    
    ```
    
9. Enter the Git credentials username you copied earlier for the username prompt.
10. Enter the Git credentials password you copied earlier for the password prompt.
11. Back in the **CodeCommit** browser tab, refresh the view of the **RepoFromCLI** repository and verify the new file was uploaded.

## Conclusion

Congratulations on successfully completing this hands-on lab!