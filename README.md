# Serverless ETL and data analysis on AWS

![kaggle_data.png](/images/kaggle_data.png)

**This lab using Trending YouTube Video Statistics dataset on Kaggle**

## Scenario

Various of enterprises nowadays facing with big data problems. Most of them concern about data mining, data cleaning, data analytics, and even about machine learning workflow. With workflow being more complicated, it brings out lots of trouble and management issues. For example, an ETL job is a big challenge for many companies. In addition to data processing, server provision and server management are still take time and effort. The big data workflow is also relate to huge cost because it is too hard to use the server effectively.

AWS plays an important role of big data solution. We will focus on AWS Glue to perform serverless ETL job. You can use AWS Glue to build a data warehouse to organize, cleanse, validate, and format data. The architecture also integrate with several services to fulfill data analysis and BI process that contains **S3**, **Lambda**, **Comprehend**, **Glue**, **Athena**, **Redshift Spectrum**, **QuickSight**.


## Use Case in this Lab 
* Dataset: Trending YouTube Video Statistics
https://www.kaggle.com/datasnaek/youtube-new
With the large growth of YouTube, it plays an important role of video service. The large company also use it to determine their ads strategies and marketing plans.

This use case using trending YouTube video data to analyze which video channel or video type are suitable for advertising. In addition, YouTubers can make themselves more popular by analyzing the trending videos of YouTube. We use AWS Glue to do serverless ETL job and analyze those big data automatically with BI tool that integrate with AWS Athena or AWS Redshift Spectrum.

![preview_data1.png](/images/preview_data1.png)

![preview_data2.png](/images/preview_data2.png)

## Prerequisites

* An AWS account

* Make sure the region is **US East (N. Virginia)**, which its short name is **us-east-1**.

* Download **this repository** and unzip, ensure that **data** folder including three files:
    * **USvideos.csv**
    * **US-category-id.json**
    * **word_analysis.csv**

## Lab Architecture
![lab_architecture.png](/images/lab_architecture2.png)

As illustrated in the preceding diagram, this is a big data processing in this model:
1. **Store**
    * [Developer scraping the data and store it into S3.](https://github.com/ecloudvalley/Serverless-ETL-and-data-analysis-on-AWS/tree/master/Scraping%20the%20data%20and%20store%20into%20S3)

2. **ETL**
    * Once S3 get the data then [trigger a Lambda function to do ETL with Glue.](https://github.com/ecloudvalley/Serverless-ETL-and-data-analysis-on-AWS/tree/master/Trigger%20Lambda%20function%20to%20do%20ETL%20with%20Glue)

    * On the other hand, [AWS Comprehend doing topic modeling job as sentiment analysis with certain data.](https://github.com/ecloudvalley/Serverless-ETL-and-data-analysis-on-AWS/tree/master/Using%20AWS%20Comprehend%20do%20topic%20modeling%20job)

3. **Analysis and Visualize**

    Here you can choose Athena or Redshift to perform query job know as data analysis when ETL job finished, both of them can be used for data analysis, ***choose one suitable for your application***.

    * [Analyze and visualize the data with Athena and QuickSight](https://github.com/ecloudvalley/Serverless-ETL-and-data-analysis-on-AWS/tree/master/Analyze%20and%20visualize%20the%20data%20with%20Athena%20and%20QuickSight)

    * [Analyze and visualize the data with Redshift and QuickSight](https://github.com/ecloudvalley/Serverless-ETL-and-data-analysis-on-AWS/tree/master/Analyze%20and%20visualize%20the%20data%20with%20Redshift%20and%20QuickSight)

## Clean Up
(WIP)

## Conclusion
(WIP)



