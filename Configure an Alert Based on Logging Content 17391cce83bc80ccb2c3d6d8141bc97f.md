# Configure an Alert Based on Logging Content

## Introduction

In this lab, we'll walk through the 
process to filter over existing logging data inside of CloudWatch Logs. 
We'll start by creating a SNS topic without a subscription, then filter 
our CloudWatch Logs data to create a metric filter. After creating a 
metric filter and defining our metric, we will configure an alarm to 
send notifications to our created SNS topic.

## Solution

Log in to the AWS Management Console using the credentials provided for the lab. Make sure you're using the `us-east-1` (N. Virginia) Region.

### Create an SNS Topic Named `ErrorTopic` without a Subscription

1. Navigate to **Simple Notification Service** (SNS) using the **Services** menu or the unified search bar.
2. Expand the left-hand menu and select **Topics**.
3. On the right, click **Create topic**.
4. Fill in the **Details** section:
    - **Type**: Select **Standard**.
    - **Name**: In the text box, enter *ErrorTopic*.
    - **Display name**: In the text box, enter *StatusCode 500 Errors*.
5. Leave all the other default settings and click **Create topic**.

### Create a `StatusCode500` Metric Filter on the `TrackMyErrors` CloudWatch Logs Log Group

1. Navigate to **CloudWatch** using the **Services** menu or the unified search bar.
2. In the sidebar menu, expand **Logs** and select **Log groups**.
3. Select the checkbox next to the **TrackMyErrors** log group.
4. On the right, use the **Actions** dropdown to select **Create metric filter**.
5. In the **Filter pattern** field, select `[host, logName, user, timestamp, request, statusCode=4*,size]`.
6. Test the filter:
    - Use the **Select log data to test** dropdown to select your most recent log data.
    - Click **Test pattern**.
    
    You should see no results populate. This is because you're not filtering by `StatusCode 500` yet.
    
7. In the **Filter pattern** field, replace `4` with `5` to filter by `StatusCode 500`.
    
    The filter should now look like this:`[host, logName, user, timestamp, request, statusCode=5*,size]`.
    
8. Click **Test pattern** again to ensure that the test now returns results.
9. Click **Next**.
10. In the **Filter name** field, enter *StatusCode500*.
11. Fill in the **Metric details** section:
    - **Metric namespace**: In the text box, enter *StatusCode500*.
    - **Metric name**: In the text box, enter *StatusCode500*.
    - **Metric value**: In the text box, enter *1*.
12. Leave all the other fields blank and click **Next**.
13. On the **Review and create** page, click **Create metric filter**.
    
    The metric filter may take a moment to finish creating.
    

### Create an Alarm Based on the Metric You Created

### Configure Metric Filters for Your Data

1. After the metric is created, ensure the **Metric filters** tab is selected.
2. In the **Metric** field, open the second instance of **StatusCode500** in a new tab.
    
    This opens CloudWatch's **All metrics** view.
    
3. In the **Metrics** pane, select **StatusCode500 > Metrics with no dimensions**.
4. Select the checkbox next to **StatusCode500**.
5. Select the **Graphed metrics** tab.
6. Use the **Statistic** dropdown to select **Sum**.
7. Use the **Period** dropdown to select **1 minute**.
    
    The metrics may take a few moments to populate.
    
8. When the metrics start populating in the graph, use the timeline filter along the top of the page to select **1h**.
    
    > Note: You may need to click the Refresh icon on the right to update the graph.
    > 

### Configure an Alarm for Your Data

1. In the **Graphed metrics** details, select the bell icon under **Actions** to create an alarm.
2. In the **Conditions** section, set the **than...** value to *4*.
    
    This will trigger an alarm if there are more than four events in a 1-minute period.
    
3. Leave all the other default settings and click **Next**.
4. In the **Send a notification to...** field, select your **ErrorTopic** topic.
5. Leave all the other default settings and click **Next**.
6. Fill in the **Name and description** section:
    - **Alarm name**: In the text box, enter *StatusCode500*.
    - **Alarm description**: In the text box, enter *An alarm for StatusCode500 metric*.
7. Click **Next**.
8. Review your alarm details and click **Create alarm**.
    
    Based on the metric events, your alarm state should automatically be **In alarm**.