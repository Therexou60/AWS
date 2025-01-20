# Building a Three-Tier Network VPC from Scratch in AWS

## Introduction

In this lab, we're going to create a 
three-tier VPC network from scratch. We'll start by building the VPC, 
building and attaching an Internet gateway, and building six different 
subnets inside our VPC:

- Two DMZ layers
- Two App layers
- Two Database layers

Next, we're going to split these pairs
 of subnets across two different Availability Zones — the bare minimum 
we always want to do for highly available and fault-tolerant 
architecture in AWS.

Then we're going to create two different route tables:

- A route to the Internet for our public subnets or subnets we want to have access to the open Internet.
- A route to the NAT gateway so that anything placed in our private subnets will have a route to update software from the open Internet.

Finally, we'll add some security to 
our subnets with three Network Access Control Lists (NACLs), which we'll
 assign to our pairs of subnet layers.

## Solution

Log in to the AWS Management Console using the credentials provided on the lab instructions page. Make sure you're using the *us-east-1* region.

### Create a VPC

1. Select **All services**.
2. Click **VPC** under **Networking & Content Delivery**.
3. In the left sidebar menu, click **Your VPCs**.
4. Click **Create VPC**.
5. Enter the following values for **Create VPC**:
    - **Name tag**: Enter *SysOpsVPC*.
    - **Pv4 CIDR block** range: Enter *10.99.0.0/16*.
    - Leave the **IPv6 CIDR block** and **Tenancy** fields as their default values.
6. Click **Create VPC**.

### Create Six Subnets

### Create First DMZ Layer

1. In the left sidebar menu, click **Subnets**.
2. Click **Create subnet**.
3. Enter the following values for **Create subnet**:
    - **VPC ID**: Select **SysOpsVPC**.
    - **Name tag**: Enter *DMZ1public*.
    - **Availability Zone**: Select **US East (N. Virginia)/us-east-1a**.
    - **IPv4 CIDR block** range: Enter *10.99.1.0/24*.
4. Click **Create subnet**.

### Create Second DMZ Layer

1. Click **Create subnet**.
2. Enter the following values for **Create subnet**:
    - **VPC ID**: Select **SysOpsVPC**.
    - **Name tag**: Enter *DMZ2public*.
    - **Availability Zone**: Select **US East (N. Virginia)/us-east-1b**.
    - **IPv4 CIDR block** range: Enter *10.99.2.0/24*.
3. Click **Create subnet**.

### Create First App Layer

1. Click **Create subnet**.
2. Enter the following values for **Create subnet**:
    - **PC ID**: Select **SysOpsVPC**.
    - **Name tag**: Enter *AppLayer1private*.
    - **Availability Zone**: Select **US East (N. Virginia)/us-east-1a**.
    - **IPv4 CIDR block** range: Enter *10.99.11.0/24*.
3. Click **Create subnet**.

### Create Second App Layer

1. Click **Create subnet**.
2. Enter the following values for **Create subnet**:
    - **VPC ID**: Select **SysOpsVPC**.
    - **Name tag**: Enter *AppLayer2private*.
    - **Availability Zone**: Select **US East (N. Virginia)/us-east-1b**.
    - **IPv4 CIDR block** range: Enter *10.99.12.0/24*.
3. Click **Create subnet**.

### Create First Database Layer

1. Click **Create subnet**.
2. Enter the following values for **Create subnet**:
    - **VPC ID**: Select **SysOpsVPC**.
    - **Name tag**: Enter *DBLayer1private*.
    - **Availability Zone**: Select **US East (N. Virginia)/us-east-1a**.
    - **IPv4 CIDR block** range: Enter *10.99.21.0/24*.
3. Click **Create subnet**.

### Create Second Database Layer

1. Click **Create subnet**.
2. Enter the following values for **Create subnet**:
    - **VPC ID**: Select **SysOpsVPC**.
    - **Name tag**: Enter *DBLayer2private*.
    - **Availability Zone**: Select **US East (N. Virginia)/us-east-1b**.
    - **IPv4 CIDR block** range: Enter *10.99.22.0/24*.
3. Click **Create subnet**.

*NOTE:* We've now created all six subnets. We should have three subnets each in the `us-east-1a` Availability Zone and the `us-east-1b` Availability Zone.)*

*NOTE:*
 Notice a pattern in the CIDR block ranges? Using the third octet, we 
categorized them by groups of 10. So for quick reference, we know that 
if the third octet is:

- 1 or 2, it's part of the DMZ layer
- 11 or 12, it's part of the app layer
- 21 or 22, it's part of the database layer

*NOTE:* Whether we labeled these subnets public or private doesn’t *actually*
 make them public or private — it’s just a naming construct. We’ll 
actually make them public or private in a bit when we route them to a 
public or private route table.

### Create Internet Gateway

1. In the left sidebar menu, click **Internet Gateways**.
2. For the *Name tag*, enter "IGW".
3. Click **Create internet gateway**.
    
    *NOTE:* Once it's created, you'll see its *State* says *Detached*. Even though it's been created, it isn't part of the VPC yet. Let's fix that.
    
4. Click **Actions** at the top of the screen.
5. From the dropdown menu, click **Attach to VPC**.
6. For **Avaliable VPCs**, select **SysOpsVPC**.
7. Click **Attach internet gateway**.

