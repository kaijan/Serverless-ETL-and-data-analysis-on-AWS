# Visualize real time data with QuickSight

Now we have finished data analysis with Athena and Redshift.
You will learn how to use QuickSight (AWS BI service) to visualize the data.

## Data visualization with QuickSight

Amazon QuickSight is a business analytics service you can use to build visualizations, perform ad hoc analysis, and get business insights from your data. It can automatically discover AWS data sources and also works with your data sources. 

### Data visualization with QuickSight

* First you need to register QuickSight.

    * Please confirm the user have Administrator permission to sign up QuickSight.**

* Click **“Sign up for QuickSight”**

![qs1.png](/images/qs1.png)

* Select the subscription type

![qs2.png](/images/qs2.png)
 
* Provide requested information and setting then click on [Finish]

![qs3.png](/images/qs3.png)

* It takes a while to enable your account.

![qs4.png](/images/qs4.png)

* Finish register you will find the screen below

![qs5.png](/images/qs5.png)

![qs6.png](/images/qs6.png)

![qs7.png](/images/qs7.png)

If you are new to QuickSight, the analyses board will be empty

•	Now we can get start with **QuickSight**

* 	Click **Manage data** and you will see this screen. If you were first time log in QuickSight console, there is no data sets record.

![qs8.png](/images/qs8.png)

* 	Click **New data set** and you will find that many data sources

![qs9.png](/images/qs9.png)

* 	Click on **Athena** and enter **“AthenaBI”** in **Data source name**, then click **Validate connection**<br><br>
![qs10.png](/images/qs10.png)

* 	Click **Create data source** and select **“my-data”** database. For tables, choose **yourname_etl_result** and click **Select**.

![qs11.png](/images/qs11.png)

![qs12.png](/images/qs12.png)

* 	When loading finish click **Visualize**

![qs13.png](/images/qs13.png)

![qs14.png](/images/qs14.png)

* 	Select a **Visual types (e.g., Vertical bar chart)**

![qs15.png](/images/qs15.png)

* 	Drag the field items which below **Fields list** into **Field wells** and you can create different BI charts.

![qs16.png](/images/qs16.png)

![qs17.png](/images/qs17.png)

![qs18.png](/images/qs18.png)

This chart analyze that whether views and comment impact on likes. The video **“Falcon Heavy Test Flight”** is one of the example that really affected.

* 	You can analyze the channel type of the video by **yourname_etl_result2 bucket** using Athena (e.g., Entertainment)

*	Create a **New data set** and choose Athena as data sources

* 	Select database **“my-data”** and select the table **“yourname_etl_result2"**

![qs19.png](/images/qs19.png)

*	Click **Select** and **Visualize**

* 	Select table chart to display the title and id of category

![qs20.png](/images/qs20.png)

You can query certain **category id** to do several analytics

* 	Back to your QuickSight data sets page click **New data set**

![qs21.png](/images/qs21.png)

* 	Choose **Athena** and enter data source name **“Entertainment”** 

* 	Select **yourname_etl_result** in **my-data** database and click **Edit / Preview data**

![qs22.png](/images/qs22.png)

* 	In the navigation pane under **Tables**, select **Use SQL**

![qs23.png](/images/qs23.png)

* 	Enter SQL name **“Entertainment”** and paste below code in Custom SQL field

         Select * From "my-data"."yourname_etl_result" where category_id = 24;
         (category_id =24 for Entertainment category)

![qs24.png](/images/qs24.png)

* 	Click **Finish** then Click on **Save & visualize**

* 	Then you can start customize your chart to do your BI strategy

![qs25.png](/images/qs25.png)

This plot shows the factors that impact Entertainment video type

Congratulations! You now have learned how to visualize data on Quicksight with Athena. **Next step we will visualize data on QuickSight with Redshift**.
 
* 	Back to QuickSight home page and click **New analysis**.

* 	Click **New data set** and select **Redshift (Manual connect)**

![qs26.png](/images/qs26.png)

* 	Enter Data source name **RedshiftBI**

