

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


