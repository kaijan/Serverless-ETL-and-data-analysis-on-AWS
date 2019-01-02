# Analyze and visualize the data with Athena and QuickSight

You use Amazon Comprehend to examine and analyze a document to determine common themes.

Next, we will use Athena to query the data in an easy way, and use Amazon BI tool - QuickSight, it is a business analytics service you can use to build visualizations, perform ad hoc analysis, and get business insights from your data. It can automatically discover AWS data sources and also works with your data sources.


## Prerequisites

* Make sure the region is **US East (N. Virginia)**, which its short name is **us-east-1**.


## Start sorting data by using Athena

Amazon Athena is an interactive query service that makes it easy to analyze data directly in Amazon S3 using standard SQL. It is a serverless service and does not need any infrastructure to create, manage, or scale data sets. It works directly on top of Amazon S3 data sets. It creates external tables and therefore does not manipulate S3 data sources, working as a read-only service from an S3 perspective. 

* On the **Services** menu, click **Athena**.

* Choose **Get Started** to open the Query Editor while your first time visiting the Athena console. If it isn't your first time, the Athena Query Editor opens.

* On the **Query Editor** tab, choose the database **my-data**.

![athena1.png](./images/athena1.png)

* Choose the **yourname_etl_result** table.

* Query the data, type below standard SQL:

    **Note:** remember to change table name, for example, *Select * From "my-data"."james_etl_result" limit 100;*

         Select * From "my-data"."yourname_etl_result" limit 100;
        	
* Click **Run Query** and results are returned that look like the following:

![athena2.png](./images/athena2.png)

* Choose the **yourname_etl_result2** table.

* Query the data, type below standard SQL:

    **Note:** remember to change table name, for example, *Select * From "my-data"."james_etl_result2";*

         Select * From "my-data"."yourname_etl_result2";

* Click **Run Query** and results are returned that look like the following:

 ![athena3.png](./images/athena3.png)
 
