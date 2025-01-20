# Storing Container Data in AWS S3

## Introduction

Using Docker volumes is the preferred 
method of storing container data locally. Volume support is built 
directly into Docker, making it an easy tool to use for storage, as well
 as more portable. However, storing container data in Docker volumes 
still requires you to back up the data in those volumes on your own.

There is another option - storing your
 container data in the cloud. It's not a solution for every problem, but
 after this lab, you'll have another tool at your disposal.

This lab will show you how to mount an
 S3 bucket onto your local system as a directory. You will then mount 
that directory into your Docker container. We will use an `httpd` container to serve the contents of that bucket as a webpage, but you can use it to share any common data between containers.

This will demonstrate how flexible 
Docker can be. You can make changes to your bucket and all of your 
containers using the S3 bucket will near-instantly have access to the 
content.

## Solution

Log in to the server using the credentials provided:

```
ssh cloud_user@<PUBLIC_IP_ADDRESS>

```

### Configuration and Installation

1. Install the `awscli`, while checking if there are any versions currently installed, and not stopping any user processes:
    
    ```
    pip install --upgrade --user awscli
    
    ```
    
2. Configure the CLI:
    
    ```
    aws configure
    
    ```
    
3. Enter the following:
    - *AWS Access Key ID:* **<ACCESS_KEY_ID>**
    - *AWS Secret Access Key:* **<SECRET_ACCESS_KEY>**
    - *Default region name:* **us-east-1**
    - *Default output format:* **json**
4. Copy the CLI configuration to the `root` user:
    
    ```
    sudo cp -r ~/.aws /root
    
    ```
    
5. Install the `s3fs` package:
    
    ```
    sudo yum install s3fs-fuse -y
    
    ```
    

### Prepare the Bucket

1. Create a mount point for the s3 bucket:
    
    ```
    sudo mkdir /mnt/widget-factory
    
    ```
    
2. Export the bucket name:
    
    ```
    export BUCKET=<S3_BUCKET_NAME>
    
    ```
    
3. Enable the **allow_other** option in **fuse.conf** to make the S3 bucket mount accessible to non-root users or processes via:
    
    ```
    sudo sed -i 's/^# user_allow_other/user_allow_other/' /etc/fuse.conf
    
    ```
    
4. Mount the S3 bucket:
    
    ```
    sudo s3fs $BUCKET /mnt/widget-factory -o allow_other -o use_cache=/tmp/s3fs
    
    ```
    
5. Verify that the bucket was mounted successfully:
    
    ```
    ll /mnt/widget-factory
    
    ```
    
6. Copy the website files to the s3 bucket:
    
    ```
    cp -r ~/widget-factory-inc/web/* /mnt/widget-factory
    
    ```
    
7. Verify the files are in the folder:
    
    ```
    ll /mnt/widget-factory
    
    ```
    
8. Verify the files are in the s3 bucket:
    
    ```
    aws s3 ls s3://$BUCKET
    
    ```
    

### Use the S3 Bucket Files in a Docker Container

1. Run an `httpd` container using the S3 bucket:
    
    ```
    docker run -d --name web1 -p 80:80 --mount type=bind,source=/mnt/widget-factory,target=/usr/local/apache2/htdocs,readonly httpd:2.4
    
    ```
    
2. In a web browser, verify connectivity to the container:
    
    ```
    <SERVER_PUBLIC_IP_ADDRESS>
    
    ```
    
3. Check the bucket cache:
    
    ```
    
    ```
    
4. Change to the `/mnt/widget-factory/` directory:
    
    ```
    cd /mnt/widget-factory
    
    ```
    
5. Create a new page within the bucket:
    
    ```
    cp index.html newPage.html
    
    ```
    
6. In a web browser, verify that the new page is accessible:
    
    ```
    <SERVER_PUBLIC_IP_ADDRESS>/newPage.html
    
    ```
    
7. Verify that the page was added to the bucket:
    
    ```
    aws s3 ls $BUCKET
    
    ```
    

## Conclusion

Congratulations — you've completed this hands-on lab!