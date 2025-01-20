# Setting Up AWS CloudFront as an HTTPS Endpoint for S3

## Introduction

In this hands-on lab, we'll be setting
 up a CloudFront distribution in front of an S3 bucket website and 
securing it via HTTPS provided by CloudFront.  AWS CloudFront is a 
versatile caching service (CDN) that helps with lowering latency when 
accessing objects over the internet. Additionally, it can act as an 
HTTPS termination point for your cached website — thus, providing a 
secure way of distributing your content over the internet.

## Solution

Log in to the AWS Management Console using the credentials provided on the lab instructions page. Make sure you're using the `us-east-1` Region.

Ensure there is an available bucket under **Amazon S3**>**Buckets**.

In a different tab, navigate to the EC2 dashboard, find the running instance, and then connect by doing the following:

1. Navigate to **EC2**> **Instances (running)**.
2. Click on the checkbox next to the instance name.
3. In the upper right corner, select **Connect**.
4. Choose **Session Manager**, and select **Connect**.
5. Once connected, assume the `cloud_user` identity by running: `sudo su cloud_user`.
6. Run `cd` to get to the `cloud_user` home.
7. Print the working directory: `pwd`.
8. Confirm you are `cloud_user`: `whoami`.

> Note: When copying and pasting code into Vim from the lab guide, first enter :set paste (and then i to enter insert mode) to avoid adding unnecessary spaces and hashes. To save and quit the file, press Escape followed by :wq. To exit the file without saving, press Escape followed by :q!.
> 

> Note: If
 the EC2 instance is not showing the ability to connect via Session 
Manager, simply restart the instance to restart the SSM Agent.
> 

### Upload Content to S3 Bucket

1. Verify pre-existing files:
    
    ```
    ls
    
    ```
    
2. Upload the `index.html` file to the bucket (replacing `<BUCKET_NAME_PROVIDED>` with the S3 bucket name listed on the lab page):
    
    ```
    aws s3 cp index.html s3://<BUCKET_NAME_PROVIDED>
    
    ```
    

### Create CloudFront OAI

1. Generate a UUID by running `uuidgen` in the console. Copy and save the UUID in a separate text file; use that for the next step.
2. Create a CloudFront origin access identity (replacing `<YOUR_UNIQUE_UUIDGEN_HERE>` with the `uuidgen`):
    
    ```
    aws cloudfront create-cloud-front-origin-access-identity --cloud-front-origin-access-identity-config CallerReference=<YOUR_UNIQUE_UUIDGEN_HERE>,Comment=MyOAI
    
    ```
    
3. Copy the `Id` from the output and paste it into a text file, as we'll need it for the next step.
4. Also, save the `OAI Id` to an environment variable:
    
    ```
    export OAI_ID=<Id value here>
    
    ```
    

### Modify S3 Policy File in Directory and Execute It Against S3 Bucket

1. Run the next command to substitute your OAI into the `policy_cloudfront.json` document.
2. `sed "s/<OAI-ID>/$(echo $OAI_ID)/" policy_cloudfront.json` to test the changes.
3. Run `sed -i "s/<OAI-ID>/$(echo $OAI_ID)/" policy_cloudfront.json` to save the changes.
4. Execute this policy against the S3 website bucket *(NOTE: The `<BUCKET_NAME>` is the **S3 Website BucketName** found in the credentials section.)*:
    
    ```
    aws s3api put-bucket-policy --bucket <BUCKET_NAME> --policy file://policy_cloudfront.json
    
    ```
    
5. Navigate to the Amazon S3 Console, click on the bucket, and go to the **Permissions** tab to confirm the bucket policy has been updated.

### Create CloudFront Distribution

1. Create a CloudFront distribution:
    
    ```
    aws cloudfront create-distribution --origin-domain-name <BUCKET_NAME>.s3.amazonaws.com --default-root-object index.html
    
    ```
    
2. Copy the `ETag` and `Id` of the CloudFront distribution from within the returned JSON string and paste them into a text file, as we'll need them later.
3. Export the `Id` to an environment variable:
    
    ```
    export CF_ID=<Id value here>
    
    ```
    
4. Export the `ETag` to an environment variable:
    
    ```
    export CF_ETAG=<Id value here>
    
    ```
    

### Get and Update the CloudFront Distribution Config

1. Get the CloudFront distribution config and filter out the distribution-specific part:
    
    ```
    aws cloudfront get-distribution-config --id $CF_ID | jq -r .DistributionConfig > dist-config.json
    
    ```
    
2. List the files:
    
    ```
    ls
    
    ```
    
3. Open the newly created `dist-config.json` file:
    
    ```
    vim dist-config.json
    
    ```
    
4. Modify the following properties in the file *(NOTE: You will need the `OAI_Id`.)*:
    - `"OriginAccessIdentity"`: `"origin-access-identity/cloudfront/<OAI_ID>"`
    - `"PriceClass"`: `"PriceClass_100"`
    - `"ViewerProtocolPolicy"`: `"redirect-to-https"`
5. Save and exit the file:
    
    ```
    :wq
    
    ```
    

### Update CloudFront Distribution with the Modified `dist-config.json` File

1. Update the CloudFront distribution with the `dist-config.json` file:
    
    ```
    aws cloudfront update-distribution --id $CF_ID --distribution-config file://dist-config.json --if-match $CF_ETAG
    
    ```
    

### Test and Verify

1. In the AWS console, navigate to CloudFront Console.
2. Click on the only distribution listed to view the details; CloudFront
distributions take about 20 minutes to propagate, so give it some time
to complete before testing.
3. Click the **Origins** tab.
4. Select the radio button for the origin listed, and click **Edit**.
5. Review the settings, then click **Cancel** to exit.
6. Click the **Behaviors** tab and confirm the **Viewer protocol policy** is **Redirect HTTP to HTTPs**.
7. Click the **General** tab and copy the domain name.
8. Once it's deployed, paste the copied domain name to a new browser tab to to
verify our CloudFront distribution is serving the S3 static website
properly.

## Conclusion

Congratulations on successfully completing this hands-on lab!