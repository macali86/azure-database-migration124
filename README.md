# Azure Data Migration - AICore Project

The aim of this project is to architect and implement a cloud-based database system on Microsoft Azure, it is to showcase the skills and hand-on expertise that has been learnt over the Azure section of the AI Core course.

There are 3 main sections in this project, the first is to establish a production environment database and to migrate this to Azure SQL Database.

Secondly, we will simulate a disaster recovery scenario with potential for data loss, here we also look at geo-replication and failover configuration.

Finally, we integrate Microsoft Entra ID and assign specific access roles to enhance security and add another layer of control and protection.

#### Milestone 1

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

## Create Production Database

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
