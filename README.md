# Azure Data Migration Project

The aim of this project is to architect and implement a cloud-based database system on Microsoft Azure, it is to showcase the skills and hand-on expertise that has been learnt over the Azure section of the AI Core course.

There are 3 main sections in this project, the first is to establish a production environment database and to migrate this to Azure SQL Database.

Secondly, we will simulate a disaster recovery scenario with potential for data loss, here we also look at geo-replication and failover configuration.

Finally, we integrate Microsoft Entra ID and assign specific access roles to enhance security and add another layer of control and protection.

## Create Production Environment

To start, we have set up the production environment, this includes provisioning a Virtual Machine in Azure and then installing SQL Server and SQL Server Management Studio onto the Virtual Machine (VM).

Here we will go through the step by step with links to the downloads included.

Firstly, we have to provision a VM, after loggin onto the Azure Portal and navigating to the VirtuaL Machine, we do the following;

- Click on + create, and the select Azure Virtual Machine,
- Create a new resource group and name it,
- Select a region to host (select the closest to you, in this case UK South),
- In the image bar select the Operating System that your VM will run (Windows 11 Pro),
- In the size option select the VM size, the bigger the size the more workload it can handle (we selected Standard B2ms),
- Add a username and password,
- For public inbound ports we have checked Allow Selected Ports, this will allow us to access the VM,
- Finally in the Select inbound ports we select RDP (3389), Port 3389 is used to facilitate remote access to Windows computers through the Remote Desktop Protocol (RDP), and the click review + create.

Next, we have to connect to the VM, to do this once the VM has been craeted we click on the go to resource button, in the top bar we will see a connect button, select it and then we will see a button that states, download RDP, once we have downloaded the RDP file, open it and follow the prompts, we will be asked for the password we created previously.

Finally, once on the VM we download SQL Server Developer from here https://www.microsoft.com/en-gb/sql-server/sql-server-downloads, and somplete the installation process, once this has been completed we can download SQL Server Management Studio from here, https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver16. These tools are essential in creating production environment because they mirror the capabilities of a corporate database server.

#### Create Production Database

Once the production environment has been created and configured our next step is to create a production database, we had to do this by restoring a bacup file known as AdventureWorks, Here we will go through the step by step process.

- Download the backup file from source.
- We have to copy the backup file and paste it into the SQL Server backup folder, we do this by copying the AdvetureWorks .bak file anf then navigationg to C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSQL\Backup and pasting it into the folder,
- Open SQL Server Management Studio,
- Connect to the SQL Server instance (this is the server in Azure),
- Right click Databases in the Object Explorer and choose Restore Database,
- Choose the Device option and click the ... button,
- Click Add button and navigate to the file, this will be on the left hand side and then select full backup,
- Click OK to perform restoration.

At the end of this Milestone I had a complete Production environment setup, with the Production Database in a VM. 

## Cloud Migration

Now we have to transition our database to Azure's cloud ecosystem by migrating the on-premises database to Azure SQL Database. We will go through the step-bystep process that was involved in completing this.

#### Create Azure SQL Database

Firstly, we begin by creating a Azure SQL Database, this is done through the Azure Portal, after logging on we do the following;

- Select SQL Databases and click the + Create tab,
- Create a database name and select create new for the server,
- We now give the server a unique name and set location to the nearest to us which is UK South,
- For Authentication method we have selected Use SQL authentication and the create a Server admin login and password,
- For the compute and storage section click the Configure database option and in the Service tier section select Basic as we don't need much storage,
- We left the rest as their default settings and then press review + create,
- Once deployed we select Go to resource to see the overview page.

#### Connect Server via VSCode

The next step is to connect to our Sever via VSCode, to do this we go into the extensions tab in VSCode and search for SQL Server, then install the extension, the next steps are as follow;

- Click on the server window on the left hand side and select Add Connection,
- Go back to the Azure portal and the SQL Database overview page and copy the Server name,
- Paste this into the VSCode dialog box that opens when you selected Add connection, 
- Choose a database name if you wish, when prompted for Authentication type we select SQL Login,
- Enter the username and password we created when creating our SQL Database in Azure, and enter a display name.

After completing these steps we actually encounter an error message, this is because public access in disabled to our database, this is done by default by Azure, we also encounter an error due to firewall rules, here we will go through the steps to resolve these issues;

- Navigate to Azure SQL Database, in this case it is azure-project-db and select the set server option near the top of the overview page,
- check the selected netork option in the Public netowrk access section, and select save at the bottom.
- Now we back to VSCode and repeat the login process from before, we will be met with another error where we have to add our Azure account, we select Add account and add the account,
- We have to repeat the logging on process again and are met with another message, click on the Create Firewall Rule, this will allow traffic from our Client IP address,
- When we click Create Firewall Rule, we see the pop up in VSCode that contains out IP address in the Start IP, we press enter and the the End IP address which is the same we also press enter.

After this we do the logging process again, though it didn't login the first time, waiting a few minutes, refreshing Azure helps, as it gives the systems time to update.

### Database Migration

#### Download & Configure Azure Data Studio

