## Developer scraping the data and store it into S3

In this tutorial, we will create IAM roles for multiple AWS services, whcih is a web service that helps you securely control access to AWS resources. You use IAM to control who is authenticated (signed in) and authorized (has permissions) to use resources.

### Create following IAM roles

* 	On the **service** menu, click **IAM**.<br>
* 	In the navigation pane, choose **Roles**.<br>
* 	Click **Create role**.<br>
* 	For role type, choose **AWS Service**, find and choose **Glue**, and choose **Next: Permissions**.<br>
* 	On the **Attach permissions policy** page, search and choose **AmazonS3FullAccess, AWSGlueServiceRole**, and choose **Next: Review**.<br>
* 	On the **Review** page, enter the following detail: <br>
**Role name: AWSGlueServiceRoleDefault**<br>
* 	Click **Create role**.<br>
* 	Choose **Roles** page, select the role **AWSGlueServiceDefault** you just created.<br>
* 	On the **Permissions** tab, choose the link **add inline policy** to create an inline policy.<br>
* 	On the JSON tab, paste in the following policy:<br>

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
* 	Click **Review policy**.<br>
* 	On the Review policy, enter policy name: **AWSCloudWatchLogs**.<br>
* 	Click **Create policy**.<br>
* 	Now confirm you have policies as below figure.<br>
![iam1.png](/images/iam1.png)<br> 
Figure1: IAM role policies<br><br><br>
You successfully create the role that allow AWS Glue get access to S3.<br>
 
* 	Back to the navigation pane, choose **Roles**.<br>
* 	Click **Create role**.<br>
* 	For role type, choose **AWS Service**, find and choose **Lambda**, and choose **Next: Permissions**.<br>
* 	On the **Attach permissions policy** page, search and choose **AmazonS3FullAccess**, and choose **Next: Review**.<br>
* 	On the **Review** page, enter the following detail: <br>
**Role name: LambdaAutoETL**<br>
* 	Click **Create role**.<br>
* 	Choose **Roles** page, select the role **LambdaAutoETL** you just created.<br>
* 	On the Permissions tab, choose the link **add inline policy** to create an inline policy.<br>
* 	On the JSON tab, paste in the following policy:<br>

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
* 	Click **Review policy**.<br>
* 	On the Review policy, enter policy name: **ETL-Analysis**<br>
* 	Click **Create policy**.<br>
* 	Now confirm you have policies as below figure.<br>
![iam2.png](/images/iam2.png)<br> 
Figure2: IAM role policies<br><br><br>
You successfully create the role that allow Lambda trigger ETL job automatically with AWS Glue by object put event of S3<br>
 
* 	Back to the navigation pane, choose **Roles**.<br>
* 	Click **Create role**.<br>
* 	For role type, choose **AWS Service**, find and choose **Lambda**, and choose **Next: Permissions**.<br>
* 	On the **Attach permissions policy** page, search and choose **AmazonS3FullAccess, IAMFullAccess, ComprehendFullAccess** and choose **Next: Review**.<br>
* 	On the **Review** page, enter the following detail: <br>
**Role name: Comprehend-Job**<br>
* 	Click **Create role**.<br>
* 	Choose **Roles** page, select the role **Comprehend-Job** you just created.<br>
* 	On the **Permissions** tab, now confirm you have policies as below figure.<br>
![iam3.png](/images/iam3.png)<br> 
Figure3: IAM role policies<br><br><br>
You successfully create the role that allow Lambda trigger topic detection job automatically with Amazon Comprehend by object put event of S3<br>
 
* 	Back to the navigation pane, choose **Roles**.<br>
* 	Click **Create role**.<br>
* 	For role type, choose **AWS Service**, find and choose **Redshift**, and choose **Redshift – Customizable** below then click **Next: Permissions**.<br>
* 	On the **Attach permissions policy** page, search and choose **AmazonS3FullAccess, AmazonAthenaFullAccess, AWSGlueConsoleFullAccess** and choose **Next: Review**.<br>
* 	On the **Review** page, enter the following detail: <br>
**Role name: SpectrumRole**<br>
* 	Click **Create role**.<br>
* 	Choose **Roles** page, select the role **SpectrumRole** you just created. Now confirm you have below figure.<br>
![iam4.png](/images/iam4.png)<br> 
Figure4: IAM role policies<br><br><br>
You successfully create the role for Redshift clusters to call S3, Athena and Glue service<br>



### Create S3 bucket to store data

The first bucket stores the data that contain YouTube trending data<br><br>
The second bucket stores the data that after **doing csv ETL process**<br><br>The 
The third bucket stores the data that after **doing json ETL process**<br><br>
The fourth bucket stores the data that **doing topic detection job**<br><br>
The fifth bucket stores the data that **complete topic detection job**<br><br>
* 	On the service menu, click **S3**.<br><br>
*  	Click **Create bucket**.<br><br>
*  	Enter the Bucket name ****“yourname-dataset” (e.g., james-dataset)** and ensure that the bucket name is unique so that you can create.<br><br>
*  	Click **Create**.<br><br>
*  	Click **“yourname-dataset”** bucket<br><br>
*  	Click **Upload****.<br><br>
*  	Click **Add files**.<br><br>
*  	Select file **USvideos.csv** and **US-category-id.json** then click **Upload**.<br><br>
*  	For another bucket, click **Create bucket** again and enter the bucket name **“yourname-etl-result” (e.g., james-etl-result)** and ensure that the bucket name is unique so that you can create.<br><br>
*  	Click **Create**.<br><br>
*  	For another bucket, click **Create bucket** again and enter the bucket name **“yourname-etl-result2” (e.g., james-etl-result2)** and ensure that the bucket name is unique so that you can create.<br><br>
*  	Click **Create**.<br><br>
*  	For another bucket, click **Create bucket** again and enter the bucket name **“yourname-topic-analysis”** and ensure that the bucket name is unique so that you can create.<br><br>
*  	Click **Create**.<br><br>
*  	Click **“yourname-topic-analysis”** bucket.<br><br>
*  	Click **Upload**.<br><br><br>
*  	Click **Add files**.<br><br>
*  	Select file **word_analysis.csv** then click Upload.<br><br>
*  	For another bucket, click **Create bucket** again and enter the bucket name **“yourname-topic-analysis-result”** and ensure that the bucket name is unique so that you can create.<br><br>
*  	Click **Create**.<br><br>
*  	Make sure that your S3 buckets contain those five buckets<br><br>
![s3_buckets.png](/images/s3_buckets.png)<br>  
