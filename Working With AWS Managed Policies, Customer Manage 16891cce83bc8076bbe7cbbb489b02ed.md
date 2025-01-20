# Working With AWS Managed Policies, Customer Managed Policies, and Inline Policies

## Introduction

In this lab, we'll explore and work 
with the different types of IAM policies available: AWS managed 
policies, customer managed policies, and inline policies.

An AWS managed policy is a standalone 
policy that is created and administered by AWS. A customer managed 
policy is a standalone policy that you administer in your own AWS 
account. An inline policy is a policy that's embedded in an IAM identity
 (a user, group, or role).

## Solution

Log in to the lab using the credentials provided. Make sure you're in the N. Virginia (`us-east-1`) Region throughout the lab.

### Create a Customer Managed Policy

1. From the AWS console, use the top search bar to search for and navigate to **IAM**.
2. From the left IAM dashboard menu, select **Users**.
    
    > Note: In addition to the cloud_user, there should be 3 users: charlie, ian, and sally.
    > 
3. From the left IAM dashboard menu, select **Policies**.
4. Click **Create Policy**.
5. In the *Visual editor* under *Service*, click **Choose a service**.
6. In *Service* search field, search for *DynamoDB*.
7. Select **DynamoDB**.
8. In *Actions*, under *Access level*, click on **Read**.
9. Check the box for **All read actions**.
10. In *Resources*, select **All resources**.
11. Click **Next:Tags** > **Next:Review**.
12. In the *Name* field, enter *MyCustomerManagedPolicy*.
13. Click **Create policy**.
    
    > Note: If you see a 
    message at the bottom of the page about needing permissions, you can 
    safely ignore this message. It won't prevent you from continuing with 
    the lab.
    > 
14. Verify the policy was successfully created by filtering policies: **Type** > **Type: Customer managed**.

### Attach the Correct Policies to the Users

### Attach a Customer Managed Policy

1. From the left menu, select **Policies**.
2. Select the newly created `MyCustomerManagedPolicy` policy.
3. Select the **Entities attached** tab, and click **Attach**.
4. Select the `charlie` user, and click **Attach policy**.

### Attach an AWS Managed Policy

1. From the left menu, select **Policies**.
2. In the *Filter policies* field, search for *AWSLambda_FullAccess*.
3. Select the `AWSLambda_FullAccess` policy.
4. At the top of the page, click **Actions** > **Attach**.
5. Select the `sally` user, and click **Attach policy**.

### Attach a New Inline Policy

1. From the left menu, select **Users**.
2. Select the `ian` user.
3. In *Permissions policies*, click **Add permissions** and select **Create inline policy**.
4. In *Select a service* search field, search for *S3*.
5. Select **S3**.
6. In *Actions*, under *Access level*, click on **Read**.
7. Select **All read actions**.
8. In *Resources*, select **All resources**.
    
    > Note: If you see a 
    message at the bottom of the page about needing permissions, you can 
    safely ignore this message. It won't prevent you from continuing with 
    the lab.
    > 
9. Click on **Next**.
10. In the *Policy Name* field, enter *MyInlinePolicy*.
11. Click **Create policy**.

### Attach Your Customer Managed Policy to Sally

1. From the left menu, select **Users**.
2. Select the `sally` user.
3. Select the *Permissions* tab, and click **Add permissions**.
4. Select **Attach policies directly**.
5. Click **Filter policies**, and select **Customer managed**.
6. Check the box next to the `MyCustomerManagedPolicy` policy.
7. Click **Next > **Add permissions**.

### Attempt to Attach an Inline Policy to Sally

1. Click **Add inline policy**. You will be prompted to create a new policy. Click **Cancel** and try to see if you can add the policy via the `ian` user.
2. From the left menu, select **Users**.
3. Select the `ian` user.
4. Select the `MyInlinePolicy` policy.
    
    > Note: You won't see 
    an option to select and attach it to another user. This is because 
    inline policies are embedded within the user group or role they're 
    attached to, so this inline policy is embedded within the ian user and we cannot attach it to another user like sally.
    > 

## Conclusion

Congratulations — you've completed this hands-on lab!