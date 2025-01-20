# AWS Security Essentials - Network Segmentation Lab

## Introduction

In this hands-on lab, we're using 
security groups and network access control lists to segment the network 
so only necessary traffic is available. You will gain experience with 
using security groups and network access control lists to secure the 
different layers of a multi-tier application.

## Solution

Log in to the live AWS environment using the credentials provided. Make sure you're in the N. Virginia (`us-east-1`) region throughout the lab.

### Configure Security Groups

1. Log in to the AWS console, navigate to **Services**.
2. Click **VPC**, located under **Networking & Content Delivery**.
3. Click **Security Groups**, located in the left sidebar menu.
4. Select Security Group **Bastion**, and click **Inbound rules**, located in the lower panel.
5. Click **Edit inbound rules**.
6. Click, **Add rule**, and set the following values:
    - *Type*: **SSH**
    - *Source*: **Anywhere**
7. Click **Save rules**.
8. Select Security Group **ALB**, and click **Inbound rules**, located in the lower panel.
9. Click **Edit inbound rules**.
10. Click, **Add rule**, and set the following values:
    - *Type*: **HTTP**
    - *Source*: **Anywhere**
11. . Click, **Add rule** to add another rule, and set the following values:
    - *Type*: **HTTPS**
    - *Source*: **Anywhere**
12. Click **Save rules**.
13. Select Security Group **AppServer**, and click **Inbound rules**, located in the lower panel.
14. Click **Edit inbound rules**.
15. Click, **Add rule**, and set the following values:
    - *Type*: **HTTP**
    - *Source*: **CustomALB** Security Groups (select from search field)
16. Click, **Add rule** to add another rule, and set the following values:
    - *Type*: **SSH**
    - *Source*: **CustomBastion** Security Groups (select from search field)
17. Click **Save rules**.
18. Select Security Group **RDSDB**, and click **Inbound rules**, located in the lower panel.
19. Click **Edit inbound rules**.
20. Click, **Add rule**, and set the following values:
    - *Type*: **MySQL/Aurora**
    - *Source*: **CustomAppServer** Security Groups (select from search field)
21. Click **Save rules**.

### Configure DMZ ACL

1. Click **Network ACLs**, located in the left sidebar menu.
2. Select Network ACLs **DMZ**, and click **Inbound rules**, located in the lower panel.
3. Click **Edit inbound rules**.
4. Click, **Add new rule**, and set the following values:
    - *Rule number*: **100**
    - *Type*: **SSH (22)**
    - *Source*: **0.0.0.0/0**
    - *Allow/Deny*: **Allow**
5. Click, **Add new rule** to add another rule, and set the following values:
    - *Rule number*: **110**
    - *Type*: **HTTP (80)**
    - *Source*: **0.0.0.0/0**
    - *Allow/Deny*: **Allow**
6. Click, **Add new rule** to add another rule, and set the following values:
    - *Rule number*: **120**
    - *Type*: **HTTPS (443)**
    - *Source*: **0.0.0.0/0**
    - *Allow/Deny*: **Allow**
7. Click, **Add new rule** to add another rule, and set the following values:
    - *Rule number*: **130**
    - *Type*: **Custom TCP**
    - *Port range*: **1024-65535**
    - *Source*: **0.0.0.0/0**
    - *Allow/Deny*: **Allow**
8. Click **Save changes**.
9. Click **Outbound rules**, located in the lower panel.
10. Click **Edit outbound rules**.
11. Click, **Add new rule**, and set the following values:
    - *Rule number*: **100**
    - *Type*: **SSH (22)**
    - *Source*: **0.0.0.0/0**
    - *Allow/Deny*: **Allow**
12. Click, **Add new rule** to add another rule, and set the following values:
    - *Rule number*: **110**
    - *Type*: **HTTP (80)**
    - *Source*: **0.0.0.0/0**
    - *Allow/Deny*: **Allow**
13. Click, **Add new rule** to add another rule, and set the following values:
    - *Rule number*: **120**
    - *Type*: **HTTPS (443)**
    - *Source*: **0.0.0.0/0**
    - *Allow/Deny*: **Allow**
14. Click, **Add new rule** to add another rule, and set the following values:
    - *Rule number*: **130**
    - *Type*: **Custom TCP**
    - *Port range*: **1024-65535**
    - *Source*: **0.0.0.0/0**
    - *Allow/Deny*: **Allow**
15. Click **Save changes**.
16. Click **Subnet associations**, located in the lower panel.
17. Click **Edit subnet associations**.
18. Select **DMZ1public** and **DMZ2public**.
19. Click **Save changes**.

### Configure the AppLayer ACL

1. Deselect Network ACL, **DMZ**, and select **AppLayer**.
2. Click **Inbound rules**, and click **Edit inbound rules**.
3. Click, **Add new rule**, and set the following values:
    - *Rule number*: **100**
    - *Type*: **HTTP (80)**
    - *Source*: **0.0.0.0/0**
    - *Allow/Deny*: **Allow**
4. Click, **Add new rule** to add another rule, and set the following values:
    - *Rule number*: **110**
    - *Type*: **Custom TCP**
    - *Port range*: **1024-65535**
    - *Source*: **0.0.0.0/0**
    - *Allow/Deny*: **Allow**
5. Click **Save changes**.
6. Click **Outbound rules**, located in the lower panel.
7. Click **Edit outbound rules**.
8. Click, **Add new rule**, and set the following values:
    - *Rule number*: **100**
    - *Type*: **HTTP (80)**
    - *Source*: **0.0.0.0/0**
    - *Allow/Deny*: **Allow**
9. Click, **Add new rule** to add another rule, and set the following values:
    - *Rule number*: **110**
    - *Type*: **Custom TCP**
    - *Port range*: **1024-65535**
    - *Source*: **0.0.0.0/0**
    - *Allow/Deny*: **Allow**
10. Click **Save changes**.
11. Click **Subnet associations**, located in the lower panel.
12. Click **Edit subnet associations**.
13. Select **AppLayer1** and **AppLayer2**.
14. Click **Save changes**.

### Configure the DBLayer ACL

1. Deselect Network ACL, **AppLayer**, and select **DBLayer**.
2. Click **Inbound rules**, and click **Edit inbound rules**.
3. Click, **Add new rule**, and set the following values:
    - *Rule number*: **100**
    - *Type*: **MySQL/Aurora (3306)**
    - *Source*: **0.0.0.0/0**
    - *Allow/Deny*: **Allow**
4. Click, **Add new rule** to add another rule, and set the following values:
    - *Rule number*: **110**
    - *Type*: **Custom TCP**
    - *Port range*: **1024-65535**
    - *Source*: **0.0.0.0/0**
    - *Allow/Deny*: **Allow**
5. Click **Save changes**.
6. Click **Outbound rules**, located in the lower panel.
7. Click **Edit outbound rules**.
8. Click, **Add new rule**, and set the following values:
    - *Rule number*: **100**
    - *Type*: **MySQL/Aurora (3306)**
    - *Source*: **0.0.0.0/0**
    - *Allow/Deny*: **Allow**
9. Click, **Add new rule** to add another rule, and set the following values:
    - *Rule number*: **110**
    - *Type*: **Custom TCP**
    - *Port range*: **1024-65535**
    - *Source*: **0.0.0.0/0**
    - *Allow/Deny*: **Allow**
10. Click **Save changes**.
11. Click **Subnet associations**, located in the lower panel.
12. Click **Edit subnet associations**.
13. Select **DBLayer1** and **DBLayer2**.
14. Click **Save changes**.