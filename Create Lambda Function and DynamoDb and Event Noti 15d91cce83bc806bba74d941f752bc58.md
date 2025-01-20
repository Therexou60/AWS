# Create Lambda Function and DynamoDb and Event Notif

# Guided Lab: Implementing a Serverless Architecture on AWS

## Lab overview and objectives

Traditionally, applications run on servers. These servers can be physical or virtual environments that run on top of physical servers. However, you must purchase and provision all these types of servers, and you must also manage their capacity. In contrast, you can run your code on AWS Lambda without needing to pre-allocate servers. With Lambda, you  need to provide only the code and define a trigger. The Lambda function can run when it is needed whether it is once a week or hundreds of times each second. You pay for only what you use.

This lab demonstrates how to invoke a Lambda function when a file is uploaded to Amazon Simple Storage Service (Amazon S3). The file will be loaded into an Amazon DynamoDB table. The data will be available for you to view on a dashboard page that retrieves the data directly from DynamoDB. This solution does not use Amazon Elastic Compute Cloud (Amazon EC2). It is a serverless solution that automatically scales when it is used. It also incurs little cost when it is in use. When it is idle, there is practically no cost because will you be billed for data storage only.

After completing this lab, you should be able to do the following:

- Implement a serverless architecture on AWS.
- Invoke Lambda functions from Amazon S3 and DynamoDB.
- Configure Amazon Simple Notification Service (Amazon SNS) to send notifications.

## Scenario

You are creating an inventory-tracking system. Stores from around the world will upload an inventory file to Amazon S3. Your team wants to be able to view the inventory levels and send a notification when inventory levels are low.

In this lab, you upload an inventory file to an S3 bucket, which initiates the following sequence of events:

- Invoke a Lambda function that reads the file and inserts items into a DynamoDB table.
- Another Lambda function receives updates from the DynamoDB table, which then sends a message to an SNS topic when an inventory item is out of stock.
- Amazon SNS then sends a notification through short message service (SMS) or email that requests additional inventory.
- A serverless, web-based dashboard application that uses Amazon Cognito to authenticate to Amazon Web Services (AWS) gains access to the DynamoDB table to display inventory levels.

At the end of this lab, your architecture will look like the following example:

