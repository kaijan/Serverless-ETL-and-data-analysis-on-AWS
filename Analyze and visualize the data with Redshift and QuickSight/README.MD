# Analyze and visualize the data with Redshift and QuickSight

You use Amazon Comprehend to examine and analyze a document to determine common themes.

Next, we will use Redshift to do data analysis that often processing structural data query job for long term workflow. Then use Amazon BI tool - QuickSight, it is a business analytics service you can use to build visualizations, perform ad hoc analysis, and get business insights from your data. It can automatically discover AWS data sources and also works with your data sources.

## Data analysis with Redshift and connect to AWS Glue

After finish analyzing data in Athena, get start with data analysis with Amazon Redshift. Different from Athena, it is recommended to use Amazon Redshift on large sets of structured data. It is scalable enough that even if new nodes are added to the cluster, it can be easily accommodated with few configuration changes.

Amazon Redshift is a petabyte-scale data warehouse used together with business intelligence tools for modern analytical solutions. Unlike Athena, Redshift requires a **cluster** for which we need to upload the data extracts and build tables before we can query. 

### Setting up VPC for Redshift

First setup VPC in which you want to create your cluster.

* On the **Services** menu, click **VPC**.

* Click **Launch VPC Wizard**.

* In this workshop we simply choose **VPC with a single Public Subnet**, click **Select**.

* Enter your VPC name: **Redshift-VPC**.

* Click **Add Endpoint** below **Service endpoints** and select **com.amazonaws.us-east-1.s3** as service.

![redshift1.png](./images/redshift1.jpg)

* Click **Create VPC**.

* In the navigation pane, choose **Security Groups**.

* Select the Security Group that attach on **Redshift-VPC** which group name is **default** then select **Inbound Rules**.

* Click **Edit rules**.

* Click **Add Rule** below, type for **ALL Traffic**, Source for **0.0.0.0/0**.

* Click **Save rules**, and **Close**.

![redshift2.png](./images/redshift2.jpg)

### Setting up Redshift

* On the **Services** menu, click **Amazon Redshift**.

* In the navigation pane, choose **Security**.

* Select **Subnet Groups** and click **Create Cluster Subnet Group**.

* Enter the Name: **redshift-sg**.

* Enter the Description: **SG for redshift**.

* Select the **VPC ID** (vpc-xxxxxxxx) same as **Redshift-VPC** that you create before.

* Click **add all the subnets** then click **Create**.

![redshift3.png](./images/redshift3.jpg)

* In the navigation pane, choose **Clusters**.

* Click **Launch cluster**.

* Enter Cluster identifier: **my-cluster**.

* Enter Database name: **mydb**.

* Leave **Database port** for **5439**.

* Enter your own **Master user name** and **Master user password** and type again your password in **Confirm password** then click **Continue**. (e.g., Master user name: james, Master user password: James123), click **Continute**.

* Select **dc2.large** for the **Node type** which is the cheapest cluster.

* Click **Continue**.

* Select VPC ID of **Redshift-VPC** in **Choose a VPC** blank.

* In **Available roles** choose **SpectrumRole** then click **Continue**.

* After examine that all setting is correct, click **Launch cluster**.

* It will need 10-15 minutes passed before the cluster was ready to use.
    
![redshift4.png](./images/redshift4.jpg)

### Connect to AWS Glue

* On the **Services** menu, click **AWS Glue**.

* In the navigation pane, choose **Connections**.

* Click **Add connection**.

* Enter Connection name: **redshift-connection**.

* Select Connection type **Amazon Redshift**.

* choose **Require SSL connection**, and click **Next**.

* 	Select **my-cluster** in **Cluster** blank.

* Enter Database name: **mydb**.

* Enter your own **Username** and **Password** then click **Next**.

* Click **Finish**.

* Select **redshift-spectrum**, and click **Test connection**.

* Select **AWSGlueServiceRoleDefault** as **IAM role**, and click **Test connection**. You will find below screen after testing.

![redshift5.png](./images/redshift5.jpg)

* On the **Services** menu, click **AWS Glue**.

* In the navigation pane, choose **Jobs**.

* Click **Add job**.

* Enter the Name: **redshift-query**.

* Select **AWSGlueServiceRoleDefault** as **IAM role**, and click **Next**.

* Select **usvideos_csv**, and click **Next**.

![redshift7.png](./images/redshift7.jpg)

* Choose **Create tables in your data target**.

* Select **Data store** as **JDBC**.

* Select **redshift-connection** for **Connection** and enter the **Database name: mydb** then click **Next**.

