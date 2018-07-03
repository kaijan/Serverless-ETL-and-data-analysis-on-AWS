# Serverless-ETL-and-data-analysis-on-AWS
![kaggle_data.png](/images/kaggle_data.png)<br>
**This lab using Trending YouTube Video Statistics dataset on Kaggle**

## Scenario

Various of enterprises nowadays facing with big data problems. Most of them concern about data mining, data cleaning, data analytics, and even about machine learning workflow. With workflow being more complicated, it brings out lots of trouble and management issues. For example, an ETL job is a big challenge for many companies. In addition to data processing, server provision and server management are still take time and effort. The big data workflow is also relate to huge cost because it is too hard to use the server effectively.

AWS plays an important role of big data solution. We will focus on AWS Glue to perform serverless ETL job. You can use AWS Glue to build a data warehouse to organize, cleanse, validate, and format data. The architecture also integrate with several services to fulfill data analysis and BI process that contains **S3**, **Lambda**, **Comprehend**, **Glue**, **Athena**, **Redshift Spectrum**, **QuickSight**.


## Use Case in this Lab 
* Dataset: Trending YouTube Video Statistics <br>
https://www.kaggle.com/datasnaek/youtube-new<br><br>
With the large growth of YouTube, it plays an important role of video service. The large company also use it to determine their ads strategies and marketing plans.
This use case using trending YouTube video data to analyze which video channel or video type are suitable for advertising. In addition, YouTubers can make themselves more popular by analyzing the trending videos of YouTube. We use AWS Glue to do serverless ETL job and analyze those big data automatically with BI tool that integrate with AWS Athena or AWS Redshift Spectrum.



![preview_data1.png](/images/preview_data1.png)<br>
![preview_data2.png](/images/preview_data2.png)<br>

## Lab Architecture
![lab_architecture.png](/images/lab_architecture.png)

As illustrated in the preceding diagram, this is a big data processing in this model:<br><br>
* 1.&nbsp;&nbsp; 	Developer scraping the data and store it into S3.<br><br>
* 2.&nbsp;&nbsp; 	Once S3 get the data then trigger a Lambda function to do ETL with Glue.<br><br>
* 3.&nbsp;&nbsp; 	On the other hand, AWS Comprehend doing topic modeling job as sentiment analysis with certain data.<br><br>
* 4.&nbsp;&nbsp; 	Athena or Redshift Spectrum will perform query job known as data analysis when ETL job finished. <br><br>
* 5.&nbsp;&nbsp; 	BI tool like QuickSight or Tableau do real time data visualization.<br><br>


## AWS Glue introduction
What is AWS Glue?
AWS Glue is a fully managed data catalog and ETL (extract, transform, and load) service that simplifies and automates the difficult and time-consuming tasks of data discovery, conversion, and job scheduling. AWS Glue crawls your data sources and constructs a data catalog using pre-built classifiers for popular data formats and data types, including CSV, Apache Parquet, JSON, and more. It is significantly reducing the time and effort that it takes to derive business insights quickly from an Amazon S3 data lake by discovering the structure and form of your data. Also automatically crawls your Amazon S3 data, identifies data formats, and then suggests schemas for use with other AWS analytic services. 

### The workshop’s region will be in ‘Virginia’


## Prerequisites
1.	Sign-in a AWS account, and make sure you have select **N.Virginia** region<br>
2.	Make sure your account have permission to create IAM role for following services: **S3, Lambda, Glue, Athena, Redshift Spectrum, QuickSight**<br>
3.	Download **this repository** and unzip, ensure that **data** folder including three files:<br>
**USvideos.csv**, **US-category-id.json**, **word_analysis.csv**<br>



## Lab tutorial


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



### Setup data catalog in AWS Glue