![https://labs.vocareum.com/web/3683978/3711363.0/ASNLIB/public/docs/lang/en_us/images/module-14-guided-lab-architecture.png](https://labs.vocareum.com/web/3683978/3711363.0/ASNLIB/public/docs/lang/en_us/images/module-14-guided-lab-architecture.png)

| Step | Explanation |
| --- | --- |
| 1 | Upload an inventory file to an S3 bucket. |
| 2 | An Amazon S3 upload event calls the Lambda function. |
| 3 | The Lambda function insert items into the DynamoDB table. |
| 4 | The
 next Lambda function is invoked through Amazon DynamoDB Streams when 
the record is inserted. If no inventory is found, a notification is sent
 to an SNS topic. |

## Duration

This lab requires approximately **40 minutes** to complete.

## AWS service restrictions

In this lab environment, access to AWS services and service actions might be restricted to the ones that are needed to complete the lab instructions. You might encounter errors if you attempt to access other services or perform actions beyond the ones that are described in this lab.

## Accessing the AWS Management Console

1. At the top of these instructions, choose  **Start Lab**.
    - The lab session starts.
    - A timer displays at the top of the page and shows the time remaining in the session.
    
    **Tip**: If you need more time to complete the lab, choose **Start Lab** again to restart the timer for the environment.
    
    - Before you continue, wait until the circle icon to the right of AWS  in the upper-left corner turns green.
2. At the top of these instructions, choose the green circle next to **AWS** .
    
    This option opens the AWS Management Console in a new browser tab. The system automatically signs you in.
    
    **Tip**: If a new browser tab does not open, a banner or icon at the top of your browser will indicate that your browser is preventing the site from opening pop-up windows. Choose the banner or icon, and choose **Allow pop-ups**.
    
3. Arrange the AWS Management Console tab so that it displays along side these instructions. Ideally, you should be able to see both browser tabs at the same time so that you can follow the lab steps.
    
    Do not change the lab Region unless specifically instructed to do so.
    

## Task 1: Creating a Lambda function to load data

In this task, you create a Lambda function that processes an inventory file. The Lambda function reads the file and inserts information into a DynamoDB table.

1. On the AWS Management Console, in the search box, enter and choose `Lambda` to open the Lambda console.
2. Choose **Create function**.
    
    *Blueprints* are code templates for writing Lambda functions. Blueprints are provided for standard Lambda triggers, such as creating Amazon Alexa skills and processing Amazon Kinesis Data Firehose streams. This lab provides you with a pre-written Lambda function, so you use the **Author from scratch** option.
    
3. Configure the following settings:
    - For **Function name**, enter `Load-Inventory`.
    - For **Runtime**, choose **Python 3.8**.
    - Expand  **Change default execution role**, and configure the following options:
        - For **Execution role**, choose **Use an existing role**.
        - For **Existing role**, choose **Lambda-Load-Inventory-Role**.
    
    This role gives the Lambda function permission to access Amazon S3 and DynamoDB.
    
4. Choose **Create function**.
5. In the **Code source** section, in the **Environment** pane, choose **lambda_function.py**.
6. In the code editor for the **lambda_function.py** file, delete all the default code.
7. In the **Code source** editor, copy and paste the following code:

```

# Load-Inventory Lambda function## This function is invoked by an object being created in an Amazon S3 bucket.# The file is downloaded and each line is inserted into a DynamoDB table.import json, urllib, boto3, csv# Connect to S3 and DynamoDBs3 = boto3.resource('s3')dynamodb = boto3.resource('dynamodb')# Connect to the DynamoDB tablesinventoryTable = dynamodb.Table('Inventory');# This handler is run every time the Lambda function is invokeddef lambda_handler(event, context):  # Show the incoming event in the debug log  print("Event received by Lambda function: " + json.dumps(event, indent=2))  # Get the bucket and object key from the Event  bucket = event['Records'][0]['s3']['bucket']['name']  key = urllib.parse.unquote_plus(event['Records'][0]['s3']['object']['key'])  localFilename = '/tmp/inventory.txt'  # Download the file from S3 to the local filesystem  try:    s3.meta.client.download_file(bucket, key, localFilename)  except Exception as e:    print(e)    print('Error getting object {} from bucket {}. Make sure they exist and your bucket is in the same region as this function.'.format(key, bucket))    raise e  # Read the Inventory CSV file  with open(localFilename) as csvfile:    reader = csv.DictReader(csvfile, delimiter=',')    # Read each row in the file    rowCount = 0    for row in reader:      rowCount += 1      # Show the row in the debug log      print(row['store'], row['item'], row['count'])      try:        # Insert Store, Item and Count into the Inventory table        inventoryTable.put_item(          Item={            'Store':  row['store'],            'Item':   row['item'],            'Count':  int(row['count'])})      except Exception as e:         print(e)         print("Unable to insert data into DynamoDB table".format(e))    # Finished!    return "%d counts inserted" % rowCount
```

Examine the code. It performs the following steps:

- Downloads the file from Amazon S3 that invokes the event
- Loops through each line in the file
- Inserts the data into the DynamoDB **Inventory** table
1. At the top of the **Code source** section, choose **File** and then choose **Save**
    - Then **Deploy** your changes.
    
    Next, you configure Amazon S3 to invoke the Lambda function when a file is uploaded.
    

## Task 2: Configuring an Amazon S3 event

Stores from around the world provide inventory files to load into the inventory tracking system. Instead of uploading their files through FTP, the stores can upload them directly to Amazon S3. They can upload the files through a webpage, through a script, or as part of a program. When a file is received, it invokes the Lambda function. This Lambda function then loads the inventory into a DynamoDB table.

In this task, you create an S3 bucket and configure it to invoke the Lambda function.

1. On the AWS Management Console, in the search box, enter and choose `S3`.
2. Choose **Create bucket**.
    
    Each bucket must have a unique name, so add a random number to the bucket name (for example, **inventory-123**).
    
3. For **Bucket name** enter `inventory-<number>`, and replace *<number>* with a random number.
4. Choose **Create bucket**.
    
     You might receive an error that states, *The requested bucket name is not available.* If you get this error, choose the first **Edit** link, change the bucket name, and try again until the bucket name is accepted.
    
    You now configure the bucket to automatically invoke the Lambda function when a file is uploaded.
    
5. Choose the name of your **inventory-<number>** bucket.
6. Choose the **Properties** tab.
    
    Next, you configure an event to invoke when an object is created in the S3 bucket.
    
7. In the **Event notifications** section, choose **Create event notification**, and then configure these settings:
    - **Event name:** Enter `Load-Inventory`.
    - **Event types:** Choose **All object create events**.
    - **Destination:** Choose **Lambda function**.
    - **Lambda function:** Choose **Load-Inventory**.
8. Choose **Save changes**.
    
    When an object is created in the bucket, this configuration tells Amazon S3 to invoke the **Load-Inventory** Lambda function that you created earlier.
    
    Your bucket is now ready to receive inventory files.
    

## Task 3: Testing the loading process

You are now ready to test the loading process. You upload an inventory file and then check that it loaded successfully.

1. Download the inventory files by opening (right-clicking) the context menu for these links:
    - [inventory-berlin.csv](https://labs.vocareum.com/web/3683978/3711363.0/ASNLIB/public/scripts/inventory-berlin.csv)
    - [inventory-calcutta.csv](https://labs.vocareum.com/web/3683978/3711363.0/ASNLIB/public/scripts/inventory-calcutta.csv)
    - [inventory-karachi.csv](https://labs.vocareum.com/web/3683978/3711363.0/ASNLIB/public/scripts/inventory-karachi.csv)
    - [inventory-pusan.csv](https://labs.vocareum.com/web/3683978/3711363.0/ASNLIB/public/scripts/inventory-pusan.csv)
    - [inventory-shanghai.csv](https://labs.vocareum.com/web/3683978/3711363.0/ASNLIB/public/scripts/inventory-shanghai.csv)
    - [inventory-springfield.csv](https://labs.vocareum.com/web/3683978/3711363.0/ASNLIB/public/scripts/inventory-springfield.csv)

These files are the inventory files that you can use to test the system. They are comma-separated values (CSV) files. The following example shows the contents of the Berlin file:

```

  store,item,count  Berlin,Echo Dot,12  Berlin,Echo (2nd Gen),19  Berlin,Echo Show,18  Berlin,Echo Plus,0  Berlin,Echo Look,10  Berlin,Amazon Tap,15
```

1. Choose the **Objects** tab.
2. Choose **Upload**.
3. Choose **Add files**, and choose one of the inventory .csv files that you downloaded earlier in this task. You can choose any inventory file.
4. Choose **Upload**.
    
    Amazon S3 automatically invokes the Lambda function, which loads the data into a DynamoDB table.
    
    A serverless dashboard application has been provided for you to view the results.
    
5. At the top of these instructions, choose **AWS Details**.
6. From the window, copy the **Dashboard** URL.
7. Copy and paste the URL into a new web browser tab.
    
    The dashboard application opens and displays the inventory data that you loaded into the bucket. The data is retrieved from DynamoDB, which proves that the upload successfully invoked the Lambda function.
    

![https://labs.vocareum.com/web/3683978/3711363.0/ASNLIB/public/docs/lang/en_us/images/module-14-guided-lab-task-3-2.png](https://labs.vocareum.com/web/3683978/3711363.0/ASNLIB/public/docs/lang/en_us/images/module-14-guided-lab-task-3-2.png)

If the dashboard application does not display any information, ask your instructor to help you diagnose the problem.

The dashboard application is served as a static webpage from Amazon S3. The dashboard authenticates through Amazon Cognito as an anonymous user, which provides sufficient permissions for the dashboard to retrieve data from DynamoDB.

You can also view the data directly in the DynamoDB table.

1. On the AWS Management Console, in the search box, enter and choose `DynamoDB`.
2. In the left navigation pane, choose **Tables**.
3. Choose the **Inventory** table.
4. Choose **Explore table items**.
    
    The data from the inventory file is displayed. It shows the **Store**, **Item**, and **Count**.
    

## Task 4: Configuring notifications

You want to notify inventory management staff when a store runs out of stock for an item. For this serverless notification functionality, you use Amazon SNS.

1. On the AWS Management Console, in the search box, enter and choose `SNS`.
2. In the **Create topic** section, for **Topic name**, enter `NoStock`.
3. Choose **Next step**.
4. On the **Create topic** page, keep **Standard** selected.
5. Choose **Create topic**.
    
    To receive notifications, you must subscribe to the topic. You can choose to receive notifications through several methods, such as SMS and email.
    
6. On the **NoStock** topic page, in the **Subscriptions** section, choose **Create subscription**.
7. On the **Create subscription** page, configure these settings:
    - **Protocol:** Choose **Email**.
    - **Endpoint:** Enter your email address.
8. Choose **Create subscription**.
    
    After you create an email subscription, you will receive a confirmation email message.
    
9. To confirm your subscription, open the email message, and choose the **Confirm subscription** link.
    
    Any message that is sent to the SNS topic will be forwarded to your email.
    

## Task 5: Creating a Lambda function to send notifications

You could modify the existing Load-Inventory Lambda function to check inventory levels while the file is being loaded. However, this configuration is not a good architectural practice. Instead of overloading the Load-Inventory function with business logic, you create another Lambda function that is invoked when data is loaded into the DynamoDB table. A DynamoDB stream invokes this function.

This architectural approach offers several benefits:

- Each Lambda function performs a single, specific function. This practice makes the code clearer and more maintainable.
- Additional business logic can be added by creating additional Lambda functions. Each function operates independently, so existing functionality is not impacted.

In this task, you create another Lambda function that looks at inventory while it is loaded into the DynamoDB table. If the Lambda function notices that an item is out of stock, it sends a notification through the SNS topic that you created earlier.

1. On the AWS Management Console, in the search box, enter and choose `Lambda`.
2. Choose **Create function**.
3. Configure these settings:
    - For **Function name**, enter `Check-Stock`.
    - For **Runtime**, choose **Python 3.8**.
    - Expand  **Change default execution role**, and configure the following options:
        - For **Execution role**, choose **Use an existing role**.
        - For **Existing role**, choose **Lambda-Check-Stock-Role**.
4. Choose **Create function**.
    
    This role was configured with permissions to send a notification to Amazon SNS.
    
5. In the **Code source** section, in the **Environment** pane, choose **lambda_function.py**.
6. In the code editor for the **lambda_function.py** file, delete the code.
7. In the **Code source** editor, copy and paste the following code:
    
    ```
    
    # Stock Check Lambda function## This function is invoked when values are inserted into the Inventory DynamoDB table.# Inventory counts are checked and if an item is out of stock, a notification is sent to an SNS Topic.import json, boto3# This handler is run every time the Lambda function is invokeddef lambda_handler(event, context):  # Show the incoming event in the debug log  print("Event received by Lambda function: " + json.dumps(event, indent=2))  # For each inventory item added, check if the count is zero  for record in event['Records']:    newImage = record['dynamodb'].get('NewImage', None)    if newImage:      count = int(record['dynamodb']['NewImage']['Count']['N'])      if count == 0:        store = record['dynamodb']['NewImage']['Store']['S']        item  = record['dynamodb']['NewImage']['Item']['S']        # Construct message to be sent        message = store + ' is out of stock of ' + item        print(message)        # Connect to SNS        sns = boto3.client('sns')        alertTopic = 'NoStock'        snsTopicArn = [t['TopicArn'] for t in sns.list_topics()['Topics']                        if t['TopicArn'].lower().endswith(':' + alertTopic.lower())][0]        # Send message to SNS        sns.publish(          TopicArn=snsTopicArn,          Message=message,          Subject='Inventory Alert!',          MessageStructure='raw'        )  # Finished!  return 'Successfully processed {} records.'.format(len(event['Records']))
    ```
    
    Examine the code. It performs the following steps:
    
    - It loops through the incoming records.
    - If the inventory count is zero, it sends a message to the **NoStock** SNS topic.
    
    You now configure the function so it is called when data is added to the Inventory table in DynamoDB.
    
8. To save your changes, choose**File** and then choose **Save**
    - Then choose **Deploy**.
9. In the **Function overview** section, choose **Add trigger**, and configure these settings:
    - **Select a source:** Choose **DynamoDB**.
    - **DynamoDB table:** Choose **Inventory**.
10. Choose **Add**.
    
    You are now ready to test the system.
    

## Task 6: Testing the system

You now upload an inventory file to Amazon S3, which invokes the original Load-Inventory function. This function loads data into DynamoDB, which then invokes the new Check-Stock Lambda function. If the Lambda function detects an item with zero inventory, it sends a message to Amazon SNS. Then, Amazon SNS notifies you through SMS or email.

1. On the AWS Management Console, in the search box, enter and choose `S3`.
2. Choose the name of your **inventory-<number>** bucket.
3. Choose **Upload**.
4. On the **Upload** page, choose **Add files**, and upload a different inventory file.
5. Return to the **Inventory System** dashboard browser tab, and refresh  the page.
    
    You should now be able to use the **All Stores** menu to view the inventory from both stores.
    
    Also, you should receive a notification through SMS or email that the store has an out-of-stock item (each inventory file has one item that is out of stock).
    
    If you did not receive a notification, wait a few minutes and upload a different inventory file. The DynamoDB trigger can sometimes take a few minutes to enable.
    
6. Try to upload multiple inventory files at the same time.

What do you think will happen?

## Conclusion

Congratulations! You have successfully done the following:

- Implemented a serverless architecture on AWS
- Invoked Lambda functions from Amazon S3 and Amazon DynamoDB
- Configured Amazon SNS to send notifications

## Submitting your work

1. At the top of these instructions, choose **Submit** to record your progress, and when prompted, choose Yes.
2. If the results don't display after a couple of minutes, return to the top of these instructions and choose Grades.
    
    **Tip**: You can submit your work multiple times. After you change your work, choose **Submit** again. Your last submission is what will be recorded for this lab.
    
3. To find detailed feedback on your work, choose **Submission Report**.

## Lab complete

Congratulations! You have completed the lab.

1. AAt the top of this page, choose **End Lab**, and then choose Yes to confirm that you want to end the lab.
    
    The message "Ended AWS Lab Successfully" is briefly displayed, indicating that the lab has ended.
    

*© 2023 Amazon Web Services, Inc. and its affiliates. All rights reserved. This work may not be reproduced or redistributed, in whole or in part, without prior written permission from Amazon Web Services, Inc. Commercial copying, lending, or selling is prohibited.*