* 	Click **Save job and edit script** you will find this screen below.

![redshift8.png](./images/redshift8.jpg)

* 	Click **Save job and edit script**.

* 	View the job. This screen provides a complete view of the job and allows you to edit, click **Save**, and choose **Run job**. This steps may be waiting around 10 minutes.

    * In this job, AWS Glue send data to Redshift cluster and processing data by the cluster.

![redshift9.png](./images/redshift9.jpg)

## Data visualization with QuickSight

### Setting Up Amazon QuickSight

* Go to AWS Services page by searching **Quicksight** by AWS Services search bar.

    * Please confirm the user have Administrator permission to sign up QuickSight.

* Provide your email address on **Welcome to QuickSight page**. Click **Continue** and **Next** until to finish **Get Started**.

* Click **Sign up for QuickSight**.

![qs1.png](./images/qs1.png)

* Choose **Standard**, click **Continue**. 
 
* Provide requested information and setting then click on **Finish**.

    * Type in a your **email** address to receive Amazon QuickSight service and usage notifications.

![qs3.png](./images/qs3.png)

* It takes a while to enable your account.

![qs4.png](./images/qs4.png)

* Finish register you will find the screen below:

![qs5.png](./images/qs5.png)

![qs6.png](./images/qs6.png)

![qs7.png](./images/qs7.png)

* If you are new to QuickSight, the analyses board will be empty, now we can get start with **QuickSight**.

### Create an Analysis with Redshift
 
* 	Back to QuickSight home page and click **New analysis**.

* 	Click **New data set** and select **Redshift (Manual connect)**.

![qs26.png](./images/qs26.png)

* 	Enter Data source name **RedshiftBI**

* 	Enter **Database server** as your redshift cluster **Endpoint**.

    * e.g., my-cluster.ceng4yniaba2.us-east-1.redshift.amazonaws.com

![qs27.png](./images/qs27.png)

* 	Enter Port **5439**.

* 	Enter **Database name: mydb**.

* 	Enter your **Username** and **Password** of redshift cluster that you create before.

* 	Click **Validate connection**.

* 	Click **Create data source**.

![qs28.png](./images/qs28.png)

* 	Select **usvideos_csv** as table and click **Select**.

![qs29.png](./images/qs29.png)

* 	Click **Visualize**.

* 	Select a **Visual types** then drag the field items which below **Fields list** into **Field wells** and you can create different BI charts just like Athena data source.

![qs30.png](./images/qs30.png)

![qs31.png](./images/qs31.png)

![qs32.png](./images/qs32.png)

* Now we import the result of topic modeling data (**topic-terms.csv**) to integrate with QuickSight.

* 	Back to **QuickSight** console.

* 	Click **New analysis**.

* 	Click **New data set**.

* 	Click **Upload a file**.

![qs33.png](./images/qs33.png)

* 	Select **topic-terms.csv** and upload.

* 	Click **Next**.

![qs34.png](./images/qs34.png)

* 	Click **Visualize**

![qs35.png](./images/qs35.png)

* 	Select a **Visual types** then drag the field items which below **Fields list** into **Field wells** and you can create different BI charts.

* 	For example, **horizontal stacked bar chart** shows that sum of weight by term. We can know that the word **president** is often appears in US videos.

![qs36.png](./images/qs36.png)

* You can query certain trending topic of **title** to do several analytics. For example, video relate to **President**.

* 	Back to your QuickSight data sets page click **New data set**.

![qs37.png](./images/qs37.png)

* 	Choose **Athena** and enter data source name **President**. 

* 	Select **yourname_etl_result** in **my-data** database and click **Edit / Preview data**

![qs38.png](./images/qs38.png)

* 	In the navigation pane under **Tables**, select **Use SQL**.

![qs39.png](./images/qs39.png)

* 	Enter SQL name **President** and paste below code in Custom SQL field.

         Select * From "my-data"."yourname_etl_result" where title like '%President%'; 

    *  It means for the title that contain “President”

![qs40.png](./images/qs40.png)

*	Click Finish then Click on **Save & visualize**.

* 	Then you can create several plots of **President title video**. For example, Pie Chart in this case.

![qs41.png](./images/qs41.png)

## Congratulations! You now have learned how to:

* Setting up Redshift.

* Adding a Redshift to determine the underlying JDBC properties to create the connection.

* Visualize data on QuickSight with Redshift.

* You now have finished whole Lab and have learned how to do **Serverless ETL with AWS Glue & BI process with QuickSight**.
