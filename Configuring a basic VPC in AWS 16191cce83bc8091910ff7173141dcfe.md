# Configuring a basic VPC in AWS

## Introduction

In this hands-on lab scenario, you’re a
 cloud network engineer tasked with setting up the security and network 
architecture for your organization's production environment. You'll have
 the opportunity to explore and understand the relationship between 
networking components. We will create a virtual private cloud (VPC), 
subnets across multiple availability zones (AZs), routes, and an 
internet gateway, as well as adding security using security groups and 
network access control lists (NACLs). These services are the foundation 
of networking architecture inside of AWS, and this lab will cover 
concepts such as infrastructure, design, routing, and security.

## Solution

Log in to the AWS environment using the lab credentials provided. Make sure to use the N. Virginia (`us-east-1`) Region for this lab.

If you are using PuTTY to connect to EC2 instances, use [these instructions](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html).

For help troubleshooting your SSH connection, [click here](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html).

### Create a VPC

> A Virtual Private Cloud (VPC) is a 
virtual network dedicated to your AWS account where you can launch AWS 
resources in a logically isolated section of the AWS cloud.
> 
1. In the AWS Management Console, in the search bar at the top of the page, type *VPC* and select **VPC** from the dropdown menu.
2. In the VPC console, in the left-hand menu, select **Your VPCs**.
3. On the **Your VPCs** page, click **Create VPC** in the top right.
4. On the **Create VPC** page, fill in the following fields:
    - **Resources to create**: Select **VPC Only**
    - **Name tag - *optional***: Type in **HoLVPC**.
    - **IPv4 CIDR block**: Select **IPv4 CIDR manual input**.
    - **IPv4 CIDR**: Type *10.0.0.0/16*.
    - **IPv6 CIDR block**: Select **No IPv6 CIDR block**.
    - **Tenancy**: Select **Default**.
5. Scroll down and click **Create VPC**.

> Note: Windows users who will be using PuTTY will need to take the following, additional steps:
> 
> 1. After your new VPC has been created, click **Actions** > **Edit VPC settings**.
> 2. Towards the bottom of the page, check the box next to **Enable DNS hostnames**.
> 3. Click **Save**.

### Create a Public and Private Subnet

> Subnets are segments of the VPC that 
divide the network into smaller, more manageable pieces, typically 
designated as public or private for better security and organization. A 
public subnet in AWS is a subnet that has a route table with a route to 
an Internet Gateway, allowing resources within it to directly 
communicate with the Internet.
> 

### Create the Public Subnet

1. From your newly-created VPC page, in the left-hand menu, select **Subnets**.
2. On the **Subnets** page, click **Create subnet**.
3. On the **Create subnet** page, under **VPC ID**, click into the empty field, and then choose the one **(HoLVPC)** option.
4. Under the **Subnet settings**, set the following fields:
    - **Subnet Name**: Type *hol-public-a*
    - **Availability Zone**: Select **us-east-1a**
    - **IPv4 CIDR block**: Type *10.0.1.0/24*
5. Below the **Subnet settings**, click **Add new subnet**.

### Create the Private Subnet

1. Under **Subnet 2 of 2**, set the following fields:
    - **Subnet Name**: Type *hol-private-b*
    - **Availability Zone**: Select **us-east-1b**
    - **IPv4 CIDR block**: Type *10.0.2.0/24*
2. At the bottom of the page, click **Create Subnet**.

### Create Routes and Internet Gateway

### Enable Auto-assign public IPv4 addresses

1. From the **Subnets** page, in the left-hand menu, select **Subnets**.
2. On the **Subnets** page, click the checkbox next to *hol-public-a* subnet to select it.
3. In the top right-hand corner of the page, click **Actions** > **Edit subnet settings**.
4. On the **Edit subnet settings** page, under **Auto-assign IP settings**, click the checkbox next to **Enable auto-assign public IPv4 address**.
5. Click **Save** at the bottom of the page.

### Configure an Internet Gateway

> Internet Gateways allows communication
 between instances in your VPC and the internet, enabling internet 
access for your resources.
> 
1. From the **Subnets** page, in the left-hand menu, select **Internet gateways**.
2. On the **Internet gateways** page, click **Create internet gateway** in the top right.
3. On the **Create internet gateway** page, under **Name tag**, type *hol-VPCIGW*.
4. Click **Create internet gateway** at the bottom of the page.
5. From the internet gateway's page, in the top right, click on **Actions** > **Attach to VPC**.
6. On the **Attach to VPC** page, click into the empty field under **Available VPCs**.
7. Choose the one option that says **HoLVPC** at the end.
8. Click **Attach internet gateway** at the bottom of the page.

### Configuring a Public Route Table

