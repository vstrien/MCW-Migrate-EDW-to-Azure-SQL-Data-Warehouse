![](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/master/Media/ms-cloud-workshop.png "Microsoft Cloud Workshops")


<div class="MCWHeader1">
Migrate EDW to Azure SQL Data Warehouse
</div>

<div class="MCWHeader2">
Hands-on lab step-by-step
</div>

<div class="MCWHeader3">
September 2018
</div>


Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.

© 2018 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at <https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx> are trademarks of the Microsoft group of companies. All other trademarks are property of their respective owners

**Contents**
<!-- TOC -->

- [Migrate EDW to Azure SQL Data Warehouse hands-on lab step-by-step](#migrate-edw-to-azure-sql-data-warehouse-hands-on-lab-step-by-step)
  - [Abstract and learning objectives](#abstract-and-learning-objectives)
  - [Overview](#overview)
  - [Solution architecture](#solution-architecture)
  - [Requirements](#requirements)
  - [Exercise 1: Configure Azure Services](#exercise-1-configure-azure-services)
    - [Task 1: Create a logical SQL Server to host SSISDB](#task-1-create-a-logical-sql-server-to-host-ssisdb)
    - [Task 2: Create an Azure Data Factory v2](#task-2-create-an-azure-data-factory-v2)
    - [Task 3: Create an Azure SQL Data Warehouse](#task-3-create-an-azure-sql-data-warehouse)
    - [Task 4: Create an Azure Storage Account](#task-4-create-an-azure-storage-account)
    - [Task 5: Create Analysis Services](#task-5-create-analysis-services)
    - [Task 6: Prepare Environment and Create Migration Accounts](#task-6-prepare-environment-and-create-migration-accounts)
  - [Exercise 2: Data and schema preparation](#exercise-2-data-and-schema-preparation)
    - [Task 1: Validate schema and data](#task-1-validate-schema-and-data)
    - [Task 2: Prepare Azure SQL Data Warehouse and migrate schema](#task-2-prepare-azure-sql-data-warehouse-and-migrate-schema)
  - [Exercise 3: Migrate the data to Azure SQL Data Warehouse](#exercise-3-migrate-the-data-to-azure-sql-data-warehouse)
    - [Task 1: Exporting the data from your current data warehouse](#task-1-exporting-the-data-from-your-current-data-warehouse)
    - [Task 2: Transfer your data to Azure](#task-2-transfer-your-data-to-azure)
  - [Exercise 4: Migrate an SSIS Package to Data Factory v2](#exercise-4-migrate-an-ssis-package-to-data-factory-v2)
    - [Task 1: Deploy SSIS Package to Data Factory](#task-1-deploy-ssis-package-to-data-factory)
    - [Task 2: Schedule the SSIS Package](#task-2-schedule-the-ssis-package)
  - [Exercise 5: Create an Analysis Services Model](#exercise-5-create-an-analysis-services-model)
    - [Task 1: Configure Analysis Services backup](#task-1-configure-analysis-services-backup)
    - [Task 2: Restore Analysis Services backup](#task-2-restore-analysis-services-backup)
    - [Task 3: Update Analysis Services connections](#task-3-update-analysis-services-connections)
  - [Exercise 6: Visualize data with Power BI Desktop](#exercise-6-visualize-data-with-power-bi-desktop)
    - [Task 1: Install Power BI Desktop](#task-1-install-power-bi-desktop)
    - [Task 2: Query data with Power BI Desktop](#task-2-query-data-with-power-bi-desktop)
  - [After the hands-on Lab](#after-the-hands-on-lab)
    - [Task 1: Cleanup resource groups](#task-1-cleanup-resource-groups)

<!-- /TOC -->

# Migrate EDW to Azure SQL Data Warehouse hands-on lab step-by-step

## Abstract and learning objectives

In this hands-on lab you will migrate an existing on-premises enterprise data warehouse to the cloud. You will investigate the current data warehouse to identify any incompatibilities, export the data from the on-premises data warehouse, and transfer it to an Azure Blob Storage. You will then load the data into the warehouse using Polybase. Finally, you will integrate the warehouse by migrating ETL to Azure Data Factory and supporting ad-hoc access by implementing Azure Analysis Services. 

At the end of this hands-on lab, you will be better able to plan and implement a migration of your existing on-premises enterprise data warehouse to Azure SQL Data Warehouse and integrating it with both cloud-based and on-premises services and data sources.

## Overview

Coho, a retail company focusing on consumer electronics, is modernizing their data architecture. Critical to this effort is migrating their existing enterprise data warehouse to the cloud for better integration with their cloud native customer 360 project and self-service business intelligence for their people in the field.

## Solution architecture

![An image of the solution architecture that depicts a simulated on-premises environment hosting SQL Server with paths for data to be uploaded to Azure Blob Storage and schema transferred to Azure SQL Datawarehouse. Finally, data is shown transferred to a computer running Power BI.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image2.png "Solution architecture that depicts a simulated on-premises environment hosting SQL Server with paths for data to be uploaded to Azure Blob Storage and schema transferred to Azure SQL Datawarehouse. Finally, data is shown transferred to a computer running Power BI")

## Requirements

1.  Microsoft Azure subscription
2.  Complete the Before the hands-on lab setup guide to deploy the \"on-premises\" environment.

## Exercise 1: Configure Azure Services

In this exercise, you will create and configure an Azure Storage Account, Azure SQL Data Warehouse, Azure Analysis Services and Azure Data Factory V2. Using these services you migrate your existing data warehouse and sample workloads to Azure.

### Task 1: Create a logical SQL Server to host SSISDB

1.  Browse to the Azure Portal and authenticate at <https://portal.azure.com/>.

2.  Click **+Create a resource** and type **Logical SQL Server** in the search box. Choose **SQL server (logical server)** from the results.

    ![Screenshot of choosing SQL server logical server from the Azure Marketplace](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image14.png)

3.  Click **Create** on the SQL Server blade. Specify the following information, and click **Create**:

    -   Server name: **Specify a unique name**.

    -   Server admin login: **demouser**

    -   Password: **Demo\@pass123**

    -   Subscription: ***Your subscription***

    -   Resource group: **Create new -** **CohoCloud**

    -   Location: ***Choose a region near you from the following regions which support the SSIS Integration Runtime***:

        -   ***East US***
        -   ***East US 2***
        -   ***West US***
        -   ***West US 2***
        -   ***Central US***
        -   ***South Central US***
        -   ***West Central US***
        -   ***Canada Central***
        -   ***North Europe***
        -   ***West Europe***
        -   ***UK South***
        -   ***Southeast Asia***
        -   ***Japan East***
        -   ***Central India***
        -   ***Australia East***
        -   ***Australia Southeast***

        You can find the current list of available regions here: https://azure.microsoft.com/en-us/global-infrastructure/services/?products=data-factory&regions=all 

        ![Screenshot of the configuration options for the SQL server logical server](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/2019-01-22-10-58-07.png "SQL Server configuration options")


### Task 2: Create an Azure Data Factory v2

1.  Browse to the Azure Portal and authenticate at <https://portal.azure.com/>.

2.  Click **+Create a resource** and type **Data Factory** in the search box. Choose **Data Factory** from the results.

    ![Screenshot of choosing Data Factory from the Azure Marketplace.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image16.png)

3.  Click **Create** on the Data Factory blade. Specify the following information, and click **Create**:

    -   Name: ***Specify a unique name***.

    -   Subscription: ***Your subscription***.

    -   Resource group: **Use existing** - **CohoCloud**

    -   Version: **V2**

    -   Location: ***Location near you***

    The Data Factory location is where the metadata of the data factory is stored and where the triggering of the pipeline is initiated from. Meanwhile, a data factory can access data stores and compute services in other Azure regions to move data between data stores or process data using compute services. This behavior is realized through the globally available Integration Runtime to ensure data compliance, efficiency, and reduced network egress costs.

    ![Screenshot of the configuration options for the new Data Factory](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/2019-01-22-11-27-31.png "Specify Data Factory configuration options")


4.  After the Data Factory deployment completes, navigate to the Data Factory and click the Author & Monitor tile.

    ![Screenshot of the Data Factory Author and Monitor tile in the Azure Portal](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/2019-01-22-11-30-46.png "Select the Author and Monitor tile from the quick links")

5.  Select the **Configure SSIS Integration Runtime**.

    ![Screenshot of the Configure SSIS Integration Runtime tile](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image19.png "Choose the Configure SSIS Integration Runtime tile and then complete for following information")

6.  On the first Integration Runtime Setup window, select the following options and then click **Next**:

    -   Name: **Azure-SSIS**

    -   Location: ***The same location you created your logical SQL Server***.

    -   Node size: **Standard\_D1\_v2 (1 Core(s), 3584MB)**

    -   Node Number: **1**

    -   Edition: **Standard**

    The Integration Runtime Location defines the location of its back-end compute, and essentially the location where the data movement, activity dispatching, and SSIS package execution are performed. The Integration Runtime location can be different from the location of the Data Factory it belongs to. For your location, select the location of your integration runtime. Only supported locations are displayed. We recommend that you select the same location as your SQL Server logical server to host SSISDB.

    ![Screenshot of the Integration Runtime Setup](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image20.png "Integration Runtime Setup. Select the name,location, note size an node number with other configuration options")

7.  On the second Integration Runtime Setup window, select the following options, click **Test Connection** and then click **Next**:

    -   Subscription: ***Your subscription***.

    -   Location: ***Same location you created your logical SQL Server in***.

    -   Catalog Database Server Endpoint: ***\<your logical sql server name\>.database.windows.net***

    -   Admin Username: **demouser**

    -   Admin Password: **Demo\@pass123**

    -   Catalog Database Service Tier: **S1**

    ![Screenshot of the Integration Runtime Setup](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image21.png "Integration Runtime Setup for SQL Database settings")

8.  On the final Integration Runtime Setup window, set **Maximum Parallel Executions Per Node = 1**, then click **Finish**.

    ![Screenshot of the Integration Runtime Setup](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image22.png "Mention the parallel execution per node in the Integration Runtime Setup options")

9.  The Integration Runtime can take 20-30 minutes to deploy and start. You do not need to wait on it to complete and may continue with the lab. The status will change from Starting to Running when it is complete.

    ![Connections tab showing new Integration Runtime status as starting.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image23.png)

### Task 3: Create an Azure SQL Data Warehouse

1.  Click **+Create a resource** and type **SQL Data Warehouse** in the search box. Choose **SQL Data Warehouse** from the results.

    ![Screenshot of the Azure Marketplace with SQL Data Warehouse selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image29.png)

2.  Click **Create** on the SQL Data Warehouse blade. Specify the following information. Then, click the **Server** tile.

    -   Name: **CohoDW**
    -   Resource group: **CohoCloud**
    -   Select source: **Blank database**                                             
    
        ![](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/2019-01-22-11-51-04.png)

3.  On the Server blade, select **Create a new server**. Specify the following options, and click **Select**:

    -   Server name: ***Choose a unique server name**.
    -   Server admin login: **demouser**
    -   Password: **Demo\@pass123**
    -   Location: **Same location you created your logical SQL Server in**.
    -   Allow azure services: **checked** 

        ![Screenshot of the SQL Data Warehouse settings blade](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image30.png "SQL Data Warehouse configuration settings for new data warehouse")

4.  Select the Performance level tile, select the **Gen2** tab and set the performance to **400 cDWU,** and click **Apply**.

    ![In the Configure Performance blade, Gen2 tab is selected, the Scale your system sliding scale is at cDW400.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/2019-01-22-11-58-40.png "Configure performance In the Configure Performance blade, The Scale your system sliding scale is at DW400")

5.  On the SQL Data Warehouse blade, click **Create**.

### Task 4: Create an Azure Storage Account

1.  Browse to the Azure Portal and authenticate at <https://portal.azure.com/>.

2.  Click **+Create a resource** and type **Storage account** in the search box. Choose **Storage account** from the results.

    ![Select Storage account from the Azure Marketplace.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image24.png)

3.  Click **Create** on the Storage account blade. Specify the following information, and click **Review + create**:

    -   Resource group: **Use existing** - **CohoCloud**

    -   Storage account name: ***specify a unique DNS name***.

    -   Location: ***Same location you created your logical SQL Server in***.

    -   Performance: **Standard**

    -   Account kind: **StorageV2 (general purpose v2)**

    -   Replication: **Locally-redundant storage (LRS)**

    -   Access tier: **Hot**    

        ![Screenshot of the storage account settings blade](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/2019-01-22-12-05-21.png "Creating the storage account")

4. On the Review + create tab, verify your configuration choices and select **Create**.

5.  Navigate to the new storage account, and click **Blobs**.

    ![In the Azure Portal, in the left pane, Overview is selected. In the Essentials section, under Services, Blobs is circled.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image26.png)

6.  On the Blob service blade, click the **+Container** button.

    ![The container button is circled on the Blob Service blade.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image27.png)

7.  On the New container blade type **migration** for the name. Then, click **OK**.

    ![In the Blob Service blade, Migration is typed in the Name field. Public access level is set to Private.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image28.png)

### Task 5: Create Analysis Services

The first four steps of this task walk you through creating an organizational account to use as the administrator account for Analysis Services. This account must be an organizational account, it cannot be a Microsoft live account. If you are doing this lab with an existing organizational account you may skip the first four steps of this task and use your organizational account in place of the asadmin account.

1.  From the Azure Portal, open Azure Active Directory.

    ![Screenshot of the Azure Active Directory button.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image33.png)

2.  Click **Users** from the menu.

    ![Screenshot of the Users button.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image34.png)

3.  Click the **+New user** button, and create a user using the following configuration:

    -   Name: **asadmin**

    -   User name: ***asadmin@\<your-domain\>.com***

    -   Password: *Check the **Show password** box, then **copy** the password into Notepad*.

    >**Note**: The User name setting should be in the form \<name\>@\<your-domain\>.com. If you do not know your domain name you can get it by hovering over your login information in the upper right corner of your browser window.

4.  Click the **Create** button.

5.  Open an incognito or in-private browser window, navigate to https://portal.azure.com and login with your new asadmin account. You will be prompeted to change your password. Change your password. Make sure to note your new password then close the browser window.

6.  Click **+Create a resource** and type **Analysis Services** in the search box. Choose **Analysis Services** from the results.

    ![Select Analysis Services from the Azure Marketplace.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image35.png)

7.  Click **Create** on the Analysis Services information blade.

8.  Use the following configurations then click **Create**:

    -   Server name: ***Choose a unique name***.

    -   Subscription: ***Choose your subscription***.

    -   Resource group: **CohoCloud**

    -   Location: ***Same location you created your logical SQL Server in***.

    -   Pricing tier: **S0 Standard**

    -   Administrator: **Select the asadmin account you created earlier**.

    -   Backup Storage Settings: **Not configured**

    -   Storage key expiration: **Never**


        ![Configure Analysis Services](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/2019-01-22-12-17-06.png "Specifying analysis services with other configurable options")

### Task 6: Prepare Environment and Create Migration Accounts

1.  In the Azure Portal navigate to your **CohoCloud** resource group, and click on your storage account.

2.  In the Storage account blade, and under settings, click on **Access keys**.

    ![In the Storage account blade, under Settings, Access keys is selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image36.png "Choose access keys from the menu of the storage account")

3.  Copy the **storage account name** and access **key1**. and paste into notepad for later use.

    ![In the Access keys blade, The Storage account name field is edwstor0. Both the name and the copy button are circled. Under Name, the key1 key and its copy button are circled.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image37.png)

4.  From the **SQLCohoDW** virtual machine that you created before the lab, open a browser window, and connect to the **Azure Portal**.

    > **Note**: If you do not have a SQLCohoDW virtual machine, you should verify that you have completed the pre-requite steps in the correct subscription.  

5.  Navigate to your **CohoCloud** resource group. Then, select the logical SQL Server that hosts your Azure SQL Data Warehouse.

    ![In the Resource group name section, the logical SQL Server is selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image38.png)

6.  In the security menu on the left, select **Firewall and Virtual Networks**.

7.  In the cohodw - Firewall blade, click the **+Add client IP** button. Then, click the **Save** button.

    ![In the Firewall settings blade, the Add client IP and the Save buttons are circled.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image39.png)

8.  Back in the **CohoCloud** resource group, select the **CohoDW** data warehouse, and copy the server name.

    ![In the SQL Database blade, in the Essentials section, the server name is selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/2019-01-22-14-05-22.png)

9.  Next, we want to create a special account to perform our data load operations. This account will be added to the larger resource class. By default, all accounts are initially in the smallrc resource class. Adding the account to the largerc resource class allows the account to consume more memory during query execution which will be more efficient for operations such as data loads and maintenance tasks.

    Open the **C:\\LabFiles\\CreateDataLoader.sql** script in SQL Server Management Studio.

10. Connect to your Azure SQL Data Warehouse using SQL Server Authentication and the **demouser** account and password **Demo\@pass123**.

    ![The Connect to Server dialog box displays.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image41.png)

11. Verify you are connected to the **master** database.

    ![In the SQL Server window, master is selected in the database drop-down menu.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image42.png)

12. Highlight the first two commands of the script, and click the **Execute** button.

    ![In the SQL Server window, in the Script pane, the Create Login and Create User commands are selected. On the SQL Server command bar, the Execute button is selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image43.png)

13. Change the database context to **cohoDW**.

    ![In the SQL Server window, the database drop-down field is now set to cohoDW.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image44.png)

14. Highlight the remaining lines of the script, and click the **Execute** button.

    ![In the SQL Server window, the database drop-down field is still set to cohoDW, and the remaining lines of script - Create User, Grant Control on Database, and Exec, are circled.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image45.png)

## Exercise 2: Data and schema preparation

Coho is relying on you to migrate the data warehouse to Azure SQL Data Warehouse. One of the most important steps is preparing the data and schema. During this phase, you will need to verify compatibility of the schema and data, and make any necessary changes required for a successful migration.

### Task 1: Validate schema and data

1.  In the Azure portal, navigate to your **CohoOnPremEnvironment** resource group, then connect to the **SQLCohoDW** virtual machine. If you are still connected you may use the same connection.

2.  Launch SQL Server Management Studio, connect to the local **SQLCohoDW** instance with the demouser account and the Demo@pass123 password then open a **New Query** window.

    ![Screenshot of the New Query button.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image46.png)

3.  Run the following query to check for data incompatibility and potential data length issues:

    ```
    USE CohoDW
    GO
    SELECT t.[name] as [Table], 
           c.[name] as [Column], 
           c.[system_type_id], 
           c.[user_type_id], 
           y.[is_user_defined], 
           y.[name]
    FROM sys.tables t JOIN sys.columns c ON t.[object_id] = c.[object_id]
                       JOIN sys.types y ON c.[user_type_id] = y.[user_type_id]
    WHERE y.[name] IN ('geography', 'geometry', 'hierarchyid', 'image', 'ntext',
    'numeric', 'sql_variant', 'sysname', 'text', 'timestamp', 'uniqueidentifier', 'xml')
      OR (y.[name] IN ('varchar', 'varbinary') AND ((c.[max_length] = -1) or 
                                                  (c.max_length > 8000)))
      OR (y.[name] IN ('nvarchar') AND ((c.[max_length] = -1) or 
                                   (c.max_length > 4000))) OR y.[is_user_defined] = 1;
    ```

    >**Note**: A full list of incompatible table features and data types can be found in the migration documentation at: <https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-overview-migrate/>.

4.  The output of the query shows the table and column, but not the reason for the incompatibility. To gain more insight into the reason you can script the table out by expanding the CohoDW database in Object Explorer, right-click the table, select Script Table as -\> CREATE To -\> New Query Editor Window.

    ![On the left pane of the Object Explorer, dbo.Dataset.org is selected. In its right-click sub-menu, Script Table as is selected. In it\'s sub-menu, CREATE to is selected, and on its sub-menu, New Query Editor Window is circled. In the right pane, the New Query Editor Window displays with output.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image47.png)

5.  From the script of the table, you can see that the 'TSQL' column of the 'DataLog' table has a data type nvarchar(4000) equivalent to 8000 bytes which means that the data may potential exceed the maximum data size.

    ![In the New Query Editor Script for the table, the following line is circled: \[TSQL\] \[navchar\](4000) NOT NULL.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image48.png)

6.  Before we fix this column, we must validate that none of the data would be truncated. Check the maximum actual data size with the following query.

    ```
    SELECT MAX(DATALENGTH([TSQL]))
    FROM DatabaseLog
    ```

    >**Note**: The result of 3034 means our longest value is 3034 bytes or 1517 characters leaving us plenty of space to modify the column with no loss of data.

7.  Modify the column by executing the following query:

    ```
    ALTER TABLE dbo.DatabaseLog ALTER COLUMN [TSQL] nvarchar(2000)
    ```

### Task 2: Prepare Azure SQL Data Warehouse and migrate schema

1.  In SQL Management Studio, click the **connect** button in Object Explorer, chose **Database Engine**, and connect to your SQL Data Warehouse using the **demouser** account and password to verify connectivity.

    ![The Connect to Server dialog box displays with the following field settings: Server name, cohodw.database.windows.net; Authentication, SQL Server Authentication; Login, demouser.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image41.png)

2.  On your Azure SQL Data Warehouse, expand **databases**, select the **CohoDW** database followed by selecting the **New Query** button. We use this query window to run our script to generate the schema later.

3.  Under your SQLCohoDW instance of SQL Server, right click your local copy of CohoDW, and select **Tasks -\> Generate Scripts** to launch the Generate and Publish Scripts wizard.

    ![In Object Explorer, Tasks / Generate Scripts is selected in the sub-menus.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image49.png)

4.  Click **Next** on the Introduction screen.

5.  On the Choose Objects screen, select the **Select specific database objects** radio button, and check **Tables** followed by clicking **Next**.

    ![In the Generate and Publish Scripts window, under Select the database objects to script, the Select specific-database objects radio button is selected, and below that, the Tasks check box is selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image50.png)

6.  On the Set Scripting Options screen, select the **Save to Clipboard** radio button, and click **Next**.

    ![In the left pane of the Generate and Publish Scripts dialog box, Set Scripting Options is selected. In the right pane, under Specify how scripts should be saved or published, the Save to Clipboard radio button is selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image51.png)

7.  Accept the defaults for the remaining screens, and click **Finish**.

8.  Paste the results into the Query window that you opened and connected to your Azure SQL Data Warehouse.

9.  This script still needs to be modified before it will run correctly in Azure SQL Data Warehouse because some T-SQL syntax is not supported in Azure SQL Data Warehouse. Make the following updates to the script:

    -   Execute a Find and Replace on your script to replace all occurrences of "ON \[PRIMARY\]" with "" to remove them from the script.

        ![In the Find field, On Primary is typed.](images/2018-06-26-14-53-28.png)

    -   Execute a Find and Replace on your script to replace all occurrences of "USE \[" with "\--USE \[" to comment out those lines.

        ![Use \[ is typed in the Find field, and \--USE \[ is typed in the Replace field.](images/2018-06-26-14-55-42.png)

    -   Comment out the leading GO statement.

        ![The leading GO statement of the script is commented out.](images/2018-06-26-15-07-23.png)

10. Change the database context from master to CohoDW, Run the script by clicking the **Execute** button. This will use the default options to create tables, Clustered Columnstore Indexing and ROUNDROBIN distribution.

    ![Screenshot of the Execute button.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image55.png)

11. Execute the following query to verify that your tables were created. There should be 33 rows returned.

    ```
    SELECT * FROM sys.tables
    ```

##  Exercise 3: Migrate the data to Azure SQL Data Warehouse

This exercise is focused on migrating the data from your existing data warehouse into SQL Data Warehouse. We will be pulling the data and uploading it to an Azure storage account. We will then import the data via Polybase.

### Task 1: Exporting the data from your current data warehouse

1.  Connect to your **SQLCohoDW** virtual machine.

2.  Open the **C:\\LabFiles\\bcp\_commands.txt** file. These are the bcp commands for each of the tables you need to migrate. The line below is an example. Notice the bcp commands all use the -C 65001 parameter. This indicates the output will be in UTF-8 which is required by Polybase. This code page is only an option with bcp.exe that ships with SQL Server 2016 and later tools. If you are using an older version of bcp, you will have an additional step to convert to UTF-8.

    ```
    bcp "select [ScenarioKey],REPLACE([ScenarioName],'|','||') from [CohoDW].[dbo].[DimScenario]" queryout "C:\Migration/dbo.DimScenario.txt" -q -c -C 65001 -t "|" -r "\n" -S localhost -T
    ```

3.  Close the file after you are done reviewing it. Change the file name to **bcp\_commands.bat**.

    ![In File Explorer, the name of the file is now changed to bcp\_commands.bat.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image56.png)

4.  Create a new folder named **C:\\Migration** on the local drive if you have not already done so. This is where the bcp\_commands.bat will save data to.

5.  Open a command prompt and execute **C:\\LabFiles\\bcp\_commands.bat**.

    ![In the Command Prompt window, the command C:\\LabFiles\\bcp\_commands.bat displays.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image57.png)

    > **Note:** In a production environment, you would likely make some effort to parallelize the execution of the various bcp commands. For larger tables, you also might parallelize the export from a single table.

6.  Navigate to the **C:\\Migration** folder. If the commands completed successfully, you will have **33 files**. Please review the files thoroughly to make sure you have all the files generated.

    ![In File Explorer, at the bottom, 33 items is called out.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image58.png "Verify the correct number of files was generated")

### Task 2: Transfer your data to Azure

1.  From your SQLCohoDW virtual machine navigate, download and install the latest version of the Microsoft Azure Storage tools from <http://aka.ms/downloadazcopy>.

2.  Open a command prompt and navigate to the **C:\\Program Files (x86)\\Microsoft SDKs\\Azure\\AzCopy** folder.

3.  Update the following command with the storage account name and key that you saved earlier and execute it to begin copying your data files to Azure (all of the text is a single command).

    ```
    AzCopy /Source:"C:\Migration" /Dest:https://<YourStorageAccount>.blob.core.windows.net/migration /DestKey:<YourStorageAccountKey> /pattern:*.txt /NC:2
    ```

4.  Confirm all 33 files were transferred successfully.

    ![Screenshot of the Command Prompt window showing that 33 files were transferred successfully.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image59.png)

     Verify the files are in the correct storage container by navigating to your storage account, clicking on **blobs**, and selecting your **migration** container.

     ![In the Migration container, under Name, dbo.AggregateSales.txt is selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image60.png)

5.  Open SQL Server Management Studio, and connect to the **CohoDW** database of your SQL Data Warehouse using the **dataloader** account.

6.  Execute the following to create a database scoped credential you will use to store the access key to the migration storage account. Make sure the password is correct that you provided earlier.

    ```
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Demo@pass123';

    CREATE DATABASE SCOPED CREDENTIAL MigrationCredential
    WITH IDENTITY = '<YourStorageAccountName>' , SECRET = '<YourStorageAccountKey>'
    ```

7.  Create an external data source by executing the following query. The external data source defines the location of your data and the credential used to access it. Again, be sure to replace the values with your own storage name and key.

    ```
    CREATE EXTERNAL DATA SOURCE MigrationStor WITH (TYPE = HADOOP,
    LOCATION=
    'wasbs://migration@<YourStorageAccountName>.blob.core.windows.net',
    CREDENTIAL = MigrationCredential);
    ```

8.  Create an external file format by executing the following query. The external file format defines the external storage and its layout.

    ```
    CREATE EXTERNAL FILE FORMAT MigrationFiles WITH(FORMAT_TYPE = DelimitedText,
    FORMAT_OPTIONS (FIELD_TERMINATOR = '|'));
    ```
9.  Open the **C:\\LabFiles\\CreateExternalTables.sql** file in SQL Server Management Studio and verify that you are connected to your Azure SQL Data Warehouse **CohoDW** database.

10. This file contains all of the external table definitions for our tables and directly leverages the external data source and external file format we created above. Click **Execute** to create the external tables.

    ![Screenshot of the Execute button.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image55.png)

11. Run the following code to verify that 33 tables were created:

    ```
    SELECT * FROM SYS.TABLES WHERE is_external = 1
    ```

12. From your **SQLCohoDW** virtual machine, open the **C:\\LabFiles\\LoadData.sql** file in SQL Server Management Studio.

13. The commands in this file insert data extracted directly from the data files stored in Azure Storage via the external tables we defined in the previous steps. Click **execute** to begin the data load.

     ![Screenshot of the Execute button.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image55.png)

14. After your data is uploaded, you can select data from any of the tables to verify success. In production environments, you would go through a much more thorough data validation process.

## Exercise 4: Migrate an SSIS Package to Data Factory v2

In this exercise, you will use the SSIS Integration Runtime in Azure Data Factory to run a pre-built SSIS package. You will start by deploying a pre-built package into your SSISDB Catalog. You will then schedule that package via Azure Data Factory.

### Task 1: Deploy SSIS Package to Data Factory

1.  From your **SQLcohoDW** virtual machine, navigate to the Azure portal.

2.  Navigate to the **CohoCloud** resource group and open your **SSISDB** database. This database was created by Azure Data Factory when you provisioned your Azure-SSIS Integration Runtime.

3.  Select the **Set server firewall** button. 

4.  Click the **+Add client IP** button, then click **Save**.

    ![On the firewall settings blade, the add client IP button is circled.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image62.png)

5.  Go back to the SSISDB overview blade, copy the **Server name** and paste it into Notepad for later use.

    ![On the SSISDB overview blade, the server name is circled.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image63.png)

6.  Open SQL Server Management Studio, and click the **Connect** button in Object Explorer. Paste the server name you just copied into the server name field, use **demouser** for the login and **Demo\@pass123** for the password. Then click on **Options \>\>**.

    ![On the connect to server window, the server name and login credentials are circled.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image64.png)

7.  On the **Connection Properties** tab, select the **SSISDB** database then click **Connect**.

    ![On the connection properties tab, the connect to database dropdown is set to SSISDB.](images/2018-06-25-19-33-38.png)

8.  Expand Integration Services Catalogs, right-click SSISDB and choose **Create Folder...**

    ![In Object Explorer, create folder is circle in the sub-menu.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image66.png)

9.  Name your folder \"Azure-SSIS\" and click **OK**.

    ![In the create folder window, the folder name is set to Azure-SSIS.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image67.png)

10. Expand the **Azure-SSIS** folder, right-click **Projects** and choose **Deploy Project...**

11. Click next on the information window, then on the Select Source window, make sure that the deployment model is set to **Project Deployment** and **Project deployment file** is selected. Then browse to **C:\\LabFiles\\DataLoad.ispac** and click **Next**.

    ![In the integration services deployment wizard, select source is highlighted on the left, on the right, project deployment is selected from the dropdown, the radio button next to project deployment file is selected, and the path is set to C:\LabFiles\DataLoad.ispac.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image68.png)

12. Click **OK** on the warning message.

    ![Screenshot of the warning message popup.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image69.png)

13. On the Select Destination window, make sure the server name of your SSISDB database server is set correctly, use demouser and Demo\@pass123 for authentication, click **Connect** to verify your credentials, then click Next.

    ![In the integration services deployment wizard, select destination is highlighted on the left, on the right, the servername is set to the name of your SSISDB logical server, authentication is set to SQL Server Authentication, login is set to demouser, the password is set to Demo@pass123, and the path is set to /SSISDB/Azure-SSIS/DataLoad.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image70.png)

14. Package validation will show you warnings regarding the connection. Click **Next** to continue.

    ![In the integration services deployment wizard, validate is highlighted on the left, on the right, two warnings are shown and the Next button is highlighted. ](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image71.png)

15. Click **Deploy** on the review window. The deployment should take less than a minute. Click Close after the deployment completes.

    ![In the integration services deployment wizard, the results window is shown with all steps running successfully.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image72.png)

16. Expand the **Projects** folder, the **DataLoad** project, the **Packages** folder, then right-click the **Package.dtsx** file and choose **Configure**.

    ![In object explorer, the packages are expanded, and configure is circled in the sub-menu.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image73.png)

17. Click the **Connection Managers** tab, for the DestinationDW connection, edit the values the **ConnectionString**, **Password** and **ServerName** properties to reflect the name of your Azure SQL Data Warehouse server, then set the password to Demo\@pass123.

    ![In the configuration window, the connection managers tab is selected, DestinationDW is highlighted, and ConnectionString, Password, and Servername are circled.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image74.png)

18. Click the SourceOLTP connection, edit the values the **ConnectionString**, **Password** and **ServerName** properties to reflect the name of your Azure SQL Database server that you deployed to the **CohoOnPremisesEnvironment** resource group at the beginning of the lab to host the cohoOLTP database. Click **OK** to save your changes.

    ![In the configuration window, the connection managers tab is selected, SourceOLTP is highlighted, and ConnectionString, Password, and Servername are circled.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image75.png)

19. You have deployed and configured the SSIS package to run in your Azure environment. You can execute the package by right clicking the package and choosing execute. SQL Server Management Studio will also give you a report of the current status. The execution view allows you to troubleshoot package execution directly from SSMS.

    ![Screenshot of the All Executions report showing examples of both failed and successful package runs.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image76.png)

### Task 2: Schedule the SSIS Package

1.  Open SQL Server Management Studio and connect to your SQL Data Warehouse.

2.  Execute the following to clean up the staging table that are being loaded by our SSIS package.

    ```
    TRUNCATE TABLE dbo.FactResellerSales_STAGE
    ```

3.  Launch the Chrome browser, and navigate to the Azure portal. From the Azure portal, navigate to the CohoCloud resource group, open your Azure Data Factory and click the **Author & Monitor** tile.

    ![Image of the Author and Monitor tile.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image77.png)

4.  Click the edit button on the left side of the Data Factory portal.

    ![In the Data Factory portal, the edit icon is selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image78.png)

5.  Click the **+** symbol and select **Pipeline**.

    ![In the Data Factory authoring portal, the plus sign is circled and Pipleline is circled in the sub-menu.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image79.png)

6.  Expand **General**, then drag the **Execute SSIS Package** activity onto the canvas.

    ![In the Data Factory authoring portal, execute SSIS package is circled with an arrow depicting the object being dragged to the blank canvas.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image80.png)

7.  Change the name of your activity to Load stage tables.

    ![On the general tab, change the name of the activity to Load stage tables.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image81.png)

8.  Switch to the settings tab, set the Azure SSIS IR to **Azure-SSIS**, change your logging level to **Verbose**, and set your package path to **Azure-SSIS/DataLoad/Package.dtsx**.

    ![On the settings tab, the Azure-SSIS IR dropdown is set to Azure-SSIS, the logging level is set to verbose and the package path is set to Azure-SSIS/DataLoad/Package.dtsx.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image82.png)

9.  Click the Publish All button to publish save your changes to Azure Data Factory. Wait for the success confirmation before continuing.

    ![Image of the confirmation message.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image83.png)

10. Click the **Trigger** button at the top of your pipeline pane and select **Trigger Now** to test you pipeline. If the Pipeline Run window opens, click **Finish** to continue.

    ![Trigger button has been selected and Trigger now is circled in the dropdown.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image84.png)

11. In the upper right hand corner you should see a notification bell, click the bell to see the current status of your pipeline.

    ![Screenshot of the notification message under the notification bell.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image85.png)

12. To schedule your Pipeline, click the trigger button again, this time select **New/Edit**.

    ![Trigger button has been selected and New / Edit is circled in the dropdown.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image86.png)

13. On the Add Trigger window, click **Choose trigger...*** then select **+New**.

    ![On the add trigger window, the new button is circled.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image87.png)

14. Change the name to **15minuteTrigger**, set the type to **Tumbling Window**, set the recurrence to **Every 15 Minutes**, select an end date of one full day from now and verify that **Activated** is checked. Then click **Next** and then click **Finish**.

    ![Screenshot of the completed schedule configuration.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/2019-01-22-15-37-41.png)

15. Click the **Publish All** button to save your changes.

    ![Publish all button.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image89.png)

## Exercise 5: Create an Analysis Services Model

Coho has provided you with an existing Analysis Services model for use with the Data Warehouse. They have asked you to use this model to support ad-hoc query access from Power BI.

In this exercise, you will configure backup, restore for Analysis Services, and create a tabular model to allow ad-hoc queries from client tools.

### Task 1: Configure Analysis Services backup

1.  Navigate to your Analysis Services instance and click on **Backups** under settings in the menu.

    ![Screenshot of the Backups button.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image90.png)

2.  Set backups to **Enabled**.

    ![Screenshot of the Enabled button.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image91.png)

3.  Click the **Backup Storage Settings** tile and then select **+Storage account** from the menu bar at the top of the screen.

    ![Backup Storage Settings Backup Storage: Not configured](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image92.png)

    ![Screenshot of the add Storage account button.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image93.png)

4.  On the Create storage account blade, use the following configurations, and click **OK**:

    -   Name: ***Choose a unique storage account name***.

    -   Account kind: **StorageV2 (general purpose V2)**

    -   Performance: **Standard**

    -   Replication: **Locally-redundant storage (LRS)**

    -   Location: ***The same location you have been using for this lab***.

5.  Choose the storage account you just created to open the Containers blade. Click **+Container** to create a new container, type **backups** for the name, and click **OK**.

    ![In the Containers blade, in the Name field, backups is typed, and the OK button is selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image95.png)

6.  On the **Containers** blade, click on the newly created **backups** Container, then click **Select**.

    ![Containers blade picture and backups is selected and click select button is selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image96.png)    

7.  On the Backups blade, click the **Save** button.

    ![The Backups blade dipslays.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image97.png "Save your backup configuration")

### Task 2: Restore Analysis Services backup

1.  From the Analysis Services overview blade, hover over the server name and click the copy icon to **copy the Server name**. Save this into notepad for use later in this task.

    ![In the Analysis Services overview blade, the Server name copy button is selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image98.png)

2.  In the Azure Portal, navigate to the storage account you just created, click the **Blobs** tile, and open the **backups** container.

3.  Click **Upload**.

    ![Screenshot of the Upload button.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image99.png)

4.  Click the Select a file icon, and upload the **C:\\LabFiles\\coho-data-model.abf** file, and click **Upload**.

    ![In the Upload blob blade, the select a file Icon button and the Upload button are selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image100.png)

5.  Login to your SQLcohoDW virtual machine, and open **SQL Server Management Studio**.

    ![Screenshot of the SQL Server Management Studio option.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image101.png)

6.  Connect to your Analysis Server.

    -   Server Type: **Analysis Server**

    -   Server name: ***the server name you copied earlier***.

    -   Authentication: **Active Directory Password Authentication**

    -   User name: **asadmin@\<subscription\_name\>.\<domain\>**

        ![Fields in the Connect to Server dialog box are set to the previously defined settings.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image102.png)

        >**Note**: If you are using your own organizational account instead of the one we created earlier in the lab then you will put that in for the user name. You may also need to update the authentication type depending on the requirements of your organization (for example, if you use multi-factor authentication).

    The User name setting should be in the form \<name\>@\<your-domain\>. If you do not know your domain name, you can get it by navigating to the Azure Portal and hovering over your login information in the upper right corner of your browser window.

7.  Right-click the Databases folder and choose **Restore...**

    ![In Object Explorer, the Databases folder is selected, and Restore is selected from its right-click menu.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image103.png)

8.  Select the backup file by clicking the **Browse** button and selecting the **coho-data-model.abf** file from the storage account. Click **OK** to accept the backup file.

    ![In the Locate Databases Files section, the following folder is selected: /on asazure://southcentralus.asazure.windows.net/ssadw1.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image104.png)

9.  Type **coho-data-model** into the Restore database field, and click **OK** to restore the database.

    ![In the Restore Database dialog box, coho-data-model is typed in the Restore database field.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image105.png)

10. Refresh the databases folder, and you should see your coho-data-model now.

    ![In Object Explorer, the Databases folder is selected and expanded. Below the folder, coho-data-model displays.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image106.png)

### Task 3: Update Analysis Services connections

1.  From SQL Server Management Studio, expand the **coho-data-model** database, expand **Connections**, right-click **CohoDW**, script the connection as **CREATE OR REPLACE To** a **New Query Editor Window**.

    ![In Object Explorer, the following path is expanded: Databases\\coho-data-model\\Connections. Under Connections, CohoDW is selected. From its right-click menu, Script Connection As / CREATE OR REPLACE to / New Query Editor Window are selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/2019-01-22-18-36-54.png)

2.  Modify the connection string to point to your SQL Data Warehouse.

    ![In the New Query Editor Window, cohodw9607 is circled.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image108.png)

3.  From the Query menu, click **Execute** to update the connection.

    ![Execute is selected in the Query menu.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image109.png)

3.  Right-click the **coho-data-model** database, and choose **Process Database**.

    ![In Object Explorer, coho-data-model is selected, and from its right-click menu, Process Database is selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image110.png)

4.  Change the **Mode** to **Process Full** and then click **OK** on the Process Data window.

    ![The Process Database window displays.](images/2018-06-26-19-17-14.png)

5.  Close the Process Data window.

## Exercise 6: Visualize data with Power BI Desktop

In this exercise, you will setup integration with Power BI Desktop.

### Task 1: Install Power BI Desktop

1.  Connect to the **SQLcohoDW** virtual machine.

2.  In a web browser, navigate to the Power BI Desktop download page (<https://powerbi.microsoft.com/en-us/desktop/>).

3.  Select the **Download Free** link in the middle of the page.

![Screenshot of the Power BI Download Free webpage.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image112.png)

4.  Run the installer.

    ![In the Download bar, the Run button is selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image113.png)

5.  Select **Next** on the welcome screen.

    ![The Microsoft Power BI Setup wizard displays, and the Next button is selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image114.png)

6.  Accept the license agreement, and select **Next**.

    ![On the Microsoft Software License Terms page, the check box is selected for I accept the terms in the License Agreement, and the Next button at the bottom is selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image115.png)

7.  Leave the default destination folder, and select **Next**.

    ![In the Microsoft Power BI Setup wizard, on the Destination Folder page, the Next button is selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image116.png)

8.  Make sure the Create a desktop shortcut box is checked, and select **Install**.

    ![On the Ready to install page, the Create desktop shortcut check box is selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image117.png)

9.  Verify that Launch Microsoft Power BI Desktop is checked, and select **Finish**.

    ![On the Microsoft Power BI Setup wizard Completed page, the Finish button is selected.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image118.png)

### Task 2: Query data with Power BI Desktop

1.  Connect to the Azure Portal, and navigate to your Azure Analysis Services.

2.  Make note of your Analysis Server name to use in your data source configuration later in this task.

    ![In the Analysis Services blade, Overview is selected. In the Essentials section, the Server name is circled.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image119.png)

3.  From within Power BI, click the **Get Data** button.

    ![The Get Data button is selected on the Power BI Desktop.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image120.png)

4.  On the Get Data window, select **Azure** from the list on the left. Then, choose Azure Analysis Services database, and click **Connect**.

    ![In the Get Data Window, in the left pane, Azure is selected. In the right pane, Azure Analysis Services database (Beta) is selected. At the bottom, the Connect button is selected.](images/2018-06-26-18-21-40.png)

5.  If you get a Preview connector warning, click **Continue**.

    ![The Preview connector warning page displays.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image122.png)

6.  On the **SQL Server Analysis Services database** screen, provide the name of your Analysis Server service, type **,** make sure that **Connect live** is selected, and click **OK**.

    ![On the SQL Server Analysis Services database page, the following two fields and their settings are circled: Server, asazure://southcentralus.asazure.windows.net/ssadw1; Database (optional), coho-data-model; The radio button for Connect live is selected. ](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/image123.png)

7.  Login with your Active Directory Azure Portal credentials.

8.  Select your Analysis Services database.

8.  In the Fields blade in the dark grey side bar to the right, expand the **DimGeography** dimension and check the box next to **CountryRegionCode**. This will automatically launch the map visualization, because Power BI is smart enough to understand this is geographic data.

    ![In the Power BI window, in the left pane, a world map displays with dots on it. In the right pane, two more panes display: Visualizations, and Fields. In the Visualizations pane, the World graph icon is selected. Under Location, CountryRegionCode displays. In the right, Fields pane, DimGeograpny is expanded, and below it, the CountryRegionCode is check box is selected.](images/2018-06-26-18-34-00.png)

9.  The circles that PowerBI adds to the map are simply every country/region in which Coho had sales. Let's add the sales amount to this to make the map a little more interesting. Add the **SalesAmount** from the **FactInternetSales** table by putting a check next to it. The circles on the map will change in size to reflect the sum of all sales in that particular country/region.

    ![In the Power BI window, in the left pane, a world map displays with varying-sized dots on it. Larger dots are over North America and Australia. In the right Fields pane, the SalesAmount check box is selected.](images/2018-06-26-18-37-09.png)

10. We want to see a little more specific detail around what these circles actually mean, so let's add a legend to identify the countries/regions. Drag the **EnglishCountryRegionName** field under **Legend**.

    ![In the Power BI window, in the left pane, the world map displays, this time with dots that vary by size and color. In the fields pane, under DimGeography, the EnglishCountryRegionName field is circled, and the check box is selected. An arrow points from here to the same EnglishCountryRegionName field in the Visualizations pane, under Legend.](images/2018-06-26-18-41-49.png)

11. Click the **Save** button in the top left of your screen, name your report **Sales by countryregion,** and click **Save**.

## After the hands-on Lab 

To prevent excessive charges, you should cleanup the resources you have created for this lab.

### Task 1: Cleanup resource groups

1.  From the Azure Portal, navigate to the **CohoCloud** resource group.

2.  From the resource group overview blade, click **Delete resource group**.

    ![In the Resource group blade, the Delete resource group icon is selected in the top menu.](images/Hands-onlabstep-by-step-MigrateEDWtoAzureSQLDataWarehouseimages/media/2019-01-22-19-06-25.png)

3.  Type the name of the resource group to confirm the delete request, and click **delete**.

4.  Repeat the process to delete the **CohoOnPremEnvironment** resource group.

You should follow all steps provided *after* attending the hands-on lab.

