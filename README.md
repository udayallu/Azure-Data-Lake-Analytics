# Azure-Data-Lake-Analytics
Azure includes a range of **Big Data technologies** that you can use to process data at **scale**. In this lab, you will explore two of these technologies; **Azure Data Lake Analytics**, and **Azure HDInsight**.

## Exercise 1: Processing Data with Azure Data Lake Analytics
In this exercise, you will create an **Azure Data Lake Analytics Account** and use it to **process data** in Azure Storage.

### Create an Azure Data Lake Analytics Account
Before you can use Azure Data Lake Analytics to process data, you must create an **Azure Data Lake Analytics** account, and associate it with at least one **Azure Data Lake store**. In this case, you will associate your **Azure Data Lake Analytics account** with the **Azure Data Lake Store** you created in the first lab of this course.

### Steps:
1. In a web browser, navigate to http://portal.azure.com, and if prompted, sign in using the Microsoft account that is associated with your Azure subscription.
2. In the Microsoft Azure portal, in the Hub Menu, click New. Then in the **Data + Analytics menu**, click **Data Lake Analytics**.
3. In the **New Data Lake Analytics Account** blade, enter the following settings, and then click Create:
- Name: Enter a unique name (and make a note of it!)
- Subscription: Select your Azure subscription
- Resource Group: Use the existing resource group that contains your Azure Data Lake Store
- Location: Select the region where your Azure Data Lake Store is located.
- Data Lake Store: Select your existing Azure Data Lake Store
- Pin to dashboard: Not selected
4. In the Azure portal, view Notifications to verify that deployment has started. Then wait for the resource to be deployed (this can take a few minutes.)
### Link an Azure Storage Account
- Azure Data Lake Analytics can access data in the Data Lake Store you associated it with.
- You can also link additional Data Lake Stores and Azure Storage accounts and process data that is stored in them.
- In this procedure, you will link your existing Azure Storage account to your Azure Data Lake Analytics account.

### Steps
1. Click All Resources, and then click the Azure Data Lake Analytics account you just provisioned.
2. In the blade for your Data Lake Analytics account, view the Data Sources page, and then click Add Data Source.
3. In the Add Data Source blade, enter the following details and click Add:
• **Storage Type**: Azure Storage
• **Selection Method**: Select Account
• **Azure Storage**: Select your Azure Storage account
4. On the **Data Sources page**, verify that both your Azure Data Lake Store and Azure Storage account are listed.

## Process Data

Azure Data Lake Analytics enables you to process data by writing **U-SQL** code. U-SQL is similar to **Transact-SQL**, but also includes support for C# syntax and data types.

1. In the blade for your Data Lake Analytics account, view the **Data Explorer page**.
2. Under **Storage accounts**, expand your **Azure Storage account** and its **bigdata container**, and select the **data folder**. This should contain the three files that you uploaded in the first lab of this course (customers.txt, products.txt, and reviews.txt).
3. Click **products.txt** to preview the data it **contains** (if a preview is not shown, you can download the file and open it locally). Note that this file contains a tab-delimited list of products. Note also that some of the product details contain the text “NULL” – this is used in the Color, Size, and Weight columns to indicate that there is no known value for this product.
4. In the **File Preview pane**, click **Properties** to view the properties of the file, and note the WASB PATH property, which is used to access this file in Azure Storage. The path is in the form **wasb://bigdata@<storageacct>/data/products.txt** (where <storageacct> is the name of your Azure storage account.
5. Return to the blade for your **Azure Data Lake Store**, and on the **Overview page**, click + New Job.
6. In the New U-SQL Job blade, change the Job Name to Process Products.
7. In the query pane, enter the following U-SQL code (you can copy and paste this from u-sql.txt in the lab folder) – replace <storageacct> with the name of your Azure storage account:
  ```
  @products = EXTRACT ProductID string,
ProductName string,
ProductNumber string,
Color string,
StandardCost decimal,
ListPrice decimal,
Size string,
NetWeight string
FROM "wasb://bigdata@<storageacct>/data/products.txt"
USING Extractors.Tsv(skipFirstNRows:1);
@cleanProducts = SELECT ProductID,
ProductName,
ProductNumber,
(Color == "NULL") ? "None" : Color AS Color,
StandardCost,
ListPrice,
ListPrice - StandardCost AS Markup,
(Size == "NULL") ? "N/A" : Size AS Size,
(NetWeight == "NULL") ? "0.00" : NetWeight AS NetWeight
FROM @products;
OUTPUT @cleanProducts
TO "output/cleanproducts.csv"
ORDER BY ProductID
USING Outputters.Csv(outputHeader:true);
  ```
8. Review the code in the query, noting that it:
- Loads the tab-delimited data from the text file into a table variable with appropriate columns, skipping the first row (which contains the column names).
- Cleans the data by changing NULL Color values to “None”, changing NULL Size values to “N/A”, and changing NULL Weight values to 0.00.
- Calculates the markup for each product by subtracting the cost from the list price.
- Stores the cleaned data in a comma-delimited file. 
9. Click **Submit Job** and observe the job details as it is run. After the job has been prepared, a job graph should be displayed, showing the steps used to execute it.
10. When the job has finished, on the **Output tab** and **cleanproducts.csv** to see a preview of the results.
11. Click Data Explorer and browse to the output folder in your Azure Data Lake Store – this where the cleanproducts.csv file was saved.

## Exercise 2: Processing Data with HDInsight
In this exercise, you will create an **Azure HDInsight cluster** and use it to process data in Azure Storage.
### Provision an HDInsight Cluster
1. In the Microsoft Azure portal, in the Hub Menu, click New. Then in the **Data + Analytics** menu, click **HDInsight**
  
