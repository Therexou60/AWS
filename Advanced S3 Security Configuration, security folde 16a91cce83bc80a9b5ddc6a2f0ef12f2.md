# Advanced S3 Security Configuration, security folder in bucket

## Introduction

In this lab, we will configure 
permissions for a provided S3 bucket to provide appropriate access to a 
team of users, as well as a public prefix within the S3 bucket.

## Additional Information and Resources

You've been asked by a team of developers to secure the S3 bucket which will store the data from an upcoming project. The *team-data...* bucket has been provided in the account and has the following prefix structure:

```
/
/public
/team
/brock
/john
/lizzie

```

When you have completed your bucket security configuration, the following access patterns should function:

- Anyone should be able to read from the `/public` prefix if they have the full URL of the object they are attempting to access.
- All team members (Brock, John, and Lizzie) should be able to list the contents of and read and write to the `/team` prefix.
- Each user should be able to list the contents of and perform read and write
operations to the prefix that matches their username.

The provided environment includes an EC2 instance containing AWS CLI profiles with credentials for each user.

### Lab Resources

- [Lab Solution Files](https://github.com/linuxacademy/Content-AWS-Certified-Data-Analytics---Speciality/tree/master/Lab_Assets/advanced_s3_security_configuration)
- [S3 Bucket Policy](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucket-policies.html)
- [IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)
- [IAM Groups](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_groups.html)

## Solution

Log in to the AWS Management Console using the credentials provided for the lab.

### Investigate the Lab Environment

1. Navigate to the AWS Management Console.
2. Click the CloudShell icon in the top right to open in a new browser tab.
3. Close out of the *Welcome to AWS CloudShell* pop-up window and let the environment spin up.
4. Navigate back to the AWS Management Console tab.
5. Navigate to S3 using the *Services* menu or the unified search bar. You should have a `team-data-<ACCOUNT_NUMBER>` bucket provided for you.
6. Open the `team-data-<ACCOUNT_NUMBER>` bucket and click each of the folders, one at a time. Each folder should contain a .txt object.
7. Navigate to IAM using the *Services* menu or the unified search bar.
8. Click **Users:4** link. You should see 4 users: `brock`, `john`, `lizzie`, and `cloud_user`.
9. Select any user. Note these users does not have any permissions and is not currently assigned to any groups.
10. Navigate back to the AWS CloudShell tab and SSH into the EC2 instance using the credentials provided for the lab:
    
    ```
    ssh cloud_user@<PUBLIC_IP_ADDRESS>
    
    ```
    
11. Type `yes` to continue connecting and enter the password provided for the lab.
12. Run the `ls` and `cat` commands to view the contents of the instance. You should see profiles for `john`, `brock`, and `lizzie`.
    
    ```
    ls
    cat .aws/credentials
    
    ```
    
13. Try to view the ListBuckets in the account using `john`. You should receive an *Access Denied* error.
    
    ```
    aws s3 ls --profile john
    
    ```
    

### Provide Appropriate Public Access

1. Go back to the AWS Management Console and navigate to S3.
2. Select the `team-data-<ACCOUNT_NUMBER>` bucket, then select the **Permissions** tab.
3. Under *Block public access (bucket settings)*, click **Edit**.
4. Clear the *Block all public access* box, click **Save changes** and **Confirm**.
5. Scroll down to the *Bucket policy* section, click **Edit**.
6. Copy the policy provided in the [lab resources](https://github.com/linuxacademy/Content-AWS-Certified-Data-Analytics---Speciality/tree/master/Lab_Assets/advanced_s3_security_configuration) and paste it into the *Policy* editor.
    
    ```
    {
     "Version":"2012-10-17",
     "Statement":[
       {
         "Sid":"PublicRead",
         "Effect":"Allow",
         "Principal": "*",
         "Action":[
             "s3:GetObject",
             "s3:GetObjectVersion"
           ],
         "Resource":[
             "arn:aws:s3:::<TEAM-DATA>/public/*"
           ]
       }
     ]
    }
    
    ```
    
7. Above the *Policy* editor, copy the `team-data-<ACCOUNT_NUMBER>` portion of the bucket ARN, then paste it into the `<TEAM-DATA>` placeholder in the policy.
8. At the bottom of the page, click **Save changes**. The S3 bucket now displays a *Publicly accessible* warning.
9. Select the **Amazon S3** breadcrumb to go back to the bucket root.
10. Go back to CloudShell and copy the *public.txt* object to the current directory. Be sure to replace `<ACCOUNT_NUMBER>` with your account number for the lab.
    
    ```
    aws s3 cp s3://team-data-<ACCOUNT_NUMBER>/public/public.txt . --profile john
    
    ```
    
11. Run `cat` on the *public.txt* object. You should see a *Hello everyone!* response, indicating you have successfully provided public access to the *public/* folder.
    
    ```
    cat public.txt
    
    ```
    
12. Go back to the AWS Management Console and open the `team-data-<ACCOUNT_NUMBER>` bucket, then select the **public/** folder.
13. Select the **public.txt** object, then open the *Object URL* in a separate browser. You should see a *Hello, everyone!* text.

### Provide Appropriate Team and User Access

### Create User and Group Policies

1. Go back to the AWS Management Console and navigate to IAM using the *Services* menu or the unified search bar.
2. In the sidebar menu, select **Policies**, then click **Create policy**.
3. Select the **JSON** tab, then copy the user policy provided in the [lab resources](https://github.com/linuxacademy/Content-AWS-Certified-Data-Analytics---Speciality/tree/master/Lab_Assets/advanced_s3_security_configuration) and paste it into the *JSON* policy editor.
    
    ```
    {
      "Version": "2012-10-17",
      "Statement":[
       {
          "Sid":"AllowList",
          "Action":[
              "s3:ListBucket"
             ],
          "Effect":"Allow",
          "Resource":[
              "arn:aws:s3:::<TEAM-DATA>"
             ],
          "Condition":{
             "StringLike":{
                 "s3:prefix":[
                     "${aws:username}/*"
                 ]
             }
          }
       },
       {
         "Sid":"AllowUserToReadWrite",
         "Action":[
             "s3:GetObject",
             "s3:PutObject"
         ],
         "Effect":"Allow",
         "Resource":[
             "arn:aws:s3:::<TEAM-DATA>/${aws:username}/*"
         ]
       }
    ]
    }
    
    ```
    
4. Copy your `team-data-<ACCOUNT_NUMBER>` S3 bucket name and paste it into any `<TEAM-DATA>` placeholders in the *JSON* policy editor.
5. Click **Next: Tags**. We are not adding any tags right now.
6. Click **Next: Review**.
7. In the *Name* field, enter "team-data-users" and no description is necessary.
8. Click **Create policy**.
9. Back at the IAM dashboard, click **Create policy** to create another policy.
10. Select the **JSON** tab, then copy the group policy provided in the [lab resources](https://github.com/linuxacademy/Content-AWS-Certified-Data-Analytics---Speciality/tree/master/Lab_Assets/advanced_s3_security_configuration) and paste it into the *JSON* policy editor.
    
    ```
    {
       "Version": "2012-10-17",
       "Statement":[
        {
           "Sid":"AllowList",
           "Action":[
               "s3:ListBucket"
           ],
           "Effect":"Allow",
           "Resource":[
               "arn:aws:s3:::<TEAM-DATA>"
           ],
           "Condition":{
              "StringLike":{
                  "s3:prefix":[
                      "team/*"
                  ]
              }
           }
        },
        {
          "Sid":"AllowGroupToReadWrite",
          "Action":[
              "s3:GetObject",
              "s3:PutObject"
          ],
          "Effect":"Allow",
          "Resource":[
              "arn:aws:s3:::<TEAM-DATA>/team/*"
          ]
        }
     ]
    }
    
    ```
    
11. Copy your `team-data-<ACCOUNT_NUMBER>` S3 bucket name and paste it into any `<TEAM-DATA>` placeholders in the *JSON* policy editor.
12. Click **Next: Tags**. We do not need any tags.
13. Click **Next: Review**.
14. In the *Name* field, enter "team-data-group" and no description is necessary.
15. Click **Create policy**.

### Apply the User and Group Policies

1. After the group policy is created, use the sidebar menu to select **Users** to view users.
2. In the sidebar menu, click **Groups**.
3. Click **Create New Group**.
4. In the *Group Name* field, enter "team-data".
5. Click **Next Step**.
6. In the *Policy Type* field, search `team-data`.
7. Select the 2 created polices:

```
* *team-data-group*
* *team-data-users*

```

1. Click **Next Step**.
2. Verify the *Group Name* and *Policies* and click **Create Group**.
3. In the IAM dashboard, select `team-data` group.
4. Click **Add Users to Group**.
5. In the *Add Users to Group* section, select **brock**, **john**, and **lizzie**.
6. Click **Add Users**.
7. Select the **Permissions** tab to confirm group policies.
8. In the *Attach permissions policies* section, use the search bar to search for `team-data`. Your two *team-data* policies display.
9. Select the **team-data-group** and **team-data-users** policies.
10. Review the group details, then click **Create Group**. The *team-data* group is created, and any users added to this group will inherit permissions from the *team-data-group* and *team-data-users* policies.
11. Click the **team-data** group name to open the group. The users now display on the group's *Users* tab, and you can select the *Permissions* tab to verify the *team-data* policies are attached to the group.

### Test Permissions

1. Navigate back to the CloudShell browser tab.
2. Clear the terminal:
    
    ```
    clear
    
    ```
    
3. Run a listing to verify `john` can view the contents of the *team/* prefix in your S3 bucket. Be sure to replace `<ACCOUNT_NUMBER>` with your account number for the lab. *(NOTE: You should now see the team.txt object.)*
    
    ```
    aws s3 ls team-data-<ACCOUNT_NUMBER>/team/ --profile john
    
    ```
    
4. Run the same command for `brock` and `lizzie` to verify they can also view the contents of the *team/* prefix in your S3 bucket.
    
    ```
    aws s3 ls team-data-<ACCOUNT_NUMBER>/team --profile brock
    aws s3 ls team-data-<ACCOUNT_NUMBER>/team --profile lizzie
    
    ```
    
5. Try to download the *team.txt* object without adding profile credentials. Be sure to replace `<ACCOUNT_NUMBER>` with your account number for the lab. You should receive an error message.
    
    ```
    aws s3 cp s3://team-data-<ACCOUNT_NUMBER>/team/team.txt .
    
    ```
    
6. Run the same command, but this time add profile credentials for `john`. The command should now be successful.
    
    ```
    aws s3 cp s3://team-data-<ACCOUNT_NUMBER>/team/team.txt . --profile john
    
    ```
    
7. Run a `cat` command on *team.txt*. You should see a *HI TEAM!* message.
    
    ```
    cat team.txt
    
    ```
    
8. Verify access to the *team.txt* object is not public:
    - Go back to the AWS Management Console and navigate to S3.
    - Select the `team-data-<ACCOUNT_NUMBER>` bucket name, then open the **team/** folder.
    - Select the **team.txt** object name, then click on the *Object URL* in a separate browser. You should see an *Access Denied* error.
9. Navigate back to CloudShell and download the *team.txt* object using profile credentials for `brock` and `lizzie`. Be sure to replace `<ACCOUNT_NUMBER>` with your account number for the lab. The command should be successful for both user profiles.
    
    ```
    aws s3 cp s3://team-data-<ACCOUNT_NUMBER>/team/team.txt . --profile brock
    aws s3 cp s3://team-data-<ACCOUNT_NUMBER>/team/team.txt . --profile lizzie
    
    ```
    
10. Run the `touch` command to verify all 3 users can write to your S3 bucket prefixes. You should see the 3 .txt objects you entered in the command as well as the *public.txt* and *team.txt* objects.
    
    ```
    touch john_write.txt brock_write.txt lizzie_write.txt
    
    ```
    
11. Verify files were successfully created:
    
    ```
    ls
    
    ```
    
12. Verify all 3 users can write to the *team/* prefix. Be sure to replace `<ACCOUNT_NUMBER>` with your account number for the lab.
    
    ```
    aws s3 cp john_write.txt s3://team-data-<ACCOUNT_NUMBER>/team/ --profile john
    aws s3 cp brock_write.txt s3://team-data-<ACCOUNT_NUMBER>/team/ --profile brock
    aws s3 cp lizzie_write.txt s3://team-data-<ACCOUNT_NUMBER>/team/ --profile lizzie
    
    ```
    
13. Navigate back to the AWS Management Console and open the **team/** folder in your S3 bucket. You should now see the *john_write.txt*, *brock_write.txt*, and *lizzie_write.txt* objects you uploaded.
14. Go back to CloudShell and ensure `john` can access his own user folder. Be sure to replace `<ACCOUNT_NUMBER>` with your account number for the lab. You should see the *john.txt* object.
    
    ```
    aws s3 ls team-data-<ACCOUNT_NUMBER>/john/ --profile john
    
    ```
    
15. Test whether the `brock` and `lizzie` user profiles can access the `john` folder. You should see an *Access Denied* error, indicating that only `john` can access his own folder.
    
    ```
    aws s3 ls team-data-<ACCOUNT_NUMBER>/john/ --profile brock
    aws s3 ls team-data-<ACCOUNT_NUMBER>/john/ --profile lizzie
    
    ```
    
16. Verify the `john` user profile can write to the *john/* folder.
    
    ```
    aws s3 cp s3://team-data-<ACCOUNT_NUMBER>/john/john.txt . --profile john
    
    ```
    
17. View *john.txt*. You should see an ASCII image of `john`.
    
    ```
    cat john.txt
    
    ```
    
18. Ensure all 3 user profiles can write to their own folder. Be sure to replace `<ACCOUNT_NUMBER>` with your account number for the lab.
    
    ```
    aws s3 cp john_write.txt s3://team-data-<ACCOUNT_NUMBER>/john/ --profile john
    aws s3 cp brock_write.txt s3://team-data-<ACCOUNT_NUMBER>/brock/ --profile brock
    aws s3 cp lizzie_write.txt s3://team-data-<ACCOUNT_NUMBER>/lizzie/ --profile lizzie
    
    ```
    
19. Navigate back to the S3 dashboard and the **team/** folder.
20. Confirm there are 4 objects listed:
    - *brock_write.txt*
    - *john_write.txt*
    - *lizie_write.txt*
    - *team.txt*
21. Click on **team-data-<ACCOUNT_NUMBER>** bucket root.
22. Select each user folder and verify they all contain *user_write.txt* and *user.txt* objects.

## Conclusion

Congratulations — you've completed this hands-on lab!