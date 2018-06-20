# Migrate EDW to Azure SQL Data Warehouse setup

## Before the hands-on lab

In this exercise, you will deploy the source environment for this lab. The source environment is designed to represent the existing on-premises environment you will migrate to Azure SQL Data Warehouse.

### Task 1: Deploy the source environment

1.  Browse to the Azure Portal at <https://portal.azure.com>

2.  Click **+Create a resource,** and type **SQL Server 2016** in the search box. Choose the latest service pack version of **Free License: SQL Server 2016 SP2 Developer on Windows Server 2016** from the search results. ![](images/Setup/image3.png)

3.  Click the **Create** button to begin deployment of the SQL Server

4.  On the Basics blade, use the following configurations:

    -   Name: **SQLcohoDW**

    -   VM disk type: **SSD**

    -   User name: **demouser**

    -   Password: **Demo\@pass123**

    -   Subscription: ***Your subscription***

    -   Resource Group: **Create new** **OnPremisesEnvironment**

    -   Location: ***Choose a location near you***

5.  On the size blade, choose **D2s\_v3**. If this option is not available in your chosen region, choose DS2\_V2.

    ![](images/Setup/image4.png)

6.  On the Settings blade, change the Auto-shutdown time zone to reflect your current time zone. Then, click **OK**.

    ![In the Settings blade Auto-shutdown section, Enable auto-shutdown is set to On, Shutdown time is 7:00:00 PM, and Time zone is Central Time (US and Canada).](images/Setup/image5.png)

7.  On the SQL Server settings blade, enable SQL Authentication, and click **OK**.

    ![](images/Setup/image6.png)

8.  On the Summary blade, review your settings. Then, click **Create**.

### Task 2: Configure the SQL Server

1.  Login to the **SQLcochoDW** virtual machine you created in the previous task

    ![Screenshot of the SQLcochoDW button.](images/Setup/image7.png)

2.  From Server Manager, click on **Local Server** select **IE Enhanced Security Configuration,** and set it to **Off**

    ![In the Server Manager menu, Local Server is selected.](images/Setup/image8.png)

    ![In the Everything section, IE Ehanced Security Configuration, which is set to Off, is selected.](images/Setup/image9.png)


3.  Install the Azure PowerShell command-line tools from:

    <https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids>

4.  Open **File Explorer,** and create two new folders called **C:\\LabFiles, C:\\Migration**

5.  Download the Student Files from <http://cloudworkshop.blob.core.windows.net/migrate-edw/StudentFiles-06-2018.zip>, and extract the files to **C:\\LabFiles**

    ![The Extract Zipped folders window displays. In the Browse field, C:\\LabFiles\\ displays.](images/Setup/image10.png)

6.  Launch **SQL Server Management Studio,** and connect to the SQLcohoDW instance

7.  Open a **New Query** window, and cut/paste the following code into the window

    ```
    RESTORE DATABASE CohoDW FROM DISK = 'C:\LabFiles\CohoDW.bak' 
    WITH MOVE 'CohoDW_Data' TO 'F:\Data\CohoDW_Data.mdf'
       , MOVE 'CohoDW_Log' TO 'F:\Log\CohoDW_Log.ldf'
    ```

9.  Click **Execute** to restore the database

    ![Screenshot of the Execute button.](images/Setup/image11.png)

### Task 3: Deploy an Azure SQL Database

1.  Browse to the Azure Portal and authenticate at <https://portal.azure.com/>

2.  Click **+Create a resource** and type **SQL Database** in the search box. Choose **SQL Database** from the results\
    \
    ![](images/Setup/image12.png)

3.  Click **Create** on the SQL Database blade. Specify the following information, and click **Create**.

    -   Database name: **cohoOLTP **

    -   Subscription: **Your subscription**

    -   Resource group: **Create new** - **CohoOLTP**

    -   Select source: **Sample (AdventureWorksLT)**

    -   Server: ***Create a new server***

        -   Server name: ***Choose a unique name***

        -   Server admin login: **demouser**

        -   Password: **Demo\@pass123**

        -   Location: ***The same location you used for your other resources***

        -   Allow azure services to access server: ***checked***

    -   Pricing tier: **S1**

         ![](images/Setup/image13.png)