Now, we have to prepare the Migration of the database, we start this process by downloading Azure Data Studio onto our VM, it is usually downloaded automatically when you download SQL Server Management Studio, if not you can download it here https://azure.microsoft.com/en-gb/products/data-studio. Once the installation process is complete we have to configure it by completing the followig steps to connect to a Local SQL Server Database;

- CLick on the server icon in the Activity Bar and select New Connection,
- Select Microsoft SQL Server as the server type,
- In the server name type localhost and and do the required authentication,
- When we try to select our database we are met with a Authentication Error, we have to click on the Enable Trust seerver certificate,
Then when we click connect we will have established a connection and we can see our local database in Data Studio.

Now we have to connect to our Azure SQL Database, the initial steps are the same but after seleting New Connection we input some diffferent information;

- In the connection Dialog select Microsoft SQL Server,
- Put in the server name, we can get this from our Azure SQL Server overview page on the portal,
- Select the appropriate Authentication Type (SQL Authenticaation) and input our Server username and password,
- Now we can see the server we want, select it and we will be met with a error box, this indicates VM cannot access the server because the machine has not been added to the SQL Server Firewall,
- Go to the VM overview page and copy the IP address,
- Go to the Azure Server overview page and in the side-panel we access the Networking section,
- Select add firewall rule, give the rule a unique name and paste the IP address in the start IP and End IP and click save,
-  No we go back to Azure Data Studio and we can establish connection.

### Migrate from local server to Azure Server

#### Schema Compare

Now we should have 2 servers show in the side panel, one is connected to the localhost and the other to our Azure Server, Now we have to Migrate our database that is on the localhost to our Azure Server. 

First we install the SQL Server Schema Compare extension in Azure Data Studio and complete the following steps;

- Right-click on our local SQL server and select Schema compare,
- In the dialog box click the ... button, here we make sure that the Source corresponds to our loacl databse and Target corresponds to our Azure SQL Database, and click OK,
- We click Compare and select all the schema changes we want to apply, and click Apply.

#### Data Migration

SQL Schema Compare allows the synchronization of the structure of the database, this includes tables, views, procedures and other objects, note we have not migrated the data that is contained yet, to do that we have to carry out the following tasks;

- Add the Azure SQL Migration extension,
- Right-click on the conected Azure SQL Server and select Manage,
- Click on the Migrate to Azure SQL button,
- From here the setup wizard is opened and we have to follow the steps,
- step 5 includes creating a Azure Database Migration Service, we do this by navigating to the Azure portal and searching for Database Migration Service,
- In the Homepage we select create to create a new service and leave the configurations as default, also select the same VM to which our database has been provisioned and UK South as our location and the click create,
- Now, we go back to our Azure Data Studio and select the Migration Service, we are met with a message that the service is not registered,
- We have to download and install the itegration runtime, the link is in the error meaasge,
- Once download is complete we run and copy and paste any one of the 2 Authentication keys into the integration runtime and select register, after a few minutes when we go back to Azure Data Studio everything will have updated and we can continue,
- For the next step, we provide the password we created for the local SQL Server, and select all the tables we want to migrate, in this case all of them,
- We click update and then the Run Validation button, once this is complete we can select Start migration to begin the process.

Once completed all the tables, along with the schema had been migrated to the VM.

## Data Backup & Restore

We have to ensure that the data stored in our production database is securely storedon Azure and also creating a developer environment for the database. The production database is for storing real customer data whereas a developer databse is used for testing and experimenting, it gives developer the opportunity to test and alter the data without affecting the integrity of the live data.

#### Create a full backup of production database

The first task is to create a full backup of the production database, to do this we have to go onto SQL Server Management Studio (SSMS), connect to our SQL Server instance and complete the following step;

- Right-click our database (AdventureWorks2022), and select Tasks>backup,
- In the dialog box choose a destination for the backup (C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup\),
- We then selected a full backup,
- Click OK to initiate backup.

Now we have we have backed up the file locally we can create a Blob storage backup on Azure, this means that is anything happens to our local machine we still have a offline backup, to do this we complete the following steps;

- Log into our Azure account,
- Create a storage account (I used the same resource group as this project),
- Created a container called aicoreprojectbackup,
- Whilst in the container clicked the upload button,
- Navigated to where the file is stores locally, in this case C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup\,
- drag and dropped the file into the upload section and clicked the blue upload button.

Now we have a local and cloud stored backup of AdvetureWorld2022.

#### Create A Developer Environment

To create a developer environment we firstly repeat the steps of provisioning a VM and downloading the necessary programs, this includes SQL Server Developer and SQL Server Management Studio.

Once this has been done we have to restore our database in the Developer Environment.

- In our VM we navigate to our blob sotrage backup in Azure and download it,
- We move the file from our Download folder and into the backup folder of the SQL Server (C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup\),
- Open SSMS, right-click on Databases and select Restore database,
- In the dialog box select Device and click the ... button,
- Click the Add button and find the AdventureWorld2022 file in the relevant folder,
- Click OK to restore the database.

#### Periodic Backups

We can now schedule a automatic backup, this means that SSMS will autonatically back up the database and upload the backup to the Azure blob storage. We do this in SSMS by doing the following;

