# Working with CloudFormation Nested Stacks

## Introduction

In this hands-on lab, we will create a
 nested stack from the templates provided. We will use the two templates
 to create a nested stack that will implement a child template, which 
will then create an S3 bucket with a policy of no retain. This child 
template can be reused repeatedly whenever an S3 bucket of this type is 
needed.

## Solution

Log in with the credentials provided, and make sure you are in the `us-east-1` (N. Virginia) region.

Download the necessary files from the lab's [GitHub repository](https://github.com/ACloudGuru-Resources/Working-with-CloudFormation-Nested-Stacks).

### Create an S3 Bucket and Upload CloudFormation Templates

1. Navigate to S3.
2. Click **Create bucket**.
3. On the *Create bucket* screen, give your bucket a unique name. (**Note:** It must be all lowercase letters and be unique across *all* AWS accounts.)
4. Leave the rest of the configuration using default settings, and click **Create bucket**.
5. Click to open the bucket, and click **Upload**.
6. Click **Add files**, and select the `s3static.json` and `noretain.json` CloudFormation templates you downloaded earlier.
7. Accept the default settings and click **Upload**.
8. Click **Close**.

### Create a Nested Stack from the CloudFormation Root Template

1. In a new browser tab, navigate to CloudFormation.
2. Click **Create stack** and select **with new resources (standard)**.
3. In the *Prerequisite - Prepare template* section, select **Choose an existing template**.
4. Click **Upload a template file**.
5. Upload the `root.json` file from your local machine.
6. Click **View in Application Composer**.
7. Click **Template** in the upper-left of the right side of the screen.
    
    ```bash
    {
        "AWSTemplateFormatVersion" : "2010-09-09",
        "Resources" : {
            "myStack" : {
               "Type" : "AWS::CloudFormation::Stack",
               "Properties" : {
                  "TemplateURL" : "https://s3.amazonaws.com/<YOUR-BUCKET-NAME>/noretain.json",
                  "TimeoutInMinutes" : "60"
               }
            }
        }
    }
    
    ```
    
8. In the `"TemplateURL"` line, replace `<YOUR-BUCKET-NAME>` with the name of the bucket you just created.
9. Click **Create Template**.
10. Click **Confirm and continue to CloudFormation**.
11. Click **Next**.
12. On the stack details page, give it a *Stack name* of `s3webnoretain`.
13. Click **Next**.
14. Leave the settings on the stack options page, and click **Next**.
15. Check the boxes to accept the acknowledgements, and click **Submit**. It will take a few minutes for it to fully be created.

### Create a Nested Stack with Multiple Child Stacks

1. In the CloudFormation browser tab, click **Create stack** and select **with new resources (standard)**.
2. In the *Prerequisite - Prepare template* section, select **Choose an existing template**.
3. Click **Upload a template file**.
4. Click **Choose file**.
5. Select the `multinest.json` template, which is included in the lab GitHub repository.
6. Click **View in Application Composer**.
7. Click **Template** in the upper-left of the right side of the screen.
    
    ```bash
    {
        "AWSTemplateFormatVersion" : "2010-09-09",
        "Resources" : {
            "myStack" : {
               "Type" : "AWS::CloudFormation::Stack",
               "Properties" : {
                  "TemplateURL" : "https://s3.amazonaws.com/<YOUR-BUCKET-NAME>/s3static.json",
                  "TimeoutInMinutes" : "60"
               }
            },
            "myStack2" : {
                "Type" : "AWS::CloudFormation::Stack",
                "Properties" : {
                   "TemplateURL" : "https://s3.amazonaws.com/<YOUR-BUCKET-NAME>/noretain.json",
                   "TimeoutInMinutes" : "60"
                }
             }
        }
    }
    
    ```
    
8. In the two `"TemplateURL"` lines, replace `<YOUR-BUCKET-NAME>` with the name of your bucket.
9. Click **Create Template**.
10. Click **Confirm and continue to CloudFormation**.
11. Click **Next**.
12. On the stack details page, give it a *Stack name* of `multinestdemo`.
13. Click **Next**.
14. Leave the settings on the stack options page, and click **Next**.
15. Check the boxes to accept the acknowledgements, and click **Create stack**. It will take a few minutes for it to fully be created.

### Upload an `index.html` File and Verify the Page Loads

1. In the S3 browser tab, click to open one of the nested stack buckets.
2. Click **Upload**.
3. Click **Add files**, and select the `index.html` file included in the lab GitHub repository.
4. Accept the default settings, and click **Upload**.
5. In the CloudFormation browser tab, select the nested stack in which you uploaded the `index.html` file.
6. Click the **Outputs** tab, and browse to the listed *WebsiteURL*. It should display an Elastic Beanstalk web page, which is fine — all that matters is if it shows up alright.

## Conclusion

Congratulations on successfully completing this hands-on lab!