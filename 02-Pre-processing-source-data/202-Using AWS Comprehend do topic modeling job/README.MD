# Using AWS Comprehend do topic modeling job

You use AWS Glue to set up and manage your extract, transform, and load (ETL) workload. To set up crawlers that scan data in Amazon S3, classify it, extract schema information from it, and store the metadata automatically in the AWS Glue Data Catalog. Then trigger a Lambda function to automate ETL job.

Next, let's use Amazon Comprehend to create and manage asynchronous topic detection jobs.


## Prerequisites

* Make sure the region is **US East (N. Virginia)**, which its short name is **us-east-1**.


## Setup topic detection jobs by Amazon Comprehend

Amazon Comprehend uses a pre-trained model to examine and analyze a document or set of documents to gather insights about it. This model is continuously trained on a large body of text so that there is no need for you to provide training data. Topic modeling can search the content of documents to determine common themes and topics.

### To create a topic detection job

1. 	On the **Services** menu, click **Amazon Comprehend**.

2.   Click **Try Amazon Comprehend**.

3.   Click **switch to the old console** on the top of console.

4. 	In the navigation pane, click **Topic modeling**.

5. 	Click **Create**.

6. 	Select **My data (S3)** as input data.

7. 	For **S3 data location** of input data, enter the URL of **“yourname-topic-analysis”** bucket **(e.g., s3://james-topic-analysis/)**.

8. 	For Input format, select **One document per line**.

9. 	Enter **50** for **Numbers of topic**.

10. For **Job Name**, enter **first_job**.

11. For **S3 data location** of output data, enter the URL of **“yourname-topic-analysis-result” (e.g., s3://james-topic-analysis-result/)**.

12. Select **Create a new IAM role** in **Select an IAM role** blank.

13. For **Permission to access** select **any S3 bucket**.

14. For **Name suffix** enter **user**.

![setup_comprehend1.png](./images/setup_comprehend1.png)

15. Click **Create job** to create and start the topic detection job.

16. It will take a few time running.

<p align="center">
    <img src="images/setup_comprehend2.png" width="40%" height="40%">
</p>

17. Then you will see your job is running.

![setup_comprehend3.png](./images/setup_comprehend3.png)

18. When the job complete the status change to *Completed*. You can click reflesh icon if waiting a while.

19. After job completed, you will find a new output in **yourname-topic-analysis-result** bucket in Amazon S3.

20. Click below folder:

![setup_comprehend4.png](./images/setup_comprehend4.png)

21. Click **output** you will see a file **output.tar.gz**.

![setup_comprehend5.png](./images/setup_comprehend5.png)

22. Download the file, unzip it and you will get the topic modeling result as csv file.

<p align="left">
    <img src="images/setup_comprehend6.png" width="80%" height="80%">
</p>

<p align="left">
     <img src="images/setup_comprehend7.png" width="60%" height="60%">
</p>

## Furthermore

* Read [Automated topic modeling job](Automated-topic-modeling-job.md) in order to set up an automated topic modeling job with Lambda function.

## Clean Up

* Go to **S3** and **delete** buckets you create.





## Congratulations! You  have learned how to:

*  Using AWS Comprehend do topic modeling job.

*  Set up an automated topic modeling job with Lambda function.


### Now you are ready to analyze the data with Athena or Redshift

* [Analyze data with Athena](https://github.com/ecloudvalley/Serverless-ETL-and-data-analysis-on-AWS/tree/master/Analyze%20and%20visualize%20the%20data%20with%20Athena%20and%20QuickSight)

* [Analyze data with Redshift](https://github.com/ecloudvalley/Serverless-ETL-and-data-analysis-on-AWS/tree/master/Analyze%20and%20visualize%20the%20data%20with%20Redshift%20and%20QuickSight)

