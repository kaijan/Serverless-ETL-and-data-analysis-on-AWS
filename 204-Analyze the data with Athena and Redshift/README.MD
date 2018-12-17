# Analyze the data with Athena and Redshift

You use Amazon Comprehend to examine and analyze a document to determine common themes.

Next, we will use Athena to query the data in an easy way with data catalog of Glue, and use Redshift to do data analysis that often processing structural data query job for long term workflow. Both of them can be used for data analysis, choose one suitable for your application.

## Start sorting data by using Athena

Amazon Athena is an interactive query service that makes it easy to analyze data directly in Amazon S3 using standard SQL. It is a serverless service and does not need any infrastructure to create, manage, or scale data sets. It works directly on top of Amazon S3 data sets. It creates external tables and therefore does not manipulate S3 data sources, working as a read-only service from an S3 perspective. 

* On the **Services** menu, click **Athena**.

* Choose **Get Started** to open the Query Editor while your first time visiting the Athena console. If it isn't your first time, the Athena Query Editor opens.

* On the **Query Editor** tab, choose the database **my-data**.

![athena1.png](/images/athena1.png)

* Choose the **yourname_etl_result** table.

* Query the data, type below standard SQL:

    **Note:** remember to change table name, for example, *Select * From "my-data"."james_etl_result" limit 100;*

         Select * From "my-data"."yourname_etl_result" limit 100;
        	
* Click **Run Query** and results are returned that look like the following:

![athena2.png](/images/athena2.png)

* Choose the **yourname_etl_result2** table.

* Query the data, type below standard SQL:

    **Note:** remember to change table name, for example, *Select * From "my-data"."james_etl_result2";*

         Select * From "my-data"."yourname_etl_result2";

* Click **Run Query** and results are returned that look like the following:

 ![athena3.png](/images/athena3.png)
 
 
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

![redshift1.png](/images/redshift1.png)

* Click **Create VPC**.

* In the navigation pane, choose **Security Groups**.

* Select the Security Group that attach on **“Redshift-VPC”** which group name is **default** then select **Inbound Rules**.

* Click **Edit**.

*Click **Add another rule** below, **Type** for **ALL Traffic**, Source for **0.0.0.0/0**.

* Click **Save**.

![redshift2.png](/images/redshift2.png)

### Setting up Redshift

* On the **Services** menu, click **Amazon Redshift**.

* In the navigation pane, choose **Security**.

* Select **Subnet Groups** and click **Create Cluster Subnet Group**.

* Enter the Name: **redshift-sg**.

* Enter the Description: **SG for redshift**.

* Select the **VPC ID** (vpc-xxxxxxxx) same as **Redshift-VPC** that you create before.

* Click **add all the subnets** then click **Create**.

![redshift3.png](/images/redshift3.png)

* In the navigation pane, choose **Clusters**.

* Click **Launch cluster**

* Enter Cluster identifier: **my-cluster**

* Enter Database name: **mydb**

* Leave **Database port** for **5439**

* Enter your own **Master user name** and **Master user password** and type again your password in **Confirm password** then click **Continue**. (e.g., Master user name: james, Master user password: James123)

* Select **dc2.large** for the **Node type** which is the cheapest cluster.

* Click **Continue**.

* Select VPC ID of **Redshift-VPC** in **Choose a VPC** blank.

* In **Available roles** choose **SpectrumRole** then click **Continue**.

* After examine that all setting is correct, click **Launch cluster**.

* It will need 10-15 minutes passed before the cluster was ready to use.
    
![redshift4.png](/images/redshift4.png)

### Connect to AWS Glue

* On the **Services** menu, click **AWS Glue**.

* In the navigation pane, choose **Connections**.

* Click **Add connection**.

* Enter Connection name: **redshift-spectrum**.

* Select Connection type **Amazon Redshift**.

* choose **Require SSL connection**, and click **next**.

* 	Select **my-cluster** in **Cluster** blank.

* Enter Database name: **mydb**.

* Enter your own **Username** and **Password** then click **Next**.

* Click **Finish**.

* Select **redshift-spectrum** and click **Test connection**.

* Select **AWSGlueServiceRoleDefault** as **IAM role**, and click **Test connection**. You will find below screen after testing.

![redshift5.png](/images/redshift5.png)

![redshift6.png](/images/redshift6.png)

* On the **Services** menu, click **AWS Glue**.

* In the navigation pane, choose **Jobs**.

* Click **Add job**.

* Enter the **Name “redshift-query”**.

* Select **AWSGlueServiceRoleDefault** as **IAM role** and click **Next**.

* Select **“usvideos_csv”** and click **Next**.

![redshift7.png](/images/redshift7.png)

* Choose **Create tables in your data target**.

* Select **Data store** as **JDBC**.

* Select **redshift-spectrum** for **Connection** and enter the **Database name “mydb”** then click **Next**.

* 	Click **Next** you will find this screen below.

![redshift8.png](/images/redshift8.png)

* 	Click **finish**.

* 	View the job. This screen provides a complete view of the job and allows you to edit, click **Save**, and choose **Run job**. This steps may be waiting around 10 minutes.

    * In this job, AWS Glue send data to Redshift cluster and processing data by the cluster.

![redshift9.png](/images/redshift9.png)

## Congratulations! You now have learned how to:

* Sorting data by using Athena.

* Setting up Redshift.

* Adding a Redshift to determine the underlying JDBC properties to create the connection.

### Now you are ready to [Visualize real time data with QuickSight](https://github.com/ecloudvalley/Serverless-ETL-and-data-analysis-on-AWS/tree/master/205-Visualize%20real%20time%20data%20with%20QuickSight)
