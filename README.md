# Serverless ETL and data analysis on AWS
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

![learnflow.png](/images/learnflow.png)<br>

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

Athena can query the data in an easy way with data catalog of Glue<br><br>
* 	On the **Services** menu, click **Athena**.<br><br>
* 	On the **Query Editor** tab, choose the database **my-data**.<br><br>
![athena1.png](/images/athena1.png)<br> 
* 	Choose the **yourname_etl_result** table.<br><br>
* 	Query the data, type below standard SQL:<br><br>

         Select * From "my-data"."yourname_etl_result" limit 100;
         (e.g., Select * From "my-data"."james_etl_result" limit 100;)

* 	Click **Run Query** and Athena will query data as the below screen<br><br>
![athena2.png](/images/athena2.png)<br>  
* 	Choose the **yourname_etl_result2** table.<br><br>
* 	Query the data, type below standard SQL:<br><br>

         Select * From "my-data"."yourname_etl_result2";
         (e.g., Select * From "my-data"."james_etl_result2";)

Click **Run Query** and Athena will query data as the below screen<br><br>
 ![athena3.png](/images/athena3.png)<br> 
 
After finish analyzing data in Athena, get start with data analysis with **Redshift Spectrum**. **Different from Athena, Redshift is suitable for long term workflow of data analysis that often processing structural data query job**.<br><br>
*	First setup VPC in which you want to create your cluster<br><br>
*	Launch Redshift cluster and connect to AWS Glue<br><br>
* 	On the **Services** menu, click **VPC**.<br><br>
* 	Click **Start VPC Wizard**.<br><br>
* 	In this workshop we simply choose **VPC with a single Public Subnet**<br><br>
* 	Enter your VPC name **“Redshift-VPC”**.<br><br>
* 	Click **Add Endpoint** below **Service endpoints** and select **com.amazonaws.us-east-1.s3** as service.<br><br>
![redshift1.png](/images/redshift1.png)<br> 
* 	Click **Create VPC**.<br><br>
* 	In the navigation pane, choose **Security Groups**.<br><br>
* 	Select the Security Group that attach on **“Redshift-VPC”** which group name is **default** then select **Inbound Rules**.<br><br>
* 	Click **Edit**<br><br>
*	Click **Add another rule** below, **Type** for **ALL Traffic**, Source for **0.0.0.0/0**.<br><br><br><br>
* 	Click **Save**.<br><br>
![redshift2.png](/images/redshift2.png)<br>  
* 	On the **Services** menu, click **Amazon Redshift**.<br><br>
* 	In the navigation pane, choose **Security**.<br><br>
* 	Select **Subnet Groups** and click **Create Cluster Subnet Group**.<br><br>
*	Enter the **Name “redshift-sg”**.<br><br>
* 	Enter the **Description “SG for redshift”**.<br><br>
* 	Select the **VPC ID** (vpc-xxxxxxxx)same as **Redshift-VPC** that you create before.<br><br>
* 	Click **add all the subnets** then click **Create**.<br><br>
![redshift3.png](/images/redshift3.png)<br>  
* 	In the navigation pane, choose **Clusters**.<br><br>
* 	Click **Launch cluster**<br><br>
* 	Enter **Cluster identifier “my-cluster”**<br><br>
* 	Enter **Database name “mydb”**<br><br>
* 	Leave **Database port** for **5439**<br><br>
* 	Enter your own **Master user name** and **Master user password** and type again your password in **Confirm password** then click **Continue**. (e.g., Master user name: james, Master user password: James123)<br><br>
* 	Select **dc2.large** for the **Node type** which is the cheapest cluster.<br><br>
* 	Click **Continue**.<br><br>
* 	Select VPC ID of **“Redshift-VPC”** in **Choose a VPC** blank.<br><br>
* 	In **Available roles** choose **SpectrumRole** then click **Continue**.<br><br>
* 	After examine that all setting is correct, click **Launch cluster**.<br>
(**This instance will charged $0.25 hourly**)<br>
Detail pricing issues https://aws.amazon.com/tw/redshift/pricing/#<br>
    At launching time, cluster creation times averaged 15 minutes<br><br>
