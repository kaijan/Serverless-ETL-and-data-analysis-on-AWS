# Developer scraping the data and store it into S3

In this tutorial, we will create IAM roles for multiple AWS services, whcih is a web service that helps you securely control access to AWS resources. You use IAM to control who is authenticated (signed in) and authorized (has permissions) to use resources.

## Prerequisites

* Make sure the region is **US East (N. Virginia)**, which its short name is **us-east-1**.

* Download this **repository** and unzip, ensure that **data** folder including three files:

    * USvideos.csv
    * US-category-id.json
    * word_analysis.csv


## Create IAM roles

### IAM role for AWS Glue

* On the **service** menu, click **IAM**.

* In the navigation pane, choose **Roles**.

* Click **Create role**.

* For role type, choose **AWS Service**, find and choose **Glue**, and choose **Next: Permissions**.

* On the **Attach permissions policy** page, search and choose **AmazonS3FullAccess, AWSGlueConsoleFullAccess**, **AmazonEC2FullAccess**, click **Next: Tags**, and **Next: Review**.

* On the **Review** page, enter the following detail:
**Role name: AWSGlueServiceRoleDefault**

* Click **Create role**.

* Choose **Roles** page, select the role **AWSGlueServiceRoleDefault** you just created.

* On the **Permissions** tab, choose the link **add inline policy** to create an inline policy.

* On the JSON tab, paste in the following policy:

      {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Effect": "Allow",
           "Action": [
             "logs:CreateLogGroup",
             "logs:CreateLogStream",
             "logs:PutLogEvents",
             "logs:DescribeLogStreams"
         ],
           "Resource": [
             "arn:aws:logs:*:*:*"
         ]
       }
      ]
     }

* Click **Review policy**.

* On the Review policy, enter policy name: **AWSCloudWatchLogs**.

* Click **Create policy**.

* Click the link **add inline policy** to create an inline policy.

* On the JSON tab, paste in the following policy:

      {
          "Version": "2012-10-17",
          "Statement": [
              {
                  "Sid": "VisualEditor0",
                  "Effect": "Allow",
                  "Action": [
                      "ec2:DeleteTags",
                      "ec2:CreateTags"
                  ],
                  "Resource": "*"
              }
          ]
      }

* Click **Review policy**.

* On the Review policy, enter policy name: **CreateTags**.

* Click **Create policy**.

* Now confirm you have policies as below figure.

![iam1.png](/images/iam1.png)

You successfully create the role that allow AWS Glue get access to S3.<br><br>

### IAM role for automated ETL job using AWS Lambda

* Back to the navigation pane, choose **Roles**.

* Click **Create role**.

* For role type, choose **AWS Service**, find and choose **Lambda**, and choose **Next: Permissions**.

* On the **Attach permissions policy** page, search and choose **AmazonS3FullAccess**, click **Next: Tags**, and **Next: Review**.

* On the **Review** page, enter the following detail:
**Role name: LambdaAutoETL**

* Click **Create role**.

* Choose **Roles** page, select the role **LambdaAutoETL** you just created.

* On the Permissions tab, choose the link **add inline policy** to create an inline policy.

* On the JSON tab, paste in the following policy:

      {
         "Version": "2012-10-17",
         "Statement": [
             {
                 "Sid": "VisualEditor0",
                 "Effect": "Allow",
                 "Action": [
                     "athena:*",
                     "glue:*"
                 ],
                 "Resource": "*"
             }
         ]
       }

* Click **Review policy**.

* On the Review policy, enter policy name: **ETL-Analysis**

* Click **Create policy**.

* Now confirm you have policies as below figure.
![iam2.png](/images/iam2.png)

You successfully create the role that allow Lambda trigger ETL job automatically with AWS Glue by object put event of S3.<br><br>

### IAM role for Amazon Comprehend job

* Back to the navigation pane, choose **Roles**.

* Click **Create role**.

* For role type, choose **AWS Service**, find and choose **Lambda**, and choose **Next: Permissions**.

* On the **Attach permissions policy** page, search and choose **AmazonS3FullAccess, IAMFullAccess, ComprehendFullAccess**, click **Next: Tags**, and **Next: Review**.

* On the **Review** page, enter the following detail:
**Role name: Comprehend-Job**

* Click **Create role**.

* Choose **Roles** page, select the role **Comprehend-Job** you just created.

* On the **Permissions** tab, now confirm you have policies as below figure.

![iam3.png](/images/iam3.png)

You successfully create the role that allow Lambda trigger topic detection job automatically with Amazon Comprehend by object put event of S3.<br><br>

### IAM role for Redshift

* Back to the navigation pane, choose **Roles**.

* Click **Create role**.

* For role type, choose **AWS Service**, find and choose **Redshift**, and choose **Redshift – Customizable** below then click **Next: Permissions**.

* On the **Attach permissions policy** page, search and choose **AmazonS3FullAccess, AmazonAthenaFullAccess, AWSGlueConsoleFullAccess**, click **Next: Tags**, and **Next: Review**.

* On the **Review** page, enter the following detail:
**Role name: SpectrumRole**

* Click **Create role**.

* Choose **Roles** page, select the role **SpectrumRole** you just created. Now confirm you have below figure.
![iam4.png](/images/iam4.png)

You successfully create the role for Redshift clusters to call S3, Athena and Glue service.<br><br>


## Create S3 bucket to store data

The first bucket stores the data that contain YouTube trending data<br>
The second bucket stores the data that after **doing csv ETL process**<br>The 
The third bucket stores the data that after **doing json ETL process**<br>
The fourth bucket stores the data that **doing topic detection job**<br>
The fifth bucket stores the data that **complete topic detection job**<br>

* On the **service** menu, click **S3**.

* Click **Create bucket**.

* Enter the Bucket name **“yourname-dataset” (e.g., james-dataset)** and ensure that the bucket name is unique so that you can create.

* Click **Create**.

* Click **“yourname-dataset”** bucket.

* Click **Upload**.

* Click **Add files**.

* Select file **USvideos.csv** and **US-category-id.json** then click **Upload**.

* For another bucket, click **Create bucket** again and enter the bucket name **“yourname-etl-result” (e.g., james-etl-result)** and ensure that the bucket name is unique so that you can create.

* Click **Create**.

* For another bucket, click **Create bucket** again and enter the bucket name **“yourname-etl-result2” (e.g., james-etl-result2)** and ensure that the bucket name is unique so that you can create.

* Click **Create**.

* For another bucket, click **Create bucket** again and enter the bucket name **“yourname-topic-analysis”** and ensure that the bucket name is unique so that you can create.

* Click **Create**.

* Click **“yourname-topic-analysis”** bucket.

* Click **Upload**.

* Click **Add files**.

* Select file **word_analysis.csv** then click Upload.

* For another bucket, click **Create bucket** again and enter the bucket name **“yourname-topic-analysis-result”** and ensure that the bucket name is unique so that you can create.

* Click **Create**.

* Make sure that your S3 buckets contain those five buckets:

![s3_buckets.png](/images/s3_buckets.png)<br>  

### Now you are ready to [trigger a Lambda function to do ETL with Glue](https://github.com/ecloudvalley/Serverless-ETL-and-data-analysis-on-AWS/tree/master/202-Trigger%20Lambda%20function%20to%20do%20ETL%20with%20Glue)