> Route Tables in AWS contain a set of 
rules (routes) that determine where network traffic from associated 
subnets are directed, such as to the internet via an Internet Gateway or
 between subnets.
> 
1. From the internet gateway's page, in the left-hand menu, select **Route tables**.
2. Click **Create route table** in the top right.
3. On the **Create route table page**, under **Name - *optional***, type the name *publicRT*.
4. Under **VPC**, click into the empty field, and then select the one **(HoLVPC)** option.
5. Click **Create route table** at the bottom of the page.
6. On the route table's page, halfway down the page, on the **Routes** tab, click **Edit routes** on the right.
7. On the **Edit routes** page, click **Add Route**.
8. For the new route, under **Destination**, type *0.0.0.0/0*.
9. Under **Target**, select **Internet Gateway**, and then click again on the **igw-** that ends with **(hol-VPCIGW)**.
10. Click **Save changes**.

### Associate the Public Route Table with the Public Subnet

1. From the route table's page, halfway down the page, click the **Subnet associations** tab.
2. On the **Subnet associations** tab, in the **Explicit subnet associations** section, click **Edit subnet associations**.
3. Click the checkbox next to the **hol-public-a** subnet.
4. Click **Save associations**.

### Launch EC2 Instances in the Subnets

### Launch an EC2 Instance in the Public Subnet

> EC2 Instances are virtual servers in the cloud where you can run applications and workloads.
> 
1. From the route table's page, navigate to the **EC2** service by typing *EC2* into the search bar at the top of the page and selecting **EC2** from the dropdown.
2. In the left-hand menu, click **Instances**.
3. On the **Instances** page, select **Launch instances** in the top right.
4. On the **Launch an instance** page, in the name field, type *hol-pub-instance*.
5. In the **Application and OS Images** section, make sure, by default, that **Amazon Linux 2** is selected for the **Amazon Machine Image (AMI)** and make sure the **Architecture** is set to **64-bit (x86)**.
6. In the **Instance Type** section, select **t3.micro** from the dropdown.
7. In the **Key pair (login)** section, click **Create new key pair**.
8. In the **Create key pair** window, for the **Key pair name**, type *vpcpubhol*.
9. Leave the **Key pair type** set to **RSA**.
10. For the **private key file format**, choose either **.pem** or **.ppk**, depending on if you will use PuTTY or OpenSSH to connect to the EC2 instance later on.
    
    > Note: Use .pem for a mac device; use .ppk for a Windows device.
    > 
11. Click **Create key pair**.
12. Back on the **Launch an instance** page, in the **Network settings** section, click **Edit**.
13. Make sure that, by default, the **VPC** is set to a **vpc-** that ends in **(HoLVPC)**, and the **Subnet** is set to the **hol-public-a** subnet.
14. Under **Security group name**, change the name, by typing in *holpubSG*.
15. Under **Description - *required***, type *holpubSG*.
16. Under **Security Group rule 1**, set the following fields:
    - **Type**: Select **ssh**
    - **Source type**: **My IP**
17. Under **Source**, take
note of the IP address, as you will need it for the last objective. Copy and paste it to a text editor to keep it handy for later in the lab.
18. Click **Add security group rule**.
19. Under **Security Group rule 2**, set the following fields:
    - **Type**: Select **ssh**
    - **Source type**: Select **Custom**
    - **Source**: Type *10.0.0.0/16*
20. Leave all other settings as the default, and click **Launch instance**.
    
    > Note: Your vpcpubhol .pem or .ppk key pair file should have downloaded. You will use this file later in the lab.
    > 
21. Go back to the **Instances** section of the EC2 dashboard.
22. On the **Instances** page, hit the refresh icon at the top of the screen to check the progress of the **hol-pub-instance**.

### Launch an EC2 Instance in the Private Subnet

1. From the **Instances** page, click **Launch instances** in the top right.
2. On the **Launch an instance** page, in the **Name** field, type *hol-priv-instance*.
3. In the **Application and OS Images** section, make sure, by default, that **Amazon Linux 2** is selected for the **Amazon Machine Image (AMI)** and make sure the **Architecture** is set to **64-bit (x86)**.
4. In the **Instance Type** section, select **t3.micro** from the dropdown.
5. In the **Key pair (login)** section, click **Create new key pair**.
6. In the **Create key pair** window, in the **Key pair name**, type *vpcprivhol*.
7. Leave the **Key pair type** set to **RSA**.
8. For the **Private key file format**, choose either **.pem** or **.ppk**, depending on if you will use PuTTY or OpenSSH to connect to the EC2 instance later on.
    
    > Note: Use .pem for a mac device; use .ppk for a Windows device.
    > 