![redshift4.png](/images/redshift4.png)<br>  
* 	On the **Services** menu, click **AWS Glue**.<br><br>
* 	In the navigation pane, choose **Connections**.<br><br>
* 	Click **Add connection**.<br><br>
* 	Enter **Connection name “redshift-spectrum”**.<br><br>
* 	Select **Connection type “Amazon Redshift”** and click **next**.<br><br>
* 	Select **my-cluster** in **Cluster** blank.<br><br>
* 	Enter **Database name “mydb”**.<br><br>
* 	Enter your own **Username** and **Password** then click **Next**.<br><br>
* 	Click **Finish**.<br><br>
* 	Select **redshift-spectrum** and click **Test connection**.<br><br>
* 	Select **AWSGlueServiceRoleDefault** as **IAM role** and click **Test connection**. You will find below screen after testing.<br><br>
![redshift5.png](/images/redshift5.png)<br>  
![redshift6.png](/images/redshift6.png)<br>  
* 	On the **Services** menu, click **AWS Glue**.<br><br>
* 	In the navigation pane, choose **Jobs**.<br><br>
*.	Click **Add job**.<br><br>
* 	Enter the **Name “redshift-query”**.<br><br>
* 	Select **AWSGlueServiceRoleDefault** as **IAM role** and click **Next**.<br><br>
* 	Select **“usvideos_csv”** and click **Next**.<br><br>
![redshift7.png](/images/redshift7.png)<br>  
* 	Choose **Create tables in your data target**.<br><br>
* 	Select **Data store** as **JDBC**.<br><br>
* 	Select **redshift-spectrum** for **Connection** and enter the **Database name “mydb”** then click **Next**.<br><br>
* 	Click **Next** you will find this screen below.<br><br>
![redshift8.png](/images/redshift8.png)<br>  
* 	Click **finish**.<br><br>
* 	View the job. This screen provides a complete view of the job and allows you to edit, click **Save**, and choose **Run job**. This steps may be waiting around 10 minutes.<br><br>
In this job, Glue send data to Redshift cluster and processing data by the cluster.<br><br>
![redshift9.png](/images/redshift9.png)<br> 



### Data visualization with QuickSight

Now we have finished data analysis with Athena and Redshift Spectrum<br><br>
You will learn how to use QuickSight (AWS BI service) to visualize the data<br><br>
•	First you need to register QuickSight<br><br>
**Please confirm the user have Administrator permission to sign up QuickSight.**<br><br>
•	Click **“Sign up for QuickSight”**<br><br>
![qs1.png](/images/qs1.png)<br>  
•	Select the subscription type<br><br>
![qs2.png](/images/qs2.png)<br>  
 
•	Provide requested information and setting then click on [Finish]<br><br>
![qs3.png](/images/qs3.png)<br>  
•	It takes a while to enable your account.<br><br>
![qs4.png](/images/qs4.png)<br>  
•	Finish register you will find the screen below<br><br>
![qs5.png](/images/qs5.png)<br>  
![qs6.png](/images/qs6.png)<br>  
![qs7.png](/images/qs7.png)<br>  
If you are new to QuickSight, the analyses board will be empty<br><br>
•	Now we can get start with **QuickSight**<br><br>
* 	Click **Manage data** and you will see this screen. If you were first time log in QuickSight console, there is no data sets record.<br><br>
![qs8.png](/images/qs8.png)<br>  
* 	Click **New data set** and you will find that many data sources<br><br>
![qs9.png](/images/qs9.png)<br>  
* 	Click on **Athena** and enter **“AthenaBI”** in **Data source name**, then click **Validate connection**<br><br>
![qs10.png](/images/qs10.png)<br>  
* 	Click **Create data source** and select **“my-data”** database. For tables, choose **yourname_etl_result** and click **Select**.<br><br>
![qs11.png](/images/qs11.png)<br>  
![qs12.png](/images/qs12.png)<br>  
* 	When loading finish click **Visualize**<br><br>
![qs13.png](/images/qs13.png)<br>  
![qs14.png](/images/qs14.png)<br>  
* 	Select a **Visual types (e.g., Vertical bar chart)**<br><br>
![qs15.png](/images/qs15.png)<br>  
* 	Drag the field items which below **Fields list** into **Field wells** and you can create different BI charts.<br><br>
![qs16.png](/images/qs16.png)<br>  
![qs17.png](/images/qs17.png)<br>  
![qs18.png](/images/qs18.png)<br>  
This chart analyze that whether views and comment impact on likes. The video **“Falcon Heavy Test Flight”** is one of the example that really affected.<br><br>

