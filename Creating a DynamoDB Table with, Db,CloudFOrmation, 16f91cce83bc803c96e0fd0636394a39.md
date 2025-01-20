# Creating a DynamoDB Table with, Db,CloudFOrmation,Python

## Introduction

In this hands-on lab, we will create a
 DynamoDB table to store pet inventory data. In the scenario, the data 
architect on the team has requested a table definition with a partition 
key of `pet_species` with the data type `String`, a sort key of `pet_id` with the data type `Number`, and that the table be named `PetInventory`. Because the workload in development is not known, `On-Demand`
 provisioning should be utilized. We're free to create the table with 
any method we choose (web console, CLI/SDK, CloudFormation).

## Solution

Log in to the live AWS environment using the `cloud_user` credentials provided on the lab page.

### Create a DynamoDB Table Using the AWS Web Console

1. Navigate to DynamoDB.
2. Click **Create table**.
3. Set the following values:
    - *Table name*: **PetInventory**
    - *Primary key*:
        - *Partition key*: **pet_species**, **String**
        - Check **Add sort key**
        - *Sort key*: **pet_id**, **Number**
4. In the *Table settings* section, un-check **Use default settings**.
5. In the *Read/write capacity mode* section, select **On-demand**.
6. Leave the rest of the settings as-is, and click **Create**.
7. Once it's created, click **Delete table**.
8. In the *Delete table* confirmation dialog box, click **Delete**.

### Create a DynamoDB Table Using the AWS CLI

1. Open a terminal session and log in to our instance via SSH using the Cloud Server credentials provided on the lab page.
2. Enter the following `create-table` command in the terminal:
    
    ```
    aws dynamodb\
        create-table\
            --table-name PetInventory\
            --attribute-definitions\
                AttributeName=pet_species,AttributeType=S\
                AttributeName=pet_id,AttributeType=N\
            --key-schema\
                AttributeName=pet_species,KeyType=HASH\
                AttributeName=pet_id,KeyType=RANGE\
            --billing-mode PAY_PER_REQUEST
    
    ```
    
    We should then see a JSON object returned.
    
3. Check the table was created:
    
    ```
    aws dynamodb describe-table --table-name PetInventory
    
    ```
    
    We should then see a JSON object returned — this time with an `ACTIVE` `TableStatus`.
    
4. List our tables:
    
    ```
    aws dynamodb list-tables
    
    ```
    
    We should just see our one table listed.
    
5. Verify the table was created as requested:
    
    ```
    aws dynamodb describe-table --table-name PetInventory --query 'Table.{PartitionKey:KeySchema[0].AttributeName,PartKeyType:AttributeDefinitions[1].AttributeType,SortKey:KeySchema[1].AttributeName,SortKeyType:AttributeDefinitions[0].AttributeType,BillingMode:BillingModeSummary.BillingMode}'
    
    ```
    
6. If the table was created as requested, the output will be:
    
    ```
    {
        "SortKeyType": "N",
        "PartitionKey": "pet_species",
        "BillingMode": "PAY_PER_REQUEST",
        "SortKey": "pet_id",
        "PartKeyType": "S"
    }
    
    ```
    
7. Delete the table:
    
    ```
    aws dynamodb delete-table --table-name PetInventory
    
    ```
    
8. To ensure the table is deleted, enter:
    
    ```
    aws dynamodb describe-table --table-name PetInventory
    
    ```
    
    This should result in an error message saying `PetInventory` was not found, which means the table was successfully deleted.
    
9. To further verify it was deleted, enter:
    
    ```
    aws dynamodb list-tables
    
    ```
    
    This will display that there are no tables.
    

### Create a DynamoDB Table with the Python Boto3 SDK

1. Open a terminal session and log in to our instance via SSH using the Cloud Server credentials provided on the lab page.
2. Launch the Python 3 console:
    
    ```
    python3
    
    ```
    
3. Import Boto 3:
    
    ```
    import boto3
    
    ```
    
4. Create a client object:
    
    ```
    ddb = boto3.client('dynamodb')
    
    ```
    
5. Create the requested DynamoDB table:
    
    ```
    createResponse = ddb.create_table(
        AttributeDefinitions=[
            {
                'AttributeName':'pet_species',
                'AttributeType': 'S',
            },
            {
                'AttributeName':'pet_id',
                'AttributeType':'N'
            }
        ],
        KeySchema=[
            {
                'AttributeName':'pet_species',
                'KeyType':'HASH'
            },
            {
                'AttributeName':'pet_id',
                'KeyType':'RANGE'
            },
        ],
        BillingMode = 'PAY_PER_REQUEST',
        TableName='PetInventory'
    )
    
    ```
    
6. View the data in the response:
    
    ```
    print(createResponse)
    
    ```
    
