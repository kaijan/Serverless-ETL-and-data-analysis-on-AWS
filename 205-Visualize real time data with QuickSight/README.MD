
### Data visualization with QuickSight

Now we have finished data analysis with Athena and Redshift Spectrum<br><br>
You will learn how to use QuickSight (AWS BI service) to visualize the data<br><br>
•	First you need to register QuickSight<br><br>
**Please confirm the user have Administrator permission to sign up QuickSight.**<br><br>
•	Click **“Sign up for QuickSight”**<br><br>
![qs1.png](/images/qs1.png)<br>  
•	Select the subscription type<br><br>
![qs2.png](/images/qs2.png)<br>  
 
•	Provide requested information and setting then click on [Finish]<br><br>
![qs3.png](/images/qs3.png)<br>  
•	It takes a while to enable your account.<br><br>
![qs4.png](/images/qs4.png)<br>  
•	Finish register you will find the screen below<br><br>
![qs5.png](/images/qs5.png)<br>  
![qs6.png](/images/qs6.png)<br>  
![qs7.png](/images/qs7.png)<br>  
If you are new to QuickSight, the analyses board will be empty<br><br>
•	Now we can get start with **QuickSight**<br><br>
* 	Click **Manage data** and you will see this screen. If you were first time log in QuickSight console, there is no data sets record.<br><br>
![qs8.png](/images/qs8.png)<br>  
* 	Click **New data set** and you will find that many data sources<br><br>
![qs9.png](/images/qs9.png)<br>  
* 	Click on **Athena** and enter **“AthenaBI”** in **Data source name**, then click **Validate connection**<br><br>
![qs10.png](/images/qs10.png)<br>  
* 	Click **Create data source** and select **“my-data”** database. For tables, choose **yourname_etl_result** and click **Select**.<br><br>
![qs11.png](/images/qs11.png)<br>  
![qs12.png](/images/qs12.png)<br>  
* 	When loading finish click **Visualize**<br><br>
![qs13.png](/images/qs13.png)<br>  
![qs14.png](/images/qs14.png)<br>  
* 	Select a **Visual types (e.g., Vertical bar chart)**<br><br>
![qs15.png](/images/qs15.png)<br>  
* 	Drag the field items which below **Fields list** into **Field wells** and you can create different BI charts.<br><br>
![qs16.png](/images/qs16.png)<br>  
![qs17.png](/images/qs17.png)<br>  
![qs18.png](/images/qs18.png)<br>  
This chart analyze that whether views and comment impact on likes. The video **“Falcon Heavy Test Flight”** is one of the example that really affected.<br><br>

* 	You can analyze the channel type of the video by **yourname_etl_result2 bucket** using Athena (e.g., Entertainment)<br><br>
*	Create a **New data set** and choose Athena as data sources<br><br>
* 	Select database **“my-data”** and select the table **“yourname_etl_result2"**<br><br>
![qs19.png](/images/qs19.png)<br>  
*	Click **Select** and **Visualize**<br><br>
* 	Select table chart to display the title and id of category<br><br>
![qs20.png](/images/qs20.png)<br>  
You can query certain **category id** to do several analytics<br><br>
* 	Back to your QuickSight data sets page click **New data set**<br><br>
![qs21.png](/images/qs21.png)<br>  
* 	Choose **Athena** and enter data source name **“Entertainment”** <br><br>
* 	Select **yourname_etl_result** in **my-data** database and click **Edit / Preview data**<br><br>
![qs22.png](/images/qs22.png)<br>  
* 	In the navigation pane under **Tables**, select **Use SQL**<br><br>
![qs23.png](/images/qs23.png)<br>  
* 	Enter SQL name **“Entertainment”** and paste below code in Custom SQL field <br><br>

         Select * From "my-data"."yourname_etl_result" where category_id = 24;
         (category_id =24 for Entertainment category)

![qs24.png](/images/qs24.png)<br>  
* 	Click **Finish** then Click on **Save & visualize**<br><br>
* 	Then you can start customize your chart to do your BI strategy<br><br>
![qs25.png](/images/qs25.png)<br>  
This plot shows the factors that impact Entertainment video type<br><br>

Congratulations! You now have learned how to visualize data on Quicksight with Athena. **Next step we will visualize data on QuickSight with Redshift**.<br><br>
 
