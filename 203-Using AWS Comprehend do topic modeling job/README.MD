# Using AWS Comprehend do topic modeling job

You use AWS Glue to set up and manage your extract, transform, and load (ETL) workload. To set up crawlers that scan data in Amazon S3, classify it, extract schema information from it, and store the metadata automatically in the AWS Glue Data Catalog. Then trigger a Lambda function to automate ETL job.

Next, let's use Amazon Comprehend to create and manage asynchronous topic detection jobs.


## Prerequisites

* Make sure the region is **US East (N. Virginia)**, which its short name is **us-east-1**.


## Setup topic detection jobs by Amazon Comprehend

Amazon Comprehend uses a pre-trained model to examine and analyze a document or set of documents to gather insights about it. This model is continuously trained on a large body of text so that there is no need for you to provide training data. Topic modeling can search the content of documents to determine common themes and topics.

### To create a topic detection job

* 	On the **Services** menu, click **Amazon Comprehend**.

*   Click **Try Amazon Comprehend**.

*   Click **switch to the old console** on the top of console.

* 	In the navigation pane, click **Topic modeling**.

* 	Click **Create**.

* 	Select **My data (S3)** as input data.

* 	For **S3 data location** of input data, enter the URL of **“yourname-topic-analysis”** bucket **(e.g., s3://james-topic-analysis/)**.

* 	For Input format, select **One document per line**.

* 	Enter **50** for **Numbers of topic**.

* 	For **Job Name**, enter **FirstJob**.

* 	For **S3 data location** of output data, enter the URL of **“yourname-topic-analysis-result” (e.g., s3://james-topic-analysis-result/)**.

* 	Select **Create a new IAM role** in **Select an IAM role** blank.

* 	For **Permission to access** select **any S3 bucket**.

* 	For **Name suffix** enter **“user”**.

![setup_comprehend1.png](/images/setup_comprehend1.png)

* 	Click **Create job** to create and start the topic detection job.

* 	It will take a few time running.

![setup_comprehend2.png](/images/setup_comprehend2.png)

* 	Then you will see your job is running.

![setup_comprehend3.png](/images/setup_comprehend3.png)

* 	When the job complete the status change to *Completed*.

* 	After job completed, you will find a new output in **yourname-topic-analysis-result** bucket in Amazon S3.

* 	Click below folder:

![setup_comprehend4.png](/images/setup_comprehend4.png)

*   You will find that a **output** folder inside. 

![setup_comprehend5.png](/images/setup_comprehend5.png)

*   Click **output** you will see a file **output.tar.gz**.

![setup_comprehend6.png](/images/setup_comprehend6.png)

*   Download the file you will get the topic modeling result as csv file.

![setup_comprehend7.png](/images/setup_comprehend7.png)

![setup_comprehend8.png](/images/setup_comprehend8.png)


## Automated topic modeling job

### Create a Lambda to trigger topic detection jobs by Amazon Comprehend

* 	On the **Services** menu, click **Lambda**.

* 	Click **Create function**.

* 	Choose **Author from scratch**.

* 	Enter function Name: **comprehend-lambda**.

* 	Select **python 3.6** in **Runtime** blank.

* 	Select **Choose an existing role** in **Role** blank and choose **Comprehend-Job** as **Existing role**.

* 	Click **Create function**.

* 	Click **comprehend-lambda** blank in **Designer** and replace original code that existing in **Function code** editor with below code

    *   Input_s3_url = “s3://yourname-topic-analysis”
    
    *   output_s3_url = "s3://yourname-topic-analysis-result"
    
    *   data_access_role_arn = "arn:aws:iam::xxxxxxxxxxxx:role/service-role/AmazonComprehendServiceRoleS3FullAccess-user"
        (The arn of IAM role that you create in Amazon Comprehend console)
 
 
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
 

* 	In **configuration**, click **S3** below **Add triggers** to add trigger for **comprehend-lambda** function

* 	and drop down to **Configure triggers** part, select bucket **“yourname-topic-analysis”** as Bucket, select **PUT** as **Event type**. Remember to check Enable trigger box then you click **Add**.

![lambda_comprehend1.png](/images/lambda_comprehend1.png)

* 	Click **Save** to save the change of function.

* 	Now you can re-upload a csv file into **“yourname-topic-analysis”** bucket to test that whether this Lambda function operating normally.

* 	On the **Services** menu, click **S3**.

* 	Click **yourname-topic-analysis** bucket.

* 	Click **Upload**.

* 	Click **Add files**.

*	Select file **word_analysis.csv** and click **Upload**.

* 	When it upload finish go to your **Amazon comprehend** console.

* 	Click **Topic modeling**.

* 	**You will find a new job is running**

![lambda_comprehend2.png](/images/lambda_comprehend2.png)

* 	Download the output of job in **yourname-topic-analysis-result** bucket after job completed which is a result from topic modeling.

![lambda_comprehend3.png](/images/lambda_comprehend3.png)

![lambda_comprehend4.png](/images/lambda_comprehend4.png)

![lambda_comprehend5.png](/images/lambda_comprehend5.png)

![lambda_comprehend6.png](/images/lambda_comprehend6.png)

![lambda_comprehend7.png](/images/lambda_comprehend7.png)

![lambda_comprehend8.png](/images/lambda_comprehend8.png)

## Congratulations! You now have learned how to:

*  Using AWS Comprehend do topic modeling job.

*  Setup an automated topic modeling job with Lambda function.


### Now you are ready to [Analyze the data with Athena and Redshift](https://github.com/ecloudvalley/Serverless-ETL-and-data-analysis-on-AWS/tree/master/204-Analyze%20the%20data%20with%20Athena%20and%20Redshift)
