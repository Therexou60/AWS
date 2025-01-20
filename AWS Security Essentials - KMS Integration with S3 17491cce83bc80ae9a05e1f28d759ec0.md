# AWS Security Essentials - KMS Integration with S3

## Introduction

AWS Key Management Service (KMS) is a 
managed service that makes it easy for you to create and control the 
encryption keys used to encrypt your data. KMS uses FIPS 140-2 validated
 hardware security modules to protect the security of your keys. AWS Key
 Management Service is integrated with most other AWS services to help 
you protect the data you store with these services. AWS Key Management 
Service is also integrated with AWS CloudTrail and S3 to provide you 
with logs of all key usage to help meet your regulatory and compliance 
needs.

This activity allows the student to 
get experience with how KMS integrates with services in AWS while 
encrypting S3 data with a default master key as well as a custom key.

## Solution

Log in to the live AWS environment using the credentials provided. Make sure you're in the N. Virginia (`us-east-1`) region throughout the lab.

### Create an Encrypted S3 Bucket

1. From the AWS Management Console, click **Services**.
2. Under *Storage*, click **S3**.
3. Click **Create Bucket**.
4. In *Bucket name*, set a unique bucket name.
5. Scroll down to *Default encryption* and select **Server-side encryption with AWS Key Management Service keys (SSE-KMS)**.
6. Under *AWS KMS key*, select **Choose from your AWS KMS keys**.
7. In *Available AWS KMS keys*, select the key that has *AWS/S3* in its description and click **Create bucket**.

### Upload a File to the Encrypted S3 Bucket

1. Click the newly created bucket to open it.
2. To the right, click **Upload**.
3. Click **Add files**.
4. Select a file from your local machine to upload and click **Choose for Upload**.
5. Click **Upload**.
6. Once the upload has succeeded, click **Exit**.
7. To confirm that the file was successfully encrypted, click the file to open it.
8. Scroll down to *Server-side encryption settings*. Notice that in *KMS master key ARN*, a master key ARN number has been created to encrypt the file from the S3 bucket.

### Encrypt Two Files in S3 with Different Keys

### Create a Customer Managed Key

1. On the top main menu, click **Services** and select **Key Management Service**.

> Note: If you see any 
error messages at the top of the page telling you that you dont' have 
permissions, you can ignore those messages and close them. They won't 
prevent you from being able to complete the lab.
> 
1. On the left menu, select **Customer managed keys**.
2. Click **Create key**.
3. Leave all of the default settings and click **Next**.
4. In *Alias*, enter "my_s3_key".
5. Click **Next** > **Next** > **Next**.
6. Click **Finish**.

### Upload a New File and Encrypt with the Customer Managed Key

1. Return to S3 and click the bucket to open it.
2. To the right, click **Upload**.
3. Click **Add files**.
4. Select a new file from your local machine to upload and click **Open**.
5. Expand **Properties** and scroll down to *Server-side encryption*.
6. In *Server-side encryption*, select **Specify an encryption key**, then **Override bucket settings for default encryption**.
7. In *Encryption key type*, select **AWS Key Management Service key (SSE-KMS)**.
8. In *AWS KMS Key*, select **Choose from your AWS KMS keys**.
9. In *Available AWS KMS keys*, select the customer managed key previously created.
10. Scroll to the bottom and click **Upload**.
11. Once the upload has succeeded, click **Close**.
12. To confirm that the file was successfully encrypted by the custom key, click the file to open it.
13. Scroll down to *Server-side encryption settings*. Notice that in *KMS master key ARN*, a new master key ARN number has been created to override the S3 bucket policy.