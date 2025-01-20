# Configure Transit Gateway for a Multi-VPC Environment

## Introduction

In this hands-on AWS lab, we will 
create a transit gateway and attach two VPCs. We will review the 
propagated routes on the Transit Gateway, create the appropriate routes 
in our VPCs, and validate the connectivity.

## Solution

Log in to the AWS Management Console 
using the credentials provided on the lab instructions page. Make sure 
you're in the N. Virginia (`us-east-1`) region throughout the lab.

### Create the Transit Gateway

1. Navigate to **Services**.
2. Under **Networking & Content Delivery**, click **VPC**.
3. Click **VPCs**.
4. In the left sidebar menu, click **Transit Gateways**.
5. Click **Create Transit Gateway**.
6. Set the following values:
    - *Name tag*: **MyTGW**
    - *Description*: **MyTGW**
    - *Amazon side ASN:* **65065**
7. Leave the rest as their defaults and click **Create Transit Gateway**.

> Note: It may take a few minutes for the transit gateway to change from a pending state to an available state.
> 

### Transit Gateway Attachments

1. In the left sidebar menu, click **Transit Gateway Attachments**.
2. Click **Create Transit Gateway Attachment**.
3. Set the following values:
    - *Attachment name tag*: **VPC1**
    - *Transit Gateway ID*: **MyTGW**
    - *Attachment type*: **VPC**
    - *DNS support*: enable
    - *VPC ID*: **VPC1**
4. Under *Subnet IDs*, select **us-east-1a** as the Availability Zone and **PublicSubnet1** as the Subnet ID.
5. Click **Create transit gateway attachment**.

---

1. Click **Create transit gateway attachment**, and set the following values for `VPC2`:
    - *Attachment name tag*: **VPC2**
    - *Transit Gateway ID*: **MyTGW**
    - *Attachment type*: **VPC**
    - *DNS support*: enable
    - *VPC ID*: **VPC2**
2. Under *Subnet IDs*, select **us-east-1a** as the Availability Zone and **PublicSubnet2** as the Subnet ID.
3. Click **Create transit gateway attachment**.

---

1. Click **Create Transit Gateway Attachment**, and set the following values for `VPC3`:
    - *Attachment name tag*: **VPC3**
    - *Transit Gateway ID*: **MyTGW**
    - *Attachment type*: **VPC**
    - *DNS support*: enable
    - *VPC ID*: **VPC3**
2. Under *Subnet IDs*, select **us-east-1a** as the Availability Zone and **PublicSubnet3** as the Subnet ID.
3. Click **Create transit gateway attachment**.

---

> Note: It may take a 
few minutes for the transit gateway attachements to become available. To
 view them all clear the filter at the top of the table view.
> 

---

### Routes

1. In the left sidebar menu, click **Route Tables** under **VIRTUAL PRIVATE CLOUD**.
2. Select the `Public1-RT` route table.
3. Select the *Routes* tab and click **Edit routes**.
4. Click **Add route** and set the following values:
    - *Destination*: **10.2.0.0/16**
    - *Target:* **Transit Gateway**
5. Click **Save changes**.

---

Go back to **Route tables** page, by the left menu or the breadcrumb navigation above.

6. Select the `Public2-RT` route table.

7. Under the *Routes* tab, click **Edit routes**.

8. Click **Add route** and set the following values:

* *Destination*: **10.1.0.0/16**

* *Target:* **Transit Gateway**

9.  Click **Save changes**.

### Testing

1. Open a terminal window.
2. Log in to `INSTANCE1` via SSH using the provided lab credentials:
    
    ```
    ssh cloud_user@<INSTANCE1_PUBLIC_IP_ADDRESS>
    
    ```
    
3. Ping the public IP address of `INSTANCE2`:
    
    ```
    ping <INSTANCE2_PUBLIC_IP_ADDRESS>
    
    ```
    
4. Ping the private IP address of `INSTANCE2`:
    
    ```
    ping <INSTANCE2_PRIVATE_IP_ADDRESS>
    
    ```
    
    These should both work for you.
    

---

1. Now, lets, ping the private IP address of `INSTANCE3`:
    
    ```
    ping <INSTANCE3_PRIVATE_IP_ADDRESS>
    
    ```
    
    You will find that pinging Instance 3 will not work, this is because we have not set up the routing for VPC3