* 	You can analyze the channel type of the video by **yourname_etl_result2 bucket** using Athena (e.g., Entertainment)<br><br>
*	Create a **New data set** and choose Athena as data sources<br><br>
* 	Select database **“my-data”** and select the table **“yourname_etl_result2"**<br><br>
![qs19.png](/images/qs19.png)<br>  
*	Click **Select** and **Visualize**<br><br>
* 	Select table chart to display the title and id of category<br><br>
![qs20.png](/images/qs20.png)<br>  
You can query certain **category id** to do several analytics<br><br>
* 	Back to your QuickSight data sets page click **New data set**<br><br>
![qs21.png](/images/qs21.png)<br>  
* 	Choose **Athena** and enter data source name **“Entertainment”** <br><br>
* 	Select **yourname_etl_result** in **my-data** database and click **Edit / Preview data**<br><br>
![qs22.png](/images/qs22.png)<br>  
* 	In the navigation pane under **Tables**, select **Use SQL**<br><br>
![qs23.png](/images/qs23.png)<br>  
* 	Enter SQL name **“Entertainment”** and paste below code in Custom SQL field <br><br>

         Select * From "my-data"."yourname_etl_result" where category_id = 24;
         (category_id =24 for Entertainment category)

![qs24.png](/images/qs24.png)<br>  
* 	Click **Finish** then Click on **Save & visualize**<br><br>
* 	Then you can start customize your chart to do your BI strategy<br><br>
![qs25.png](/images/qs25.png)<br>  
This plot shows the factors that impact Entertainment video type<br><br>

Congratulations! You now have learned how to visualize data on Quicksight with Athena. **Next step we will visualize data on QuickSight with Redshift**.<br><br>
 
* 	Back to QuickSight home page and click **New analysis**.<br><br>
* 	Click **New data set** and select **Redshift (Manual connect)**<br><br>
![qs26.png](/images/qs26.png)<br>  
* 	Enter Data source name **RedshiftBI**<br><br>
* 	Enter **Database server** as your redshift cluster **Endpoint**<br>
(e.g.,  my-cluster.ceng4yniaba2.us-east-1.redshift.amazonaws.com)<br><br><br><br>
![qs27.png](/images/qs27.png)<br>  
* 	Enter Port **5439**<br><br>
* 	Enter **Database name “mydb”**<br><br>
* 	Enter your **Username** and **Password** of redshift cluster that you create before<br><br>
* 	Click **Validate connection**<br><br>
* 	Click **Create data source**<br><br>
![qs28.png](/images/qs28.png)<br>  
* 	Select **usvideos_csv** as table and click **Select**.<br><br>
![qs29.png](/images/qs29.png)<br>  
* 	Click **Visualize**.<br><br>
* 	Select a **Visual types** then drag the field items which below **Fields list** into **Field wells** and you can create different BI charts just like Athena data source.<br><br>
![qs30.png](/images/qs30.png)<br>  
![qs31.png](/images/qs31.png)<br>  
![qs32.png](/images/qs32.png)<br>  

