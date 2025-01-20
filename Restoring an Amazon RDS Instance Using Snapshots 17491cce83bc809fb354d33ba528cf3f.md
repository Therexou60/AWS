# Restoring an Amazon RDS Instance Using Snapshots

## Introduction

A critical part of database management is being able to recover your data after corruption or accidental deletion has occurred.

In this hands-on lab, we'll use the 
point-in-time restore capability of RDS automated snapshots to restore a
 database and bring a site back up.

**NOTE**: This lab may take up to 15 minutes to fully start due to the number of resources that need to be provisioned.

## Solution

Log in to the AWS Management Console using the credentials provided on the lab instructions page. Make sure you're using the `us-east-1` Region.

### Create an RDS Snapshot

1. In the AWS Management Console, under **Recently visited**, select **EC2**.
2. In the lefthand navigation menu, under **Load Balancers**, select **Load Balancers**.
3. In the **DNS name** column of the **Load balancers** table, click the copy icon next to the given DNS name for the provided load balancer.
4. Open a new browser window or tab and paste the DNS name into the address bar. You should see a functioning Wordpress site.
5. Return to the AWS console.
6. In the upper left of the console, select **Services**.
7. In the search bar on top of the console, enter *rds*.
8. From the search results, select **RDS**.
9. Under **Resources**, select **DB Instances**.
10. Under **Databases**, select **wordpress-database**.
11. Select the **Maintenance & backups** tab. You should see there is an automated snapshot already created.
12. Click the **Take snapshot** button.
13. On the **Take DB Snapshot** page, under **Snapshot Name**, enter *wordpress-<insert current date>*, substituting the current date for *<insert current date>*.
14. Click the **Take snapshot** button. It may take a few minutes to create the snapshot.

### Log In to the Bastion Host and Delete Database Data

1. In the lefthand navigation menu, select **Databases**.
2. From the list of databases, select **wordpress-database**.
3. In the **Connectivity & security** tab, under **Endpoint & port**, copy the endpoint and save it to a text file or other location for future reference.
4. Return to the lab credentials page.
5. Under **Cloud Server bastion instance**, click **Launch Instant Terminal**.
6. In the terminal, log in to the Bastion instance using the credentials provided by the lab.
7. Log in to your database instance, substituting the endpoint you copied earlier for `<RDS endpoint>`:
    
    ```
    mysql -u wpuser -p -h <RDS endpoint>
    
    ```
    
8. When prompted for a password, enter `Password1`.
9. Change to the `wordpressdb` database:
    
    ```
    use wordpressdb;
    
    ```
    
10. Drop the `wp_posts` table from the Wordpress database:
    
    ```
    drop table wp_posts
    
    ```
    
11. Return to the browser window or tab with the Wordpress site open and refresh the page. You should see a **NOTHING FOUND** message instead of the page we saw previously.

### Restore an RDS Database from a Snapshot

1. Return to the browser window or tab with the AWS console open.
2. In the lefthand navigation menu, select **Snapshots**.
3. In the **Manual** tab, take note of the date and time listed under **Snapshot creation time**.
4. In the lefthand navigation menu, select **Databases**.
5. Under **Databases**, select **wordpress-database**.
6. Click the **Actions** button.
7. From the dropdown menu, select **Restore to point in time**.
8. On the **Restore to point in time** page, set the following values:
- **Restore time**: Select **Custom date and time**.
- **Date**: Select today's date.
- **Time**: From the dropdown menus, select the hour, minute, and second from the snapshot time viewed earlier.
- **DB instance identifier**: Enter *wordpress-recovery*.
1. Click the **Restore to point in time** button. The recovery process will take a few minutes to complete. You
may need to click the refresh icon in the console to view progress.

### Rename Database Instances

1. Select the **wordpress-database** database.
2. Click the **Modify** button.
3. On the **Modify DB instance: wordpress-database** page, under **DB instance identifier**, enter *wordpress-corrupt*.
4. Click the **Continue** button.
5. Under **Schedule modifications**, select **Apply immediately**.
6. Click **Modify DB instance**. This may take a few minutes for the renaming process to complete.
7. Select the **wordpress-recovery** database.
8. On the **Modify DB instance: wordpress-database** page, under **DB instance identifier**, enter *wordpress-database*.
9. Under **Security group**, make sure that the security group that includes **DatabaseSecurityGroup** in its name is selected.
10. Click the **Continue** button.
11. Under **Schedule modifications**, select **Apply immediately**.
12. Click **Modify DB instance**. Again, this may take a few minutes for the renaming process to complete.
13. Return to the browser window or tab with the Wordpress site open and refresh
the page. You should see the site has been restored and looks the same
when you first opened it.