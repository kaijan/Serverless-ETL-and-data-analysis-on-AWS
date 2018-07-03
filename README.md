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


#### Setup data catalog in AWS Glue


#### Create a Lambda function in order to automate ETL job with glue




#### Create a Lambda to trigger topic detection jobs by Comprehend






#### Data visualization with QuickSight


 


## Appendix


#### Visualize data with Tableau