Now we import the result of topic modeling data (**topic-terms.csv**) to integrate with QuickSight<br><br>
* 	Back to **QuickSight** console<br><br>
* 	Click **New analysis**<br><br>
* 	Click **New data set**<br><br>
* 	Click **Upload a file**<br><br>
![qs33.png](/images/qs33.png)<br>  
* 	Select **topic-terms.csv** and upload<br><br>
* 	Click **Next**<br><br>
![qs34.png](/images/qs34.png)<br>  
* 	Click **Visualize**<br><br>
![qs35.png](/images/qs35.png)<br>  
* 	Select a **Visual types** then drag the field items which below **Fields list** into **Field wells** and you can create different BI charts.<br><br>
* 	For example, **horizontal stacked bar chart** shows that sum of weight by term.<br><br>
We can know that the word **“president”** is often appears in US videos.<br><br>
![qs36.png](/images/qs36.png)<br>  
You can query certain trending topic of **title** to do several analytics<br>
(For example, video relate to **President**)<br><br>
* 	Back to your QuickSight data sets page click **New data set**<br><br>
![qs37.png](/images/qs37.png)<br>  
* 	Choose **Athena** and enter data source name **“President”** <br><br>
* 	Select **yourname_etl_result** in **my-data** database and click **Edit / Preview data**<br><br>
![qs38.png](/images/qs38.png)<br>  
* 	In the navigation pane under **Tables**, select **Use SQL**<br><br>
![qs39.png](/images/qs39.png)<br>  
* 	Enter SQL name **“President”** and paste below code in Custom SQL field <br>

         Select * From "my-data"."james_etl_result" where title like '%President%'; 
         (for the title that contain “President”)

![qs40.png](/images/qs40.png)<br>  
*	Click Finish then Click on **Save & visualize**<br><br>
* 	Then you can create several plots of **President title video**<br><br>
![qs41.png](/images/qs41.png)<br>  
Pie Chart example<br><br>
Congratulations! You now have finished whole Lab and have learned how to do **Serverless ETL with AWS Glue & BI process with QuickSight**.<br>

 


## Appendix


### Visualize data with Tableau

When it comes to BI, Tableau is one of the best tools.<br><br>
The following steps will integrate AWS services with Tableau. (Athena for this example)<br><br>
* 	First you need to download Tableau on premise or on EC2.<br><br>
Click below link to download<br><br>
Note that download the version higher than 10.3 (10.5 for this example)<br><br>
https://www.tableau.com/support/releases<br>
https://www.tableau.com/support/releases/server<br>
* 	Make sure that you have license to use Tableau<br><br>
https://www.tableau.com/pricing<br>
* 	Open Tableau you will see this screen (Tableau Desktop for example)<br><br>
![tableau1.png](/images/tableau1.png)<br>  
* 	To connect to Athena, click **Amazon Athena** in navigation pane left side <br><br>
![tableau2.png](/images/tableau2.png)<br>
* 	Enter **“Athena.us-east-1.amazonaws.com”** in **Server**<br><br>
* 	Enter **port** for 443<br><br>
* 	Enter Staging Directory for your **Athena query result S3 bucket**<br><br>
* 	Enter **Access Key ID** and **Secret Access Key** then click **sign in**<br><br>
![tableau3.png](/images/tableau3.png)<br> 
* 	Select **AwsDataCatalog** in **Catalog** and select **my-data** in **database**<br><br>
* 	Drag the table you want to make the chart for<br><br>
![tableau4.png](/images/tableau4.png)<br><br>
![tableau5.png](/images/tableau5.png)<br><br>
1.11. 	Click **New Worksheet** icon below then you can start making your chart to do BI
![tableau6.png](/images/tableau6.png)<br><br> 
![tableau7.png](/images/tableau7.png)<br><br>
![tableau8.png](/images/tableau8.png)<br><br>