7. Narrow the data down to the `TableDescription`:
    
    ```
    createResponse['TableDescription']
    
    ```
    
8. Drill further down into the data by adding more keys:
    
    ```
    createResponse['TableDescription']['AttributeDefinitions']
    
    ```
    
9. Take a look at the key schema:
    
    ```
    createResponse['TableDescription']['KeySchema']
    
    ```
    
10. Check the table status:
    
    ```
    createResponse['TableDescription']['TableStatus']
    
    ```
    
11. Look at the billing mode:
    
    ```
    createResponse['TableDescription']['BillingModeSummary']
    
    ```
    
12. Get the table name:
    
    ```
    createResponse['TableDescription']['TableName']
    
    ```
    
13. Create the variable to check the current status of the table:
    
    ```
    statusResponse = ddb.describe_table(TableName='PetInventory')
    
    ```
    
    No output means there were no errors with the API call.
    
14. Enter the `statusResponse` variable:
    
    ```
    statusResponse
    
    ```
    
15. Add in some keys:
    
    ```
    statusResponse['Table']['TableStatus']
    
    ```
    
    It will result in `ACTIVE`, which was its status when the call was made.
    
16. Take a look at what's available:
    
    ```
    statusResponse['Table']
    
    ```
    
17. Create the variable to list the tables we have available in this region:
    
    ```
    listResponse = ddb.list_tables()
    
    ```
    
18. Run `listResponse`:
    
    ```
    listResponse
    
    ```
    
19. Check the table names:
    
    ```
    listResponse['TableNames']
    
    ```
    
20. Delete the table:
    
    ```
    deleteResponse = ddb.delete_table(TableName = 'PetInventory')
    
    ```
    
21. Look at the `deleteReponse` variable to see what's available:
    
    ```
    deleteResponse
    
    ```
    
22. Add the table description key for this response:
    
    ```
    deleteResponse['TableDescription']
    
    ```
    
23. Describe the table again:
    
    ```
    describeResponse = ddb.describe_table(TableName = 'PetInventory')
    
    ```
    
    An error message indicates the table was successfully deleted.
    
24. List the tables:
    
    ```
    listResponse = ddb.list_tables()['TableNames']
    
    listResponse
    
    ```
    
    An empty set of square brackets indicates the table was deleted.
    

### Create a DynamoDB Table with CloudFormation

1. In the AWS console, navigate to CloudFormation.
2. Click **Create stack**.
3. In the *Prerequisite - Prepare template* section, select **Create template in Designer**.
4. Click **Create template in designer**.
5. Locate *DynamoDB* in the *Resource types* list, and drag **Table** over into the canvas.
6. In the *Properties* box below, make sure it's showing the *Components*. Delete the existing components code.
7. Click the *Template* tab at the bottom to view the default template.
8. Right-click the table in the canvas above, and delete it.
9. Back in the *Template* window, delete the existing template and paste in the following:
    
    ```
    {
        "AWSTemplateFormatVersion": "2010-09-09",
        "Resources": {
            "PetTable": {
                "Type": "AWS::DynamoDB::Table",
                "Properties": {
                    "AttributeDefinitions" : [
                        {
                            "AttributeName": "pet_species",
                            "AttributeType": "S"
                        },
                        {
                            "AttributeName": "pet_id",
                            "AttributeType": "N"
                        }
                    ],
                    "KeySchema" : [
                        {
                            "AttributeName": "pet_species",
                            "KeyType": "HASH"
                        },
                        {
                            "AttributeName": "pet_id",
                            "KeyType": "RANGE"
                        }
                    ],
                    "TableName": "PetInventory",
                    "BillingMode": "PAY_PER_REQUEST"
                }
            }
        }
    }
    
    ```
    
10. Select **YAML** to view the template in YAML instead of JSON.
11. Click the checkbox at the top to validate the template, and then click the cloud icon with the up arrow to create the stack.
12. Click **Next**.
13. Give it a *Stack name* of "PetInventoryStack".
14. Click **Next**.
15. Leave the defaults on the stack options page, and click **Next**.
16. Click **Create stack**.
17. Monitor the stack creation process in the *Events* window by clicking the refresh button.
18. In a new browser tab, navigate to DynamoDB.
19. Click **Tables** in the left-hand menu.
20. Click our newly created **PetInventory** table to see its details.
21. Back in CloudFormation, click to open **PetInventoryStack**.
22. Click **Delete**.
23. In the *Delete PetInventoryStack?* dialog box, click **Delete stack**.
24. Monitor the deletion process by clicking the *Events* tab.
25. Back in DynamoDB, verify the table is no longer listed.

## Conclusion

Congratulations on successfully completing this hands-on lab!