* 	Enter **Database server** as your redshift cluster **Endpoint**
(e.g.,  my-cluster.ceng4yniaba2.us-east-1.redshift.amazonaws.com)

![qs27.png](/images/qs27.png)

* 	Enter Port **5439**

* 	Enter **Database name “mydb”**

* 	Enter your **Username** and **Password** of redshift cluster that you create before<br><br>
* 	Click **Validate connection**

* 	Click **Create data source**

![qs28.png](/images/qs28.png)

* 	Select **usvideos_csv** as table and click **Select**.

![qs29.png](/images/qs29.png)

* 	Click **Visualize**.

* 	Select a **Visual types** then drag the field items which below **Fields list** into **Field wells** and you can create different BI charts just like Athena data source.

![qs30.png](/images/qs30.png)

![qs31.png](/images/qs31.png)

![qs32.png](/images/qs32.png)


Now we import the result of topic modeling data (**topic-terms.csv**) to integrate with QuickSight

* 	Back to **QuickSight** console

* 	Click **New analysis**

* 	Click **New data set**

* 	Click **Upload a file**

![qs33.png](/images/qs33.png)

* 	Select **topic-terms.csv** and upload

* 	Click **Next**

![qs34.png](/images/qs34.png)

* 	Click **Visualize**

![qs35.png](/images/qs35.png)

* 	Select a **Visual types** then drag the field items which below **Fields list** into **Field wells** and you can create different BI charts.

* 	For example, **horizontal stacked bar chart** shows that sum of weight by term.

We can know that the word **“president”** is often appears in US videos.

![qs36.png](/images/qs36.png)

You can query certain trending topic of **title** to do several analytics

(For example, video relate to **President**)

* 	Back to your QuickSight data sets page click **New data set**

![qs37.png](/images/qs37.png)

* 	Choose **Athena** and enter data source name **“President”** 

* 	Select **yourname_etl_result** in **my-data** database and click **Edit / Preview data**

![qs38.png](/images/qs38.png)

* 	In the navigation pane under **Tables**, select **Use SQL**

![qs39.png](/images/qs39.png)

* 	Enter SQL name **“President”** and paste below code in Custom SQL field

         Select * From "my-data"."james_etl_result" where title like '%President%'; 
         (for the title that contain “President”)

![qs40.png](/images/qs40.png)

*	Click Finish then Click on **Save & visualize**

* 	Then you can create several plots of **President title video**

![qs41.png](/images/qs41.png)

Pie Chart example

Congratulations! You now have finished whole Lab and have learned how to do **Serverless ETL with AWS Glue & BI process with QuickSight**.



## Appendix


### Visualize data with Tableau

When it comes to BI, Tableau is one of the best tools.

The following steps will integrate AWS services with Tableau. (Athena for this example)

* 	First you need to download Tableau on premise or on EC2.

Click below link to download

Note that download the version higher than 10.3 (10.5 for this example)

https://www.tableau.com/support/releases

https://www.tableau.com/support/releases/server

* 	Make sure that you have license to use Tableau

https://www.tableau.com/pricing

* 	Open Tableau you will see this screen (Tableau Desktop for example)

![tableau1.png](/images/tableau1.png)

* 	To connect to Athena, click **Amazon Athena** in navigation pane left side

![tableau2.png](/images/tableau2.png)

* 	Enter **“Athena.us-east-1.amazonaws.com”** in **Server**

* 	Enter **port** for 443

* 	Enter Staging Directory for your **Athena query result S3 bucket**

* 	Enter **Access Key ID** and **Secret Access Key** then click **sign in**

![tableau3.png](/images/tableau3.png)

* 	Select **AwsDataCatalog** in **Catalog** and select **my-data** in **database**

* 	Drag the table you want to make the chart for

![tableau4.png](/images/tableau4.png)

![tableau5.png](/images/tableau5.png)

1.11. 	Click **New Worksheet** icon below then you can start making your chart to do BI
![tableau6.png](/images/tableau6.png)

![tableau7.png](/images/tableau7.png)

![tableau8.png](/images/tableau8.png)

