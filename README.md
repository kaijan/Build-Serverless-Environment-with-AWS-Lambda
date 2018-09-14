# Build Serverless Environment with AWS Lambda



## Scenario
First, you will host a static website in S3. You will use DyanmoDB as a database which will record the data. All we need to do is retrieve the data from DynamoDB and show the record in S3 web page.

![1.png](/images/1.png)

## Prerequisites
>The workshop’s region will be in ‘N.Virginia’

> Download index.html which is in this repository


## Lab tutorial
### Create S3 bucket
1.1.  On the **service** menu, click **S3**.

1.2. Click **Create Bucket**.

1.3. For Bucket Name, type **Unique Name**.

1.4. For Region, choose **US East(N.Virginia)**.

1.5. Click **Create** to create S3 bucket without any setting.

### Upload files to S3 Bucket

2.1. Select the bucket which you created before.

2.2. Click **Upload**.

2.3. Click **Add files**.

2.4. Select the html file which in the download folder, then choose.

2.5. Click **Upload** without any setting.

2.6. Check the status of the object which exists in S3 bucket.

![2.png](/images/2.png)

### Enable Static website hosting through S3

3.1. Select **Properties** tab.

3.2. Select **Static website hosting**.

3.3. Select **Use this bucket to host a website**.

3.4. Type **index.html** for the index document, then click **Save**.

![3.png](/images/3.png)

3.5. Verify the status of **Bucket hosting**.

3.6. Choose **Bucket Policy** tab in **Permissions**.

3.7. Copy below bucket policy, and paste it into the field. Then click **Save**.

    {
          "Version":"2012-10-17",
          "Statement":[{
            "Sid":"PublicReadGetObject",
            "Effect":"Allow",
            "Principal": "*",
              "Action":["s3:GetObject"],
              "Resource":["arn:aws:s3:::<Your-bucket-name>/*"]
              }
          ]
    }

![4.png](/images/4.png)


### View S3 static website hosting

4.1. Click **Properties**, then click **Static website hosting**.

4.2. Click the **Endpoint**.

4.3. You will see the website as below:

![5.png](/images/5.png)

### Create DynamoDB Table

5.1. On the **Services** menu, click **DynamoDB**.

5.2. Click **Create table** in the welcome page.

5.3.In the **Create DynamoDB table** section, do the following as below:

* **Table name** : <YOUR_TABLE_NAME>
* Primary key : **id** 
* Data type : **Number**

5.4. Click **Create**.

![6.png](/images/6.png)

5.5. Waiting for the table created successfully.

### Create Items in DynamoDB Table

6.1. In the DynamoDB console, choose the **Items** tab.

6.2. Select **Create item**.

6.3. In the prompt console, type data type & data value like below.

![13.png](/images/13.png)

6.4. Click **Save**.

6.5. You can review the item which just type-in via console

![7.png](/images/7.png)

### Create Lambda Function

7.1. In the **service menu**, select **Lambda**

7.2. Select **Create a function** on the right panel.

7.3. Select **Author from scratch** for a blank function

7.4. Type following as below:

* Name : **serverless-function**
* Run time : **python 3.6**
* Role : **Create a custom role**
* Role name : **Lambda_Access_Dynamo**

7.5. Click **View Policy Document**, click **Edit**, then click **Ok**.

7.6. Copy below policy and paste it into the section.

    {
          "Version": "2012-10-17",
          "Statement": [
            {
                  "Effect": "Allow",
                  "Action": [
                    "logs:CreateLogGroup",
                    "logs:CreateLogStream",
                    "logs:PutLogEvents"
                  ],
                  "Resource": "arn:aws:logs:*:*:*"
            },
            {
                   "Effect": "Allow",
                   "Action": "dynamodb:*",
                   "Resource": "*"
            }
        ]
    }

7.7. Click **Create function**.

7.8. Copy below code and paste it into **Lambda function code** section.

    import boto3

    def lambda_handler(event, context):
    
        print ("====================Lambda Start====================")
    
        client_dynamodb = boto3.client('dynamodb')
    
        response_dynamodb = client_dynamodb.scan(
            TableName = '<Your_table_name>'
            )['Items']
    
        print (response_dynamodb)
        
        response_dynamodb_sorted = sorted(response_dynamodb, key= lambda response: response['id']['N'], reverse=False)
    
        print (response_dynamodb_sorted)
    
        return (response_dynamodb_sorted)
    
7.9. Modify the **TableName** which you create previously in DynamoDB in the code.

7.10. Click **Save**.

### Create APIs in API Gateway

8.1. On the **service menu**, click **API Gateway**.

8.2. Click **Create API**.

8.3. Select **New API**.

8.4. Type-in API name in **Serverless-bootcamp**, then click **Create API**.

![8.png](/images/8.png)

8.5. In the **Resources** tab, choose the root **/**, click **Actions** and select **Create Resource**.

8.6. Type **get-items** in the **Resource Name**.

8.7. Make sure **Enable API Gateway CORS** is enabled, then click **Create Resource**

![9.png](/images/9.png)

8.8. After resource being created, click **Actions** and select **Create Method** to add method

8.9. In the drop-down list, select **GET** method and click **yes**.

8.10. In the setup page as below:
* Select **Lambda Function** for **Integration type**
* Choose **us-east-1** in the **Lambda Region**
* Type-in the Lambda function created in previous chapter in the **Lambda Function**
* Click **Save**

8.11. Click **OK** to give API Gateway permission to invoke Lambda function

8.12. Click **Actions** within Resource layer, and select **Enable CORS**

8.13. Select **Enable CORS and replace existing CORS headers**

8.14. Review and click **Yes, replace existing values**

8.15. Waiting for the steps all be checked

![10.png](/images/10.png)

### Deploy APIs

9.1. Click **Actions** and select **Deploy API**

9.2. In the prompt console,
* Select **[New Stage]** in Deployment stage 
* Give the name for **Stage name**
* Click **Deploy**

9.3. The console would jump out as below, select the **SDK Generation** tab.

9.4. In the **SDK Generation** tab, select **JavaScript** in the **Platform** field and click **Generate SDK**.

9.5. The browser would ask you to confirm download a zip file named as **javascript_TIMESTAMP.zip**

9.6. Save and unzip the zip file, after entering the folder, there would be files show as below:

![11.png](/images/11.png)

9.7. Upload those files to the S3 Bucket created in Chapter S3, must be the same layer as the html.

> Modify the Javascript in the html file to your APIs & data in DynamoDB

9.8. Reload the web page and click the button to test your API.

![12.png](/images/12.png)
## Conclusion

Congratulations! You now have learned how to:
* Create a static website hosting through S3
* Create a Lambda function
* Create DynamoDB Table
* Create API through API Gateway



