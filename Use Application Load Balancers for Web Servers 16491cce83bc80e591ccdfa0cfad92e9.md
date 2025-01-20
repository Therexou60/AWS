# Use Application Load Balancers for Web Servers

## Introduction

Load balancing automatically 
distributes your incoming traffic across multiple targets, such as EC2 
instances, containers, and IP addresses, in one or more Availability 
Zones. In this lab, we configure an Application Load Balancer to 
distribute network traffic to two EC2 instances. We then enable 
stickiness, so that once a server is contacted, the user is always sent 
to that server. This ensures our legacy application continues to work 
despite not supporting distributed logins. By the end of this lab, the 
user will understand how to create an Application Load Balancer and 
enable sticky sessions.

## Solution

Log in to the live AWS environment 
using the credentials provided. Use an incognito or private browser 
window to ensure you're using the lab account rather than your own.

Make sure you're in the N. Virginia (`us-east-1`) region throughout the lab. **NOTE**:
 While we generally recommend folloiwng the steps in the lab as shown, 
if you have trouble with using any of the public IPs referrenced in the 
lab, you can try using the public DNS name instead (Please be aware that
 DNS propogation delays are possible with this approach).

### Observe the Provided EC2 Website and Create a Second Server

1. Navigate to **EC2**.
2. Click **Instances (running)**.
3. Select the box next to **webserver-01**.
4. Copy its **Public IPv4 address**.
    
    > Note: Do NOT try clicking on the open address link as it won't work.
    > 
5. In a new browser tab, paste in the public IP address you just copied. You should see the load balancer demo page.
6. Back in the EC2 console, at the top, click **Launch instances**.
7. Under **Name and Tags**, enter "webserver2".
8. Under **Application and OS Images (Amazon Machine Image)**, select **Ubuntu** and **Ubuntu Server 22.04 LTS**.
    
    > Note: If you get a popup window that tells you some of your current settings will be changed, click on Confirm changes.
    > 
9. Under **Instance Type**, select **t3.micro**.
10. Under **Key pair (login)**, in the dropdown, select **Proceed without a key pair**.
11. Under **Network settings**, click **Edit** and set **Auto-assign Public IP** to **Enable**.
12. Under **Network settings** > **Firewall (security groups)**, click **Select existing security group** and select the one with **EC2SecurityGroup** in its name (*not* the default security group).
13. Under **Advanced Details**, in the **User Data** box, enter the following bootstrap script:
    
    ```
    #!/bin/bash
    
    # Update and install necessary packages
    sudo apt-get update -y
    sudo apt-get install -y apache2 unzip
    
    # Fetching the token for IMDSv2
    TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"`
    
    # Starting HTML file
    echo '<html><center><body bgcolor="black" text="#39ff14" style="font-family: Arial"><h1>Load Balancer Demo</h1><h3>Availability Zone: ' > /var/www/html/index.html
    
    # Using the token to fetch metadata
    echo $(curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/placement/availability-zone) >> /var/www/html/index.html
    echo '</h3> <h3>Instance Id: ' >> /var/www/html/index.html
    echo $(curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/instance-id) >> /var/www/html/index.html
    echo '</h3> <h3>Public IP: ' >> /var/www/html/index.html
    echo $(curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/public-ipv4) >> /var/www/html/index.html
    echo '</h3> <h3>Local IP: ' >> /var/www/html/index.html
    echo $(curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/local-ipv4) >> /var/www/html/index.html
    
    # Ending HTML file
    echo '</h3></html> ' >> /var/www/html/index.html
    
    # - Ensure the Apache2 service is enabled and started.
    sudo systemctl enable apache2
    sudo systemctl start apache2
    
    ```
    
14. Click **Launch Instance**.
15. Click the **Instance ID** (this will start with i-).
16. Once it's in the Running state, copy the **Public IPv4 address**
    
    > Note: Do NOT try clicking on the open address link as it won't work.
    > 
17. In a new browser tab, paste in the
public IP address you just copied. You should see the load balancer demo page again, which means the legacy clone is successfully running. This
time, though, it will have a different instance ID, public IP, and local IP listed.
    
    > Note: If your second EC2 doesn't open the demo page, it may need a couple of minutes to finish provisioning. Wait for the Status check column to show "2/2 checks passed".
    > 

### Create an Application Load Balancer

1. Back in the EC2 console, click **Load Balancers** in the left-hand menu.
2. Click **Create Load Balancer**.
3. From the **Application Load Balancer** card, click **Create**.
4. For **Load balancer name**, enter *LegacyALB*.
5. Under **Network mapping**, click the **VPC** dropdown, and select the listed VPC.
6. When the **Availability Zones** list pops up, select each one (**us-east-1a**, **us-east-1b**, and **us-east-1c**).
7. Under **Security groups**, deselect the default security group listed, and select the one from the dropdown with **EC2SecurityGroup** in its name.
8. Under **Listeners and routing**, ensure that the **Protocol** is set to **HTTP** and the **Port** is **80**. Then, under **Default action**, click **Create target group** This will open a new tab. Keep this first tab open to complete later.
9. For **Target group name**, enter *TargetGroup*.
10. Click **Next**.
11. Under **Available instances**, select both targets that are listed.
12. Click **Include as pending below**.
13. Click **Create target group**.
14. Back in the first tab, under **Default action**, click the refresh button (looks like a circular arrow), and in the dropdown, select the **TargetGroup** you just created.
15. Click **Create load balancer**.
16. On the next screen, click **View load balancer**.
17. Wait a few minutes for the load balancer to finish provisioning and enter an active state.
18. Copy its **DNS name**, and paste it into a new browser tab. You should see the load balancer
demo page again. The local IP lets you know which instance you were sent (or "load balanced") to.
19. Refresh the page a
few times. You should see the other instance's local IP listed, meaning
it's successfully load balancing between the two EC2 instances.

### Enable Sticky Sessions

1. Back on the **EC2** > **Load Balancers** page, select the **Listeners** tab.
2. Click the **TargetGroup** link in the **Default action** column, which opens the target group.
3. Select the **Attributes** tab.
4. Click **Edit**.
5. Check the box next to **Stickiness** to enable it.
6. Leave **Stickiness type** set to **Load balancer generated cookie**.
7. Leave **Stickiness duration** set to **1 days**.
8. Click **Save changes**.
9. Refresh the tab where you navigated to the load balancer's public IP. This
time, no matter how many times you refresh, it will stay on the same
instance (noted by the local IP).

## Conclusion

Congratulations on successfully completing this hands-on lab!