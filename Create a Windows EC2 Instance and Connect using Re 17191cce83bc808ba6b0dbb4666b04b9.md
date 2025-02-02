# Create a Windows EC2 Instance and Connect using Remote Desktop Protocol (RDP)

## Introduction

Typically, you will connect to a Linux
 instance via SSH. But when connecting to an EC2 instance running a 
Windows operating system, you usually connect with RDP (Remote Desktop 
Protocol).

In this lab, we'll create a Windows 
EC2 instance and connect using RDP. We will configure a security group 
to allow the RDP protocol and associate the security group with a 
Windows EC2 instance we will create. Once the instance is created, we'll
 use Remote Desktop to connect to the instance.

If you're using a Windows machine, you already have an RDP client.

If you're using a Mac computer, download and install the RDP client [here](https://itunes.apple.com/us/app/microsoft-remote-desktop/id1295203466?mt=12).

Linux users can use the following program for RDP:
[here](https://remmina.org/)

## Verify the NACL and Security Group Configuration

### Verify RDP Traffic Is Allowed Out to the Internet

First, we'll verify our VPC, internet 
gateway, route table, and Network Access Control List are configured 
correctly to allow RDP traffic out to the internet.

1. In the AWS Management Console, navigate to VPC.
2. Click **Your VPCs** in the left-hand menu, and we'll see there's a *LinuxAcademy* VPC already listed.
3. Click **Subnets** in the left-hand menu, and we'll see there are two subnets listed.
4. Click **Internet Gateways** in the left-hand menu, and we'll see one is listed and attached to the VPC.
5. Click **Route Tables** in the left-hand menu, and select the route table that's associated with two subnets.
6. Click the *Routes* tab at the bottom, and we'll see the internet gateway is attached to the route.
7. Click **Network ACLs** in the left-hand menu, and select the NACL that's associated with two subnets.
8. Click the *Inbound Rules* tab, and we'll see RDP traffic is allowed through our NACL.
9. Click the *Outbound Rules* tab, and we'll see all TCP ports are allowed.
10. Click **Security Groups** in the left-hand menu, and select the one listed.
11. Click the *Inbound Rules* and *Outbound Rules* tab, and notice all traffic is allowed in and out.

Those all look good. Let's move on.

### Create New Security Group and Allow Inbound RDP Traffic into It

Now, we'll create a new security group and allow inbound RDP traffic (port 3389) into our security group.

1. Navigate to EC2 via the *Services* menu at the top.
2. Click **Security Groups** in the left-hand menu, and then click **Create Security Group**.
3. In the *Create Security Group* popup, use the following values:
    - *Security group name*: EssentialsSG
    - *Description*: EssentialsSG
    - *VPC*: *Leave default listed.*
    - *Inbound*: Click **Add Rule** and use the following values:
        - *Type*: RDP
        - *Protocol*: TCP
        - *Port Range*: 3389
        - *Source*: Custom 0.0.0.0/0
        - *Description*: RDP ACCESS
4. Click **Create**.

## Create a Windows EC2 Instance

We're ready to create our instance.

1. Navigate to the EC2 dashboard, and click **Launch Instance**.
2. On the AMI page, scroll to find and select the free-tier Windows server.
3. Leave *t2.micro* selected, and click **Next: Configure Instance Details**.
4. On the *Configure Instance Details* page:
    - Leave the default *Network* and *Subnet* selected.
    - *Auto-assign Public IP*: Enable
5. Click **Next: Add Storage**, and then click **Next: Add Tags**.
6. On the *Add Tags* page, add the following tag:
    - *Key*: Name
    - *Value*: WinRDP
7. Click **Next: Configure Security Group**.
8. Click to *Select an existing security group*, and then select *EssentialsSG* from the table.
9. Click **Review and Launch**, and then **Launch**.
10. In the key pair popup, select **Create a new key pair** and give it a *Key pair name* of "windowsrdp". Click **Download Key Pair**, and then **Launch Instances**.
11. Click **View Instances**, and give it a few minutes to enter the *running* state.
12. Once it's running, click **Connect** at the top.
13. Click **Download Remote Desktop File**, then **Save File**, and **OK**.
14. Click **Get Password**.
15. Click **Browse...**, and then open your downloaded key pair `.pem` file.
16. Click **Decrypt Password**.
17. Copy the password, and then click **Close**.

## Connect Using RDP

Finally, we'll connect to our RDP instance.

1. Open your `Downloads` directory, and open the `.rdp` shortcut file that was downloaded as part of the instance setup.
2. You might get a message saying the connection may not be secure. Click **Continue**.
3. In the *User Account* popup, paste in the password we just copied, and click **Done** and the **Continue**.
4. The RDP connection should pop up.

## Conclusion

Congratulations on completing this lab!