9. Click **Create key pair**.
10. Back on the **Launch an instance** page, in the **Network settings** section, click **Edit**.
11. Make sure that, by default, the **VPC** is set to a **vpc-** that ends in **(HoLVPC)**, and the **Subnet** is set to the **hol-private-b** subnet.
12. Under **Security group name**, change the name by typing in *holprivSG*.
13. Under **Description - *required***, type *holprivSG*.
14. Under **Security Group rule 1**, set the following fields:
    - **Type**: Select **ssh**
    - **Source type**: Select **Custom**
    - **Source**: Type *10.0.0.0/16*
15. Leave all other settings as the default, and click **Launch instance**.
    
    > Note: Your vpcprivhol .pem or .ppk key pair file should have downloaded. You will use this file later in the lab.
    > 
16. Once your instance has launched, click **View all instances**.
17. On the **Instances** page, hit the refresh icon to check the progress of the **hol-priv-instance**.

### SSH into the Public Instance

1. From the list of instances in the EC2 console, right-click on the **hol-pub-instance** and click **Connect**.
2. On the **Connect to instance** page, select the **SSH client** tab.
3. On the **SSH client** tab, directly underneath **Example:**, copy the connection command to your clipboard. (You can optionally
paste it into a text editor to keep it handy, as you will need to use it several times in the lab.)
4. Open your SSH client.
5. Locate your private key file on your local machine that you downloaded earlier, `vpcpubhol.pem` (or `.ppk`). If it is in your local Downloads folder, you can run `cd ~/Downloads`.
6. Run the command `chmod 400 vpcpubhol.pem`, to ensure your key is not publicly viewable.
    - Linux/macOS users will need to run a `chmod 400 vpcpubhol.pem` command first to avoid errors.
    - Windows users can connect using [this as a guide](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html).
7. Connect to the public instance by running the connection command you copied to your clipboard earlier.
8. Type in *yes* to the connection prompt.
    
    You should be able to connect.
    

### SSH into the Private Instance

1. On the public instance, create a private key, as that will be used for the SSH connection. Run `vi vpcprivhol.pem` (or `.ppk`) to open Vim and create a new blank `.pem` file.
2. Enter insert mode of Vim by pressing `i`.
3. Open your downloaded file, `vpcprivhol.pem` (or `.ppk`), in a text editor of your choice and copy the key to your clipboard.
4. Back in your terminal, paste the contents (use `Command + V` on Mac and `Ctrl + Shift + V` on Linux to paste).
5. Press **Escape** to exit insert mode and type `:wq` to save the file and quit.
6. Run `chmod 400 vpcprivhol.pem` (or `.ppk`) to ensure your key is not publicly viewable.
7. Return to the AWS console, and navigate back to your list of instances by clicking **Instances** in the navigation line at the top left of the page.
8. On the **Instances** page, click the checkbox next to **hol-priv-instance** to select it.
9. On the bottom half of the page, in the **Details** tab, copy the private IPv4 address of the instance.
10. Back in the terminal, run `ssh -i "vpcprivhol.pem" ec2-user@<PRIVATE IP>`, (or `.ppk` for Windows users) making sure to replace `<PRIVATE IP>` with the IP address you just copied.
11. Type in *yes* to the connection prompt.
    
    You should be able to connect.
    
12. After connecting to the private instance, go ahead and close out your connect.

### Add a Network ACL

> A Network ACL (NACL) is an optional 
layer of security for your VPC that acts as a firewall for controlling 
inbound and outbound traffic at the subnet level.
> 
1. Return to the AWS console, and navigate to **VPC** console, by typing *VPC* into the search bar at the top of the screen and select **VPC** from the dropdown.
2. In the VPC console, in the left-hand menu, select **Network ACLs**.
    
    You should see one default Network ACL listed.
    
3. Click the checkbox next to the one default NACL to select it.
4. In the top right, click **Actions** > **Edit inbound rules**.
5. Click **Add new rule** and set the following values for the new rule:
    - **Rule number**: Type *50*
    - **Type**: Select **All traffic**
    - **Source**: Paste in the IP address you copy and pasted into a text editor earlier in the lab (Or you can get it by googling *What is my IP* in a new browser tab), and append */32* at the end
    - **Allow/Deny**: Select **Deny**
6. Click **Save changes**.
7. In your SSH terminal, navigate back to `Downloads` directory, then try to connect and log in to your public EC2 instance
using the same command that you used to log in earlier. (You may have
pasted this into a text editor for later use).
    
    You should receive an error.
    
8. Return to the AWS console, and on the **Network ACLs** page, click **Actions** > **Edit inbound rules**.
9. On the **Edit inbound rules** page, click **Remove** next to rule 50.
10. Click **Save changes**.
11. Return to your SSH client, and try
again to connect and log in to your public EC2 instance, using the same
command you just used.
    
    You should be able to connect now.