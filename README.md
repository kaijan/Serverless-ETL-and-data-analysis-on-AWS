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


#### Create following IAM roles

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



#### Create S3 bucket to store data

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



#### Setup data catalog in AWS Glue

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



#### Create a Lambda function in order to automate ETL job with glue




#### Create a Lambda to trigger topic detection jobs by Comprehend






#### Data visualization with QuickSight


 


## Appendix


#### Visualize data with Tableau