Create database, tables, crawlers, jobs in Glue<br><br>
* 	On the **Services** menu, click **AWS Glue**.<br><br>
* 	In the console, choose **Add database**. In the **Database name**, type **my-data**, and choose **Create**.<br><br>
* 	Choose **Crawlers** in the navigation pane, choose **Add crawler**. Add type Crawler name **data-crawler**, and choose **Next**.<br><br>
* 	On the **Add a data store** page, choose **S3** as data store.<br><br>
* 	Select **Specified path in my account**.<br><br>
* 	Select the bucket that you create first (**“yourname-dataset”****), and choose **Next**.<br><br>
![glue1.png](/images/glue1.png)<br>  
* 	On **Add another data store** page, choose **No**, and choose **Next**.<br><br>
* 	Select **Choose an existing IAM role**, and choose the role **AWSGlueServiceRoleDefault** you just created in the drop-down list, and choose **Next**.<br><br>
* 	For **Frequency**, choose **Run on demand**, and choose **Next**.<br><br>
* 	For **Database**, choose **my-data**, and choose **Next**.<br><br>
* 	Review the steps, and choose **Finish**.<br><br>
* 	The crawler is ready to run. Choose **Run it now**.<br><br>
* 	When the crawler has finished, two table has been added. Choose **Tables** in the left navigation pane, and then choose **usvideos_csv** to confirmed.<br><br>
![glue2.png](/images/glue2.png)<br>  
![glue3.png](/images/glue3.png)<br>  
Now you finish the crawler setting, and going to create a job to transform data type<br><br>
 
* 	In the navigation pane, under **ETL**, choose **Jobs**, and then choose **Add job**.<br><br>
* 	On the Job properties, enter the following details:<br>
**Name: data-csv-parquet**<br>
**IAM role:** choose **AWSGlueServiceRoleDefault**<br><br>
* 	For **This job runs**, select **A proposed script generated by AWS Glue**.<br><br>
* 	Choose **Next**.<br><br>
* 	Choose **usvideos_csv** as the data source, and choose **Next**.<br><br>
* 	Choose **Create tables in your data target**.<br><br>
*	For Data store, choose Amazon S3, and choose **Parquet** as the format.<br><br>
*	For **Target path**, select S3 bucket with **“yourname-etl-result”** that you created before to store the results.<br><br>
![glue4.png](/images/glue4.png)<br>  
* 	Verify the schema mapping, and choose **Next** and click **Finish**.<br><br>
![glue5.png](/images/glue5.png)<br>  
* 	View the job. This screen provides a complete view of the job and allows you to edit, click **Save**, and choose **Run job**. This steps may be waiting around 10 minutes.<br><br>
![glue6.png](/images/glue6.png)<br> 
Job running screen<br><br>
* 	When job finished, go to your S3 bucket **“yourname-etl-result”** ensure there is a parquet file that means your job succeed.<br><br>
![glue7.png](/images/glue7.png)<br>  
 
Now you need to add another parquet table and crawler<br><br>
* 	When the job has finished, add a new table for the Parquet data using a crawler.<br><br>
* 	In the navigation pane, choose **Add crawler**. Add type Crawler name **“parquet-crawler1”** and choose **Next**.<br><br>
* 	Choose **S3** as the **Data store**.<br><br>
* 	Include path choose your S3 bucket **“yourname-etl-result”** to store data.<br><br><br><br>
* 	Choose **Next**.<br><br>
*	On **Add another data store** page, choose **No**, and choose **Next**.<br><br>
* 	Select **Choose an existing IAM role**, and choose the role <br><br>
**AWSGlueServiceRoleDefault** you just created in the drop-down list, and choose Next.<br><br>
* 	For **Frequency**, choose **Run on demand**, and choose **Next**.<br><br>
* 	For **Database****, choose **my-data**, and choose **Next**.<br><br>
* 	Review the steps, and choose **Finish**.<br><br>
* 	The crawler is ready to run. Choose **Run it now**.<br><br>
* 	After the crawler has finished, there is a new table in the **my-data** database:<br><br>
![glue8.png](/images/glue8.png)<br>  
That means you finish the ETL process of csv to parquet format **(USvideos.csv)**<br><br>

