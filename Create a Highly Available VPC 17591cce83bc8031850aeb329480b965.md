# Create a Highly Available VPC

## Introduction

In this lab, you will create a highly 
available VPC. Then, you will configure the route table, create a 
security group, and enable logging to S3.

## Solution

Log in to the AWS Management Console in an incognito or private browser window using the credentials provided for the lab.

Make sure you're using the **us-east-1 (N. Virginia)** Region.

### Create a Highly Available VPC

### Create the VPC

1. Navigate to **VPC** using the **Services** menu or the unified search bar.
2. In the sidebar menu, select **Your VPCs**.
3. On the right, click **Create VPC**.
4. Configure the **VPC settings** section:
    - **Resources to create**: Select **VPC only**.
    - **Name tag**: In the text box, enter *LabVPC*.
    - **IPv4 CIDR**: In the text box, enter *10.20.0.0/16*.
5. Leave all the other default settings and click **Create VPC**.
    
    The VPC is created and its details are automatically displayed.
    

### Create the Subnets

1. In the sidebar menu, select **Subnets**.
2. On the right, click **Create subnet**.
3. In the **VPC ID** field, use the dropdown to select your VPC.
4. Configure the **Subnet settings** section for public subnet 1:
    - **Subnet name**: In the text box, enter *Public1*.
    - **Availability Zone**: Use the dropdown to select **US East (N.Virginia)/us-east-1a**.
    - **IPv4 CIDR block**: In the text box, enter *10.20.1.0/24*.
5. Toward the bottom of the page, click **Add new subnet**.
6. Configure the **Subnet settings** section for public subnet 2:
    - **Subnet name**: In the text box, enter *Public2*.
    - **Availability Zone**: Use the dropdown to select **US East (N. Virginia)/us-east-1b**.
    - **IPv4 CIDR block**: In the text box, enter *10.20.2.0/24*.
7. Toward the bottom of the page, click **Add new subnet** again.
8. Configure the **Subnet settings** section for private subnet 1:
    - **Subnet name**: In the text box, enter *Private1*.
    - **Availability Zone**: Use the dropdown to select **US East (N. Virginia)/us-east-1a**.
    - **IPv4 CIDR block**: In the text box, enter *10.20.3.0/24*.
9. Toward the bottom of the page, click **Add new subnet** one more time.
10. Configure the **Subnet settings** section for private subnet 2:
    - **Subnet name**: In the text box, enter *Private2*.
    - **Availability Zone**: Use the dropdown to select **US East (N. Virginia)/us-east-1b**.
    - **IPv4 CIDR block**: In the text box, enter *10.20.4.0/24*.
11. At the bottom of the page, click **Create subnet**.
    
    The four subnets take a moment to finish creating.
    

### Build an Internet Gateway

1. In the sidebar menu, select **Internet gateways**.
2. On the right, click **Create internet gateway**.
3. In the **Name tag** field, enter *LabIGW*.
4. Click **Create internet gateway**.
    
    The internet gateway takes a moment to finish building.
    
5. After the internet gateway is created, use the breadcrumb along the top of the page to select **Internet gateways**.
6. Check the checkbox to the left of your **LabIGW** gateway.
7. On the right, use the **Actions** dropdown to select **Attach to VPC**.
8. In the **Available VPCs** field, click into the text box and select your **LabVPC** VPC.
9. Click **Attach internet gateway**.
    
    The internet gateway is attached to your VPC.
    

### Configure a Route Table within Your VPC Named `PubRT`

### Create the Route Table and Associate the Public Subnets

1. In the sidebar menu, select **Route tables**.
2. In the top right corner, click **Create route table**.
3. Configure the **Route table settings** section:
    - **Name**: In the text box, enter *PubRT*.
    - **VPC**: Use the dropdown to select your **LabVPC** VPC.
4. Click **Create route table**.
    
    The route table is created and its details display automatically.
    
5. On the right, use the **Actions** dropdown to select **Edit subnet associations**.
6. Check the checkboxes to the left of the **Public1** and **Public2** subnets.
7. Click **Save associations**.

### Update the Routes to Send Traffic to the Internet Gateway

1. From the route table details, ensure the **Routes** tab is selected.
2. On the right, click **Edit routes**.
3. Click **Add route**.
4. In the **Destination** field, enter *0.0.0.0/0*.
5. In the **Target** field, select **Internet Gateway**, and then select your **LabIGW** gateway.
6. Click **Save changes**.

### Associate the Private Subnets with the Default VPC

1. Use the breadcrumb along the top of the page to select **Route tables**.
2. Check the checkbox to the left of the default VPC (indicated as the main VPC).
3. From the route table details, select the **Subnet associations** tab.
4. On the right, click **Edit subnet associations**.
5. Check the checkboxes to the left of the **Private1** and **Private2** subnets.
6. Click **Save associations**.

### Create the `PublicSG` and `PrivateSG` Security Groups

### Create the `PublicSG` Security Group

1. In the sidebar menu, select **Security groups**.
2. On the right, click **Create security group**.
3. Fill in the **Basic details** section:
    - **Security group name**: In the text box, enter *PublicSG*.
    - **Description**: In the text box, enter *pubsg*.
    - **VPC**: Click into the field and select your **LabVPC** VPC.
4. In the **Inbound rules** section, click **Add rule**.
5. Fill in the rule details:
    - **Type**: Use the dropdown to select **HTTPS**.
    - **Source**: Use the dropdown to select **Anywhere-IPv4**.
6. Click **Add rule** again.
7. Fill in the rule details:
    - **Type**: Use the dropdown to select **HTTPS**.
    - **Source**: Use the dropdown to select **Anywhere-IPv6**.
8. At the bottom of the page, click **Create security group**.
9. After the security groups are created, use the breadcrumb along the top of the page to select **Security Groups**.

### Create the `PrivateSG` Security Group

1. On the right, click **Create security group**.
2. Fill in the **Basic details** section:
    - **Security group name**: In the text box, enter *PrivateSG*.
    - **Description**: In the text box, enter *privatesg*.
    - **VPC**: Click into the field and select your **LabVPC** VPC.
3. In the **Inbound rules** section, click **Add rule**.
4. Fill in the rule details:
    - **Type**: Use the dropdown to select **All traffic** (you can also type *All traffic* into the field to narrow the dropdown results).
    - **Source**: Leave the **Custom** default. In the search box to the right of **Custom**, select your **PublicSG** security group.
5. At the bottom of the page, click **Create security group**.

### Enable Logging of All IP Traffic to a Created S3 Bucket

1. In the sidebar menu, select **Your VPCs**.
2. Check the checkbox to the left of your **LabVPC** VPC.
3. On the right, use the **Actions** menu to select **Create flow log**.
4. Fill in the **Flow log settings** section:
    - **Name**: In the text box, enter *LabVPCFlowLogs*.
    - **Filter**: Select **All**.
    - **Maximum aggregation interval**: Select **10 minutes**.
    - **Destination**: Select **Send to Amazon S3 bucket**.
5. To get the **S3 bucket ARN**, open **S3** in a new tab using the **Services** menu or the unified search bar.
    
    In the **S3 Management Console**, you should see one bucket.
    
6. Select the bucket name.
7. Along the top of the page, select the **Properties** tab.
8. In the **Bucket overview** section, copy the ARN.
9. Navigate back to the **VPC Management Console** tab and paste your copied ARN into the **S3 bucket ARN** field.
10. Leave all the other default settings, and click **Create flow log**.