- Right-click on SQL Server Agent node and then click Start,
- Select Yes when asked if we want to start the Agent,

We need to create a SQL Server Credential, we do this by right-cicking on Databases and running a query, the query will look like this;

CREATE CREDENTIAL [YourCredentialName]
WITH IDENTITY = '[Your Azure Storage Account Name]',
SECRET = 'Access Key';

We replace [YourCredentialName] with a unique name and can find our Storage Account Name and Access Key by navigating to storage account in Azure and clicking the Access key tab in the left side panel. Once we have completed and run this in SSMS we can check if it has succeeded by checking Security node > Credentials, with the name you provisioned the Credential with.

Now we can continue with the Backup plan (Maintenance Plan).

- In the Object Explorer, expand the Management node, right-click on Maintenance Plans, and select Maintenance Plan Wizard,
- We select full backup in the dialog box, and click next and select our database,
- We choose URL as our destination,
- In the Destination tab you will first have to select the SQL Server Credentials from a drop-down list. Here you should select the credentials you have provisioned before, that will allow you access to the Azure Storage Account.
- In the Azure Storage Container section, I put the Azure storage I previously created,
- We leave the rest as default and then click Finish to create the plan.

After refreshing the Object Explorer we can see our maintenance plan under Maintenance Plans, we right-click and select execute to run the plan. Once it has executed we can see it in the design window, here we can click on the calendar button and set a weekly automated schedule to back up.

Finally, we go into our Azure storage container and we can see that a backup has been uploaded, we now have 2 files in our container.

## Disaster Recovery Simulation

Here we will simulate the process of losing data in our production database and the steps we can take to restore the database, each process will be documented to ensure that full data is restored without any critical loss.

The first step is to go into Azure Data Studio and connect to our production Azure SQL Database, once the connetion has been established we choose a table we would like to alter, in my case I used Person.EmailAddress. Then we complete the follwoing steps;

- Right-click on the connected Azure SQL Server and select new query,
- Run the following query;

    SELECT *
    FROM Person.EmailAddress;

- Here we can see the data that is outputted, if we scroll to the bottom of the table, we can see there are 19972 rows in total,
- Now we run the follwoing query to simulate the data loss;

    DELETE TOP (100)
    FROM dbo.dim_products;

- For some reason the query did not delete the top 100 but did delete 100 rows, after running it a couple of times, and then then running the SELECT query again and scrolling to the bottom, we can see there are 19172 total rows.

Now we have simulated the data loss we have to restore the data by going into our Azure portal, once logged into the portal we do the following;

- Navigate to the Azure SQL database dashboard, in our case it is adventureworks-az-db,
- Click in the restore tab, this will redirect you to another page,
- Select a restore point, we chose 23/01/2024 09:00:00 AM,
- Create a new name for the database, ours was adventureworks-az-db-restored-1,
- Click create, then review and create.

The deployment can take a while, so we have to be patient.

Once the deployment has been completed we can go back into Azure Data Studio and create a new connection, we select the restored database to connect to and when we run the SELECT query the total number of rows is 19972 once again, showing that the database has been successfully restored.

Finally, we go back into Azure and delete the SQL Databse that has been corrupted.

## Geo-replicaiton and Failover

Here we will configure a geo-replication for our production database, this is a disaster recovery feature that establishes a synchronized copy of the database in a secondary region. The benefits of this are that it makes our data more secure whilst also allowing continuous data availability in the event of unforeseen disruptions or downtime. We will also do a failover test simulating real world scenarios, where we have to assess the consistency of the data once it has been moved to the secondary region, we will go through the process step by step;

- Navigate to the Azure SQL Database homepage in the Azure Portal,
- Select the primary database, in our case it is adventurewors-az-db-restored-1,
- Under Data Management in the left hand menu select Data Management,
- Click on Create replica, here we first need to create a new SQL Server by selecting Create,
- We named the server replication-server and set its region as US East, it is crucial we select a region far from of primary region,
- Select Use SQL Authentication, and create the server,
- Back in the Geo Replica window we select Review + Create.

Once it has been deployed we can go to the resource page to see the details.

Failover and Tailback are the processes of switiching the workload from the primary region to the secondary region and then back again. We can initiate a test failover to see if it works without disrupting the production workload, we can follow these steps to run the test;

- Navigate to the Azure SQL Database dashboard and select the primary database that is located in the UK region (adventurewors-az-db-restored-1),
- Under Data Management in the left hand menu, select Failover groups then Add group,
- Create a name for the group in my case az-project-failover, and for server select or US East server,
- Now navigate to the replication-server an under Failover groups we can see our az-project-failover group.

If we click on the failover group we can visually see the locations of our primary and secondary servers, the primary server will have a green tick next to it. We can run a test failback by;

- Clicking the failover button on the top panel, we will get a dialog box that asks if we are sure, click Yes,
- After a few minutes the green tick will move to our secondary location and that will show as primary,
- If we go back into Azure Data Studio and reconnect, we can see it is working just fine, this means that the failover was successful.

To perform a Tailback we just click the Failover button in Azure again and we can see the primary and secondary locations switch back.