In order to analyze the channel category, we need to perform another job in Glue data catalog which contains category data (**US-category-id.json**)<br><br>
* 	In the navigation pane, under **ETL**, choose **Jobs**, and then choose **Add job**.<br><br>
* 	On the Job properties, enter the following details:<br>
**Name: data-json-parquet**<br>
**IAM role:** choose **AWSGlueServiceRoleDefault**<br><br>
* 	For **This job runs**, select **A proposed script generated by AWS Glue**.<br><br>
* 	Choose **Next**.<br><br>
*	Choose **us_category_id_json** as the data source, and choose **Next**.<br><br>
* 	Choose **Create tables in your data target**.<br><br>
*	For Data store, choose Amazon S3, and choose **Parquet** as the format.<br><br>
* 	For **Target path**, select S3 bucket with **“yourname-etl-result2”** that you created before to store the results.<br><br>
![glue9.png](/images/glue9.png)<br>  
* 	Verify the schema mapping, and choose **Next** and click **Finish**.<br><br>
![glue10.png](/images/glue10.png)<br>  
* 	View the job. This screen provides a complete view of the job and allows you to edit, click **Save**, and choose **Run job**. This steps may be waiting around 10 minutes.<br><br>
![glue11.png](/images/glue11.png)<br> 
Job running screen<br><br>
* 	When job finished, go to your S3 bucket **“yourname-etl-result2”** ensure there is a parquet file that means your job succeed.<br><br>
![glue12.png](/images/glue12.png)<br>  
 
Now you need to add another parquet table and crawler<br><br>
* 	When the job has finished, add a new table for the Parquet data using a crawler.<br><br>
* 	In the navigation pane, choose **Add crawler**. Add type Crawler name **“parquet-data2”** and choose **Next**.<br><br>
* 	Choose **S3** as the **Data store**.<br><br>
* 	Include path choose your S3 bucket **“yourname-etl-result2”** to store data.<br><br>
* 	Choose **Next**.<br><br>
* 	On **Add another data store** page, choose **No**, and choose **Next**.<br><br>
* 	Select **Choose an existing IAM role**, and choose the role<br> 
**AWSGlueServiceRoleDefault** you just created in the drop-down list, and choose **Next**.<br><br>
* 	For **Frequency**, choose **Run on demand**, and choose **Next**.<br><br>
* 	For **Database**, choose **my-data**, and choose **Next**.<br><br>
*	Review the steps, and choose **Finish**.<br><br>
* 	The crawler is ready to run. Choose **Run it now**.<br><br>
* 	After the crawler has finished, there is a new table in the **my-data** database:<br><br>
![glue13.png](/images/glue13.png)<br>  
Congratulations! You now have learned how to:<br><br>
•	Processing ETL job manually using AWS Glue and Amazon S3.<br><br>
•	Crawler your data to Amazon S3 by AWS Glue.<br><br>
Next step you will learn how to trigger ETL job with Lambda function automatically<br>



### Create a Lambda function in order to automate ETL job with glue (automated ETL job)

* 	On the **Services** menu, click **Lambda**.<br><br>
* 	Click **Create function**.<br><br>
* 	Choose **Author from scratch**.<br><br>
* 	Enter function Name **ETL-auto**.<br><br>
* 	Select **python 3.6** in **Runtime** blank.<br><br>
* 	Select **Choose an existing role** in Role blank and choose **LambdaAutoETL** as **Existing role**.<br><br>
![lambda_glue1.png](/images/lambda_glue1.png)<br>   
* 	Click **Create function**.<br><br>
* 	In **configuration**, click **S3** below **Add triggers** to add trigger for **ETL-auto** function<br><br>
and drop down to **Configure triggers** part, select bucket **“yourname-dataset”** as Bucket, select **PUT** as **Event type**. Remember to check **Enable trigger** box then you click **Add**.<br><br>
![lambda_glue2.png](/images/lambda_glue2.png)<br>
![lambda_glue3.png](/images/lambda_glue3.png)<br>  
* 	Click **ETL-auto** blank in **Designer** and replace original code that existing in **Function code** editor with below code<br><br>
 
         import boto3
         import json

         glue = boto3.client('glue')

         def lambda_handler(event, context):
             # TODO implement
             response = glue.start_job_run(
                 JobName = 'data-csv-parquet',
                 Arguments = {})
             return response
 
