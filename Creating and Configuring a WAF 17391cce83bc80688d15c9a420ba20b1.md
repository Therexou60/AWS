# Creating and Configuring a WAF

## Introduction

Your team has been tasked with 
implementing an Application Load Balancer (ALB) and web application 
firewall (WAF) to help with some traffic issues and attacks that your 
instances have been experiencing. You will create an ALB and an access 
control list with a managed rule and a custom rule.

## Solution

Log in to the AWS Management Console using the credentials provided on the lab instructions page. Make sure you're using the `us-east-1` Region.

### Create an ALB and WAF

### Create an ALB

1. In the search bar at the top of the AWS portal, type and select **EC2**.
2. Click on **Instances (running)**.
3. Under **Load Balancing** in the left navigation menu, click **Load Balancers**.
4. Click **Create Load Balancer**.
5. Under **Load balancer types**, click **Create** under **Application Load Balancer**.
6. Under **Load Balancer name**, enter *ALB1*.
7. Scroll down to **Network mapping** > **VCP**, click on the dropdown menu, and select the only VPC available.
8. Under **Mappings**, click on the checkbox next to **us-east-1a** and **us-east-1b**.
9. Under **Security groups**, click on the dropdown menu and select the security group containing **EC2SecurityGroup** in the name.
10. Under **Listeners and routing** > **Default action**, click **Create target group**. This will open a new tab.
11. Under **Basic Configuration** > **Choose a target type**, select **Instances**.
12. Under **Target group name**, enter *TG1*.
13. Leave the other settings at default and click **Next**.
14. Under **Available instances**, click on the checkbox next to **Public1** and **Public2**.
15. Ensure **Ports for the selected instances** is set to port *80*.
16. Click **Include as pending below**.
17. At the bottom of the page, click **Create target group**.
18. Navigate back to the **Load balancers** tab.
19. Under **Listeners and routing** next to **Default action**, click on the Refresh icon.
20. Next to **Default action**, click on the dropdown menu and select the **TG1** target group.
21. Leave the other settings at default, scroll down, and click **Create load balancer**.
22. Click **View load balancer**. You should see it in the **Provisioning** state. Wait a may take a few minutes until it is in an **Active** state.

### Create a WAF

1. In the search bar at the top of the AWS portal, type **WAF** and select **WAF & Shield**.
2. Under **AWS WAF** in the left navigation menu, click **IP sets**.
3. Click **Create IP set**.
4. Under **IP set name**, enter *Set1*.
5. Under **IP version**, ensure **IPv4** is selected.
6. Under **IP addresses**, you will need to enter your IP address. To retrieve this information, open a new browser tab and navigate to the [DSL Reports website](http://www.dslreports.com/):
    - Click on the **Tools** dropdown menu in the menu bar and select **My IP is**.
    - Copy the IP address next to **My web IP address**.
7. Navigate back to the **AWS WAF** tab.
8. Under **IP addresses**, paste in your web IP address and add */32* as the suffix to ensure it is in CIDR format.
9. Click **Create IP set**.
10. Under **AWS WAF** in the left navigation menu, click **Web ACLs**.
11. Click **Create web ACL**.
12. Under **Name**, enter *ACL1*.
13. Leave the other settings at default and click **Next**.

### Add Rules

1. Under **Rules**, click on the **Add rules** dropdown menu and select **Add managed rule groups**.
2. Click on **AWS managed rule groups** to expand the menu.
3. Scroll down to **Free rule groups**.
4. Next to **Amazon IP reputation list**, slide the scroll bar to enable **Add to web ACL**.
5. Scroll down to the bottom of the page and click **Add rules**.
6. Under **Rules**, click on the **Add rules** dropdown menu and select **Add my own rules and rule groups**. Set the following values below:
    - **Rule type**: Select **Rule builder**.
    - **Rule builder** > **Name**: Enter *MyRule*.
        - Under **Type**, select **Regular rule**.
    - Under **Statement** > **Inspect**, click on the dropdown menu and select **Originates from an IP address in**.
        - **IP set**: Click on the dropdown menu and select **Set1**.
        - Under **IP address to use as the originating address**, select **Source IP address**.
    - Under **Action**, select **Allow**.
7. Click **Add rule**.
8. Under **Default web ACL action for requests that don't match any rules**, select **Block**.
9. Click **Next**.
10. Click **Next** through **Set rule priority**.
11. Under **Configure metrics** > **Request sampling options**, select **Disable sampled requests**.
12. Click **Next**.
13. Click **Create web ACL**. Allow 5-7 minutes for the web ACL to populate before associating the ALB.

### Associate the ALB to the Web ACL

1. Click on the **ACL1** web ACL.
2. Click on **Associated AWS resources** tab.
3. In the right corner, click **Add AWS resources**.
4. Under **Resource type**, select **Application Load Balancer**.
5. Under **Name**, click on the bubble next to **ALB1**.
6. Click **Add**.
    
    > Note: If you get an error, retry after a few minutes.
    > 

### Test Configuration of the WAF

1. In the search bar at the top of the AWS portal, type and select **EC2**.
2. Under **Load Balancing** in the left navigation menu, click **Load Balancers**.
3. Halfway down the page under **Load balancer: ALB1** > **Description** > **DNS name**, click on the Copy icon to copy the DNS name.
4. Navigate to a new browser tab and paste in the DNS name. You should see the WAF Test page.
5. Navigate back to the **EC2 Management Console** tab.
6. In the search bar at the top of the AWS portal, type **WAF** and select **WAF & Shield**.
7. Under **AWS WAF** in the left navigation menu, click **IP sets**.
8. Click on the **Set1** IP set to navigate into it.
9. Under **IP addresses**, select the checkbox next to the listed IP address.
10. In the upper right corner, click **Delete**.
11. In the pop-up menu, type *delete* to confirm deletion and click **Delete**.
12. Navigate back to the WAF Test page and refresh the page. You should see 403 Forbidden error.
    
    > Note: If you do not receive the 403 Forbidden error, refresh the page after a few minutes.
    >