*NOTE:* The state should now say *Attached*.

### Create a NAT Gateway

### Create NAT Gateway

1. In the left sidebar menu, click **NAT Gateways**. *(NOTE: If there’s one already in your account, you can ignore it; we’re still going to create a new one.)*
2. Click **Create NAT Gateway**.
3. Enter the following for **Create NAT gateway**:
    - **Name**: Enter *NGW*.
    - **Subnet**: Select **DMZ2public**.
    - **Connectivity type**: Select **Public**.
    - **Elastic IP allocation ID**: Select **Allocate Elastic IP**.
4. Click **Create NAT gateway**.

### Create Public Route Table

1. In the left sidebar menu, click **Route Tables**. *(NOTE: A route table will already exist — when we created the VPC, it created a default route table. But we're going to create two new route tables.)*
2. Click **Create route table**.
3. Enter the following for **Create route table**:
    - **Name tag**: Enter *PublicRT*.
    - **VPC**: Select **SysOpsVPC**.
4. Click **Create route table**.

### Create Private Route Table

1. Click **Create route table**.
2. Enter the following for **Create route table**:
    - **Name tag**: Enter *PrivateRT*.
    - **VPC**: Select **SysOpsVPC**.
3. Click **Create route table**.

On their own, route tables don't do anything — we have to give them routes *to* something. For the public route table, we need to . For the private route table, we need to provide a route to the NAT gateway.

### Provide Route from the Public Route Table to the Internet Gateway

1. Select **PublicRT**.
2. Click on the **Routes** tab.
3. Click **Edit routes**.
4. Click **Add route**.
5. For **Target**, select **Internet Gateway**.
6. For **Destination**, select **0.0.0.0/0**.
7. Click **Save changes**.

We've now created a route from our public route table through the internet gateway into the open internet.

### Provide Route from the Private Route Table to the NAT Gateway

1. Select **PrivateRT**.
2. Click on the **Routes** tab.
3. Click **Edit routes**.
4. Click **Add route**.
5. For **Target**, select **NAT Gateway**.
6. For **Destination**, select **0.0.0.0/0**.
7. Click **Save changes**.

### Associate Public Subnets with the Public Route Table

1. Click **PublicRT**.
2. Click on the **Subnet associations** tab.
3. Click **Edit subnet associations**.
4. Select the **DMZ1public** and **DMZ2public** subnets.
5. Click **Save associations**.

### Associate Private Subnets with the Private Route Table

1. Click **PrivateRT**.
2. Click on the **Subnet associations** tab.
3. Click **Edit subnet associations**.
4. Select the following subnets:
    - **AppLayer1private**
    - **AppLayer2private**
    - **DBLayer1private**
    - **DBLayer2private**
5. Click **Save associations**.

Now, anything placed inside the public
 route table has a route to the internet gateway, and anything placed 
inside the private route table has a route to the NAT gateway.

If we have databases or EC2 instances 
located inside these private subnets, they can get updates from the open
 internet by going through the NAT gateway, which provides an extra 
layer of security. Essentially, it’s a one-way street: The resources in 
the private subnets can access the open internet, but the open Internet 
cannot access the resources in the private subnets (unless we explicitly
 allow it).

We’re almost done with this lab. 
Before we wrap things up, let’s add another layer of security to our VPC
 by creating an NACL — a sort of firewall for controlling traffic in and
 out of one or more subnets — for each of our layers.

### Create Three NACLs and Associate Them with Subnets

### Create DMZ NACL

1. In the left sidebar menu, click **Network ACLs**. *(NOTE: We should see a default NACL, similar to route tables. The default NACL was created when we created our VPC. But we're going to create three
new ones.)*
2. Click **Create network ACL**.
3. For **Name**, enter *DMZNACL*.
4. For **VPC**, select **SysOpsVPC**.
5. Click **Create network ACL**.

### Create App NACL

1. Click **Create network ACL**.
2. For **Name**, enter *APPNACL*.
3. For **VPC**, select **SysOpsVPC**.
4. Click **Create network ACL**.

### Create DB NACL

1. Click **Create network ACL**.
2. For **Name**, enter *DBNACL*.
3. For **VPC**, select **SysOpsVPC**.
4. Click **Create network ACL**.

### Associate Subnets with NACLs

1. Select **DMZNACL**.
2. Click on the **Subnet associations** tab.
3. Click **Edit subnet associations**.
4. Select the **DMZ1public** and **DMZ2public** subnets.
5. Click **Save changes**.

*NOTE: 
Now traffic coming in and out of these subnets will be subject to the 
inbound and outbound rules we set up on this particular NACL. We're not 
going to set up any rules as part of this lab — right now, we're just 
building the infrastructure and a shell we could put resources in.*

### Finish NACLs for the Remaining Layers

1. Click **AppNACL**.
2. Click on the **Subnet associations** tab.
3. Click **Edit subnet associations**.
4. Select the **AppLayer1private** and **AppLayer2private** subnets.
5. Click **Save changes**.
6. Click **DBNACL**.
7. Click on the **Subnet associations** tab.
8. Click **Edit subnet associations**.
9. Select the **DBLayer1private** and **DBLayer2private** subnets.
10. Click **Save changes**.