![lambda_glue4.png](/images/lambda_glue4.png)<br>   
* 	Click **Save** to save the change of function.<br><br>
* 	Now you can upload a csv file into **yourname-dataset** bucket to test that whether this Lambda function operating normally.<br><br>
* 	After you upload a csv file into **yourname-dataset** bucket, AWS Glue will run the job **“data-csv-parquet”**. The **History** console will show the job is running if your Lambda function is correct.<br><br>
![lambda_glue5.png](/images/lambda_glue5.png)<br>   
Congratulations! You now have learned how to setup an automated ETL job with Lambda function.<br>




### Setup your first topic detection jobs by Amazon Comprehend

* 	On the **Services** menu, click **Amazon Comprehend**.<br><br>
* 	In the navigation pane, click **Topic modeling**.<br><br>
* 	Click **Create**.<br><br>
* 	Select **My data (S3)** as input data.<br><br>
* 	For **S3 data location** of input data, enter the URL of **“yourname-topic-analysis”** bucket **(e.g., s3://james-topic-analysis/)**.<br><br>
* 	For Input format, select **One document per line**.<br><br>
* 	Enter **50** for **Numbers of topic**.<br><br>
* 	For **Job Name**, enter **FirstJob**.<br><br>
* 	For **S3 data location** of output data, enter the URL of **“yourname-topic-analysis-result” (e.g., s3://james-topic-analysis-result/)**.<br><br>
* 	Select **Create a new IAM role** in **Select an IAM role** blank.<br><br>
* 	For **Permission to access** select **any S3 bucket**.<br><br>
* 	For **Name suffix** enter **“user”**.<br><br>
![setup_comprehend1.png](/images/setup_comprehend1.png)<br>    
* 	Click **Create job**.<br><br>
* 	It will take a few time running<br><br>
![setup_comprehend2.png](/images/setup_comprehend2.png)<br>  
* 	Then you will see your job is running.<br><br>
![setup_comprehend3.png](/images/setup_comprehend3.png)<br>  
* 	When the job complete the status change to Complete.<br><br>
* 	After job completed, you will find a new output in  **yourname-topic-analysis-result** bucket<br><br>
* 	Click below folder<br><br>
![setup_comprehend4.png](/images/setup_comprehend4.png)<br>  
You will find that a **output** folder inside <br><br>
![setup_comprehend5.png](/images/setup_comprehend5.png)<br>  
Click **output** you will see a file **output.tar.gz**<br><br>
![setup_comprehend6.png](/images/setup_comprehend6.png)<br>  
Download the file you will get the topic modeling result as csv file<br><br>
![setup_comprehend7.png](/images/setup_comprehend7.png)<br>  
![setup_comprehend8.png](/images/setup_comprehend8.png)<br> 
 



### Create a Lambda to trigger topic detection jobs by Comprehend (automated topic modeling job)

* 	On the **Services** menu, click **Lambda**.<br><br>
* 	Click **Create function**.<br><br>
* 	Choose **Author from scratch**.<br><br>
* 	Enter function Name **comprehend-lambda**.<br><br>
* 	Select **python 3.6** in **Runtime** blank.<br><br>
* 	Select **Choose an existing role** in **Role** blank and choose **Comprehend-Job** as **Existing role**.<br><br>
* 	Click **Create function**.<br><br>
* 	In **configuration**, click **S3** below **Add triggers** to add trigger for **comprehend-lambda** function<br><br>
* 	and drop down to **Configure triggers** part, select bucket **“yourname-topic-analysis”** as Bucket, select **PUT** as **Event type**. Remember to check Enable trigger box then you click **Add**.<br><br>
![lambda_comprehend1.png](/images/lambda_comprehend1.png)<br>  
* 	Click **comprehend-lambda** blank in **Designer** and replace original code that existing in **Function code** editor with below code<br><br>

*   Input_s3_url = “s3://yourname-topic-analysis”
*   output_s3_url = "s3://yourname-topic-analysis-result"
*   data_access_role_arn = "arn:aws:iam::xxxxxxxxxxxx:role/service-role/AmazonComprehendServiceRoleS3FullAccess-user"
    (The arn of IAM role that you create in Amazon Comprehend console)<br>
 
         import boto3
         import json

         def lambda_handler(event, context):
             # TODO implement
             comprehend = boto3.client(service_name='comprehend', region_name='us-east-1')
             input_s3_url = "s3://yourname-topic-analysis"
             input_doc_format = "ONE_DOC_PER_LINE"
             output_s3_url = "s3://yourname-topic-analysis-result"
             data_access_role_arn = "arn:aws:iam::xxxxxxxxxxxx:role/service-role/AmazonComprehendServiceRoleS3FullAccess-user"
             number_of_topics = 50

             input_data_config = {"S3Uri": input_s3_url, "InputFormat": input_doc_format}
             output_data_config = {"S3Uri": output_s3_url}

             start_topics_detection_job_result = comprehend.start_topics_detection_job(NumberOfTopics=number_of_topics,
                                                                                       InputDataConfig=input_data_config,
                                                                                       OutputDataConfig=output_data_config,
                                                                                       DataAccessRoleArn=data_access_role_arn)
             return start_topics_detection_job_result
 
* 	Click **Save** to save the change of function.<br><br>
* 	Now you can upload a csv file into **“yourname-topic-analysis”** bucket to test that whether this Lambda function operating normally.<br><br>
* 	On the **Services** menu, click **S3**.<br><br>
* 	Click **yourname-topic-analysis** bucket.<br><br>
* 	Click **Upload**.<br><br>
* 	Click **Add files**.<br><br>
*	Select file **word_analysis.csv** and click **Upload**.<br><br>
* 	When it upload finish go to your **Amazon comprehend** console<br><br>
* 	Click **Topic modeling**<br><br>
* 	**You will find a new job is running**<br><br>
![lambda_comprehend2.png](/images/lambda_comprehend2.png)<br> 
* 	Download the output of job in **yourname-topic-analysis-result** bucket after job completed which is a result from topic modeling<br><br>
![lambda_comprehend3.png](/images/lambda_comprehend3.png)<br> 
![lambda_comprehend4.png](/images/lambda_comprehend4.png)<br>     
![lambda_comprehend5.png](/images/lambda_comprehend5.png)<br>
![lambda_comprehend6.png](/images/lambda_comprehend6.png)<br>
![lambda_comprehend7.png](/images/lambda_comprehend7.png)<br>
![lambda_comprehend8.png](/images/lambda_comprehend8.png)<br>
Congratulations! You now have learned how to setup an automated topic modeling job with Lambda function.


### Analyze the data with Athena & Redshift Spectrum

Athena can query the data in an easy way with data catalog of Glue
1.1. 	On the Services menu, click Athena.
1.2. 	On the Query Editor tab, choose the database my-data.
 
1.3. 	Choose the yourname_etl_result table.
1.4. 	Query the data, type below standard SQL:
Select * From "my-data"."yourname_etl_result" limit 100;
(e.g., Select * From "my-data"."james_etl_result" limit 100;)
1.5. 	Click Run Query and Athena will query data as the below screen
 
1.6. 	Choose the yourname_etl_result2 table.
1.7. 	Query the data, type below standard SQL:
Select * From "my-data"."yourname_etl_result2";
(e.g., Select * From "my-data"."james_etl_result2";)
Click Run Query and Athena will query data as the below screen
 
 
After finish analyzing data in Athena, get start with data analysis with Redshift Spectrum. Different from Athena, Redshift is suitable for long term workflow of data analysis that often processing structural data query job.
•	First setup VPC in which you want to create your cluster
•	Launch Redshift cluster and connect to AWS Glue
1.8. 	On the Services menu, click VPC.
1.9. 	Click Start VPC Wizard.
1.10. 	In this workshop we simply choose VPC with a single Public Subnet
1.11. 	Enter your VPC name “Redshift-VPC”.
1.12. 	Click Add Endpoint below Service endpoints and select com.amazonaws.us-east-1.s3 as service.
 
1.13. 	Click Create VPC.
1.14. 	In the navigation pane, choose Security Groups.
1.15. 	Select the Security Group that attach on “Redshift-VPC” which group name is default then select Inbound Rules.
1.16. 	Click Edit
1.17. 	Click Add another rule below, Type for ALL Traffic, Source for 0.0.0.0/0.
1.18. 	Click Save.
 
1.19. 	On the Services menu, click Amazon Redshift.
1.20. 	In the navigation pane, choose Security.
1.21. 	Select Subnet Groups and click Create Cluster Subnet Group.
1.22. 	Enter the Name “redshift-sg”.
1.23. 	Enter the Description “SG for redshift”.
1.24. 	Select the VPC ID (vpc-xxxxxxxx)same as Redshift-VPC that you create before.
1.25. 	Click add all the subnets then click Create.
 
1.26. 	In the navigation pane, choose Clusters.
1.27. 	Click Launch cluster
1.28. 	Enter Cluster identifier “my-cluster”
1.29. 	Enter Database name “mydb”
1.30. 	Leave Database port for 5439
1.31. 	Enter your own Master user name and Master user password and type again your password in Confirm password then click Continue. (e.g., Master user name: james, Master user password: James123)
1.32. 	Select dc2.large for the Node type which is the cheapest cluster.
1.33. 	Click Continue.
1.34. 	Select VPC ID of “Redshift-VPC” in Choose a VPC blank.
1.35. 	In Available roles choose SpectrumRole then click Continue.
1.36. 	After examine that all setting is correct, click Launch cluster.
(This instance will charged $0.25 hourly)
Detail pricing issues https://aws.amazon.com/tw/redshift/pricing/#
    At launching time, cluster creation times averaged 15 minutes
 
1.37. 	On the Services menu, click AWS Glue.
1.38. 	In the navigation pane, choose Connections.
1.39. 	Click Add connection.
1.40. 	Enter Connection name “redshift-spectrum”.
1.41. 	Select Connection type “Amazon Redshift” and click next.
1.42. 	Select my-cluster in Cluster blank.
1.43. 	Enter Database name “mydb”.
1.44. 	Enter your own Username and Password then click Next.
1.45. 	Click Finish.
1.46. 	Select redshift-spectrum and click Test connection.
1.47. 	Select AWSGlueServiceRoleDefault as IAM role and click Test connection. You will find below screen after testing.
 
 
1.48. 	On the Services menu, click AWS Glue.
1.49. 	In the navigation pane, choose Jobs.
1.50. 	Click Add job.
1.51. 	Enter the Name “redshift-query”.
1.52. 	Select AWSGlueServiceRoleDefault as IAM role and click Next.
1.53. 	Select “usvideos_csv” and click Next.
 
1.54. 	Choose Create tables in your data target.
1.55. 	Select Data store as JDBC.
1.56. 	Select redshift-spectrum for Connection and enter the Database name “mydb” then click Next.
1.57. 	Click Next you will find this screen below.
 
1.58. 	Click finish.
1.59. 	View the job. This screen provides a complete view of the job and allows you to edit, click Save, and choose Run job. This steps may be waiting around 10 minutes.
In this job, Glue send data to Redshift cluster and processing data by the cluster.




### Data visualization with QuickSight


 


## Appendix


### Visualize data with Tableau