* 	Back to QuickSight home page and click **New analysis**.<br><br>
* 	Click **New data set** and select **Redshift (Manual connect)**<br><br>
![qs26.png](/images/qs26.png)<br>  
* 	Enter Data source name **RedshiftBI**<br><br>
* 	Enter **Database server** as your redshift cluster **Endpoint**<br>
(e.g.,  my-cluster.ceng4yniaba2.us-east-1.redshift.amazonaws.com)<br><br><br><br>
![qs27.png](/images/qs27.png)<br>  
* 	Enter Port **5439**<br><br>
* 	Enter **Database name “mydb”**<br><br>
* 	Enter your **Username** and **Password** of redshift cluster that you create before<br><br>
* 	Click **Validate connection**<br><br>
* 	Click **Create data source**<br><br>
![qs28.png](/images/qs28.png)<br>  
* 	Select **usvideos_csv** as table and click **Select**.<br><br>
![qs29.png](/images/qs29.png)<br>  
* 	Click **Visualize**.<br><br>
* 	Select a **Visual types** then drag the field items which below **Fields list** into **Field wells** and you can create different BI charts just like Athena data source.<br><br>
![qs30.png](/images/qs30.png)<br>  
![qs31.png](/images/qs31.png)<br>  
![qs32.png](/images/qs32.png)<br>  

Now we import the result of topic modeling data (**topic-terms.csv**) to integrate with QuickSight<br><br>
* 	Back to **QuickSight** console<br><br>
* 	Click **New analysis**<br><br>
* 	Click **New data set**<br><br>
* 	Click **Upload a file**<br><br>
![qs33.png](/images/qs33.png)<br>  
* 	Select **topic-terms.csv** and upload<br><br>
* 	Click **Next**<br><br>
![qs34.png](/images/qs34.png)<br>  
* 	Click **Visualize**<br><br>
![qs35.png](/images/qs35.png)<br>  
* 	Select a **Visual types** then drag the field items which below **Fields list** into **Field wells** and you can create different BI charts.<br><br>
* 	For example, **horizontal stacked bar chart** shows that sum of weight by term.<br><br>
We can know that the word **“president”** is often appears in US videos.<br><br>
![qs36.png](/images/qs36.png)<br>  
You can query certain trending topic of **title** to do several analytics<br>
(For example, video relate to **President**)<br><br>
* 	Back to your QuickSight data sets page click **New data set**<br><br>
![qs37.png](/images/qs37.png)<br>  
* 	Choose **Athena** and enter data source name **“President”** <br><br>
* 	Select **yourname_etl_result** in **my-data** database and click **Edit / Preview data**<br><br>
![qs38.png](/images/qs38.png)<br>  
* 	In the navigation pane under **Tables**, select **Use SQL**<br><br>
![qs39.png](/images/qs39.png)<br>  
* 	Enter SQL name **“President”** and paste below code in Custom SQL field <br>

         Select * From "my-data"."james_etl_result" where title like '%President%'; 
         (for the title that contain “President”)

![qs40.png](/images/qs40.png)<br>  
*	Click Finish then Click on **Save & visualize**<br><br>
* 	Then you can create several plots of **President title video**<br><br>
![qs41.png](/images/qs41.png)<br>  
Pie Chart example<br><br>
Congratulations! You now have finished whole Lab and have learned how to do **Serverless ETL with AWS Glue & BI process with QuickSight**.<br>

 


## Appendix


### Visualize data with Tableau

When it comes to BI, Tableau is one of the best tools.<br><br>
The following steps will integrate AWS services with Tableau. (Athena for this example)<br><br>
* 	First you need to download Tableau on premise or on EC2.<br><br>
Click below link to download<br><br>
Note that download the version higher than 10.3 (10.5 for this example)<br><br>
https://www.tableau.com/support/releases<br>
https://www.tableau.com/support/releases/server<br>
* 	Make sure that you have license to use Tableau<br><br>
https://www.tableau.com/pricing<br>
* 	Open Tableau you will see this screen (Tableau Desktop for example)<br><br>
![tableau1.png](/images/tableau1.png)<br>  
* 	To connect to Athena, click **Amazon Athena** in navigation pane left side <br><br>
![tableau2.png](/images/tableau2.png)<br>
* 	Enter **“Athena.us-east-1.amazonaws.com”** in **Server**<br><br>
* 	Enter **port** for 443<br><br>
* 	Enter Staging Directory for your **Athena query result S3 bucket**<br><br>
* 	Enter **Access Key ID** and **Secret Access Key** then click **sign in**<br><br>
![tableau3.png](/images/tableau3.png)<br> 
* 	Select **AwsDataCatalog** in **Catalog** and select **my-data** in **database**<br><br>
* 	Drag the table you want to make the chart for<br><br>
![tableau4.png](/images/tableau4.png)<br><br>
![tableau5.png](/images/tableau5.png)<br><br>
1.11. 	Click **New Worksheet** icon below then you can start making your chart to do BI
![tableau6.png](/images/tableau6.png)<br><br> 
![tableau7.png](/images/tableau7.png)<br><br>
![tableau8.png](/images/tableau8.png)<br><br>
