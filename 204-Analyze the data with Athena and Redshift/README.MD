U

### Analyze the data with Athena & Redshift Spectrum

Athena can query the data in an easy way with data catalog of Glue<br><br>
* 	On the **Services** menu, click **Athena**.<br><br>
* 	On the **Query Editor** tab, choose the database **my-data**.<br><br>
![athena1.png](/images/athena1.png)<br> 
* 	Choose the **yourname_etl_result** table.<br><br>
* 	Query the data, type below standard SQL:<br><br>

         Select * From "my-data"."yourname_etl_result" limit 100;
         (e.g., Select * From "my-data"."james_etl_result" limit 100;)

* 	Click **Run Query** and Athena will query data as the below screen<br><br>
![athena2.png](/images/athena2.png)<br>  
* 	Choose the **yourname_etl_result2** table.<br><br>
* 	Query the data, type below standard SQL:<br><br>

         Select * From "my-data"."yourname_etl_result2";
         (e.g., Select * From "my-data"."james_etl_result2";)

Click **Run Query** and Athena will query data as the below screen<br><br>
 ![athena3.png](/images/athena3.png)<br> 
 
After finish analyzing data in Athena, get start with data analysis with **Redshift Spectrum**. **Different from Athena, Redshift is suitable for long term workflow of data analysis that often processing structural data query job**.<br><br>
*	First setup VPC in which you want to create your cluster<br><br>
*	Launch Redshift cluster and connect to AWS Glue<br><br>
* 	On the **Services** menu, click **VPC**.<br><br>
* 	Click **Start VPC Wizard**.<br><br>
* 	In this workshop we simply choose **VPC with a single Public Subnet**<br><br>
* 	Enter your VPC name **“Redshift-VPC”**.<br><br>
* 	Click **Add Endpoint** below **Service endpoints** and select **com.amazonaws.us-east-1.s3** as service.<br><br>
![redshift1.png](/images/redshift1.png)<br> 
* 	Click **Create VPC**.<br><br>
* 	In the navigation pane, choose **Security Groups**.<br><br>
* 	Select the Security Group that attach on **“Redshift-VPC”** which group name is **default** then select **Inbound Rules**.<br><br>
* 	Click **Edit**<br><br>
*	Click **Add another rule** below, **Type** for **ALL Traffic**, Source for **0.0.0.0/0**.<br><br><br><br>
* 	Click **Save**.<br><br>
![redshift2.png](/images/redshift2.png)<br>  
* 	On the **Services** menu, click **Amazon Redshift**.<br><br>
* 	In the navigation pane, choose **Security**.<br><br>
* 	Select **Subnet Groups** and click **Create Cluster Subnet Group**.<br><br>
*	Enter the **Name “redshift-sg”**.<br><br>
* 	Enter the **Description “SG for redshift”**.<br><br>
* 	Select the **VPC ID** (vpc-xxxxxxxx)same as **Redshift-VPC** that you create before.<br><br>
* 	Click **add all the subnets** then click **Create**.<br><br>
![redshift3.png](/images/redshift3.png)<br>  
* 	In the navigation pane, choose **Clusters**.<br><br>
* 	Click **Launch cluster**<br><br>
* 	Enter **Cluster identifier “my-cluster”**<br><br>
* 	Enter **Database name “mydb”**<br><br>
* 	Leave **Database port** for **5439**<br><br>
* 	Enter your own **Master user name** and **Master user password** and type again your password in **Confirm password** then click **Continue**. (e.g., Master user name: james, Master user password: James123)<br><br>
* 	Select **dc2.large** for the **Node type** which is the cheapest cluster.<br><br>
* 	Click **Continue**.<br><br>
* 	Select VPC ID of **“Redshift-VPC”** in **Choose a VPC** blank.<br><br>
* 	In **Available roles** choose **SpectrumRole** then click **Continue**.<br><br>
* 	After examine that all setting is correct, click **Launch cluster**.<br>
(**This instance will charged $0.25 hourly**)<br>
Detail pricing issues https://aws.amazon.com/tw/redshift/pricing/#<br>
    At launching time, cluster creation times averaged 15 minutes<br><br>
![redshift4.png](/images/redshift4.png)<br>  
* 	On the **Services** menu, click **AWS Glue**.<br><br>
* 	In the navigation pane, choose **Connections**.<br><br>
* 	Click **Add connection**.<br><br>
* 	Enter **Connection name “redshift-spectrum”**.<br><br>
* 	Select **Connection type “Amazon Redshift”** and click **next**.<br><br>
* 	Select **my-cluster** in **Cluster** blank.<br><br>
* 	Enter **Database name “mydb”**.<br><br>
* 	Enter your own **Username** and **Password** then click **Next**.<br><br>
* 	Click **Finish**.<br><br>
* 	Select **redshift-spectrum** and click **Test connection**.<br><br>
* 	Select **AWSGlueServiceRoleDefault** as **IAM role** and click **Test connection**. You will find below screen after testing.<br><br>
![redshift5.png](/images/redshift5.png)<br>  
![redshift6.png](/images/redshift6.png)<br>  
* 	On the **Services** menu, click **AWS Glue**.<br><br>
* 	In the navigation pane, choose **Jobs**.<br><br>
*.	Click **Add job**.<br><br>
* 	Enter the **Name “redshift-query”**.<br><br>
* 	Select **AWSGlueServiceRoleDefault** as **IAM role** and click **Next**.<br><br>
* 	Select **“usvideos_csv”** and click **Next**.<br><br>
![redshift7.png](/images/redshift7.png)<br>  
* 	Choose **Create tables in your data target**.<br><br>
* 	Select **Data store** as **JDBC**.<br><br>
* 	Select **redshift-spectrum** for **Connection** and enter the **Database name “mydb”** then click **Next**.<br><br>
* 	Click **Next** you will find this screen below.<br><br>
![redshift8.png](/images/redshift8.png)<br>  
* 	Click **finish**.<br><br>
* 	View the job. This screen provides a complete view of the job and allows you to edit, click **Save**, and choose **Run job**. This steps may be waiting around 10 minutes.<br><br>
In this job, Glue send data to Redshift cluster and processing data by the cluster.<br><br>
![redshift9.png](/images/redshift9.png)<br> 

