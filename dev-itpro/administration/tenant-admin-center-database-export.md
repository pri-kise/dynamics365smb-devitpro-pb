---
title: Exporting Databases in the Admin Center
description: Learn how to export production environment data as BACPAC files to Azure storage. Start your export today.  
author: jswymer
ms.topic: how-to
ms.devlang: al
ms.search.keywords: administration, tenant, admin, environment, sandbox, database, export, bacpac, backup
ms.date: 06/20/2025
ms.author: jswymer
ms.reviewer: jswymer
---
# Exporting databases in the admin center

From the [!INCLUDE[prodadmincenter](../developer/includes/prodadmincenter.md)], you can export the database for [!INCLUDE[prod_short](../developer/includes/prod_short.md)] online environments as BACPAC files to an Azure storage container.

## Considerations before you begin

- You can only request a database export for production environments. If you want to export data from a sandbox environment, you can use Excel or RapidStart.
- You can only request a database export if the customer has a paid Business Central subscription.
- You must have explicit permission to export databases. For more information, see the [Users who can export databases](#users-who-can-export-databases) section.
- You can't export your database to an Azure premium storage account. The steps in this article are only supported on Azure standard storage accounts.
- You can export the database of an environment that's encrypted with a [customer-managed encryption key](../security/security-online.md#customer-managed-encryption-key). The bacpac files in the storage account are encrypted with the [encryption key applied to the storage account](/azure/storage/common/customer-managed-keys-overview), not the environment's encryption key.

> [!NOTE]
> For each environment, you can export the database a maximum of 10 times per month. You can see the number of exports still remaining for the current month in the **Create Database Export** pane when creating the export file.

## Setting up Azure storage

Before you can export the file, set up the Azure storage account container that the BACPAC file. 

### Creating the storage account

The first step is to create a **Standard general-purpose v2** Azure storage account, if you don't already have one. To set up the export, you must first have a subscription to Microsoft Azure and access to the [Azure portal](https://portal.azure.com).

> [!IMPORTANT]
> Exporting an environment database to a storage account that isn't a **Standard general-purpose v2** Azure storage account, such as V1 or Premium storage accounts, isn't supported.

For more information setting up an Azure storage account, see [Create a storage account](/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

### Generating a shared access signature (SAS)

The next step is to generate a shared access signature (SAS) that provides secure delegated access to your storage account. [!INCLUDE[prod_short](../developer/includes/prod_short.md)] uses the signature to write the BACPAC file to your storage account.

#### To generate a shared access signature (SAS)

1. On the Azure storage account, choose **Shared access signature** in the navigation pane.
1. In the **Allowed services** section of the shared access signature pane, select **Blob**, and clear the other options.
1. In the **Allowed resource types** section, select **Container** and **Object**, and clear the other options.
1. In the **Allowed permissions** section, mark **Read**, **Write**, **Delete**, and **Create**, and clear the other options.
1. Select a start and end date and time for the SAS. A minimum expiration window of 24 hours from the initiation of the export is required.

    > [!TIP]
    > It's a best practice to use near-term expiration for the account's SAS. To reduce risk of a compromised storage account, set the end date and time no later than what is needed for you to complete the database export operation. However, the SAS must be valid for a minimum of 24 hours.

1. In the **Allowed protocols** section, select **HTTPS only**.
1. Select **Generate SAS and connection string**.
1. Copy the **Blob service SAS URL**.

For more information on generating and using a SAS, see [Grant limited access to Azure Storage resources using shared access signatures (SAS)](/azure/storage/common/storage-sas-overview).

## Creating the database export

After you created the Azure storage account and generated the SAS URI, you can then create the export file from the [!INCLUDE[prodadmincenter](../developer/includes/prodadmincenter.md)].

1. On the **Environments** list page, choose the relevant production environment to view the environment details.
1. On the action ribbon of the environment details, choose **Database**, and then choose **Create Database Export**.
1. In the **File Name** field, specify a name for the export file, or leave the default value.
1. In the **SAS URI** field, specify the **Blob service SAS URL** value that you copied in the previous section.
1. In the **Container Name** field, enter the name of the container in the Azure storage account to which you want the BACPAC file exported. If you already created a container in your Azure storage account, enter the name of that container here. Otherwise, if the name specified in the **Container Name** field doesn't exist in the Azure storage account, it's created for you.

Once the export operation begins, the BACPAC file is generated and exported to the indicated Azure storage account. The operation might take several minutes to several hours depending on the size of the database. You can close the browser window with the [!INCLUDE[prodadmincenter](../developer/includes/prodadmincenter.md)] during the export. When the export completes, you can access the export file in the defined container in your Azure storage account. Optionally, you can import the data into a new database in Azure SQL Database or SQL Server for further processing. For more information, see [Quickstart: Import a BACPAC file to a database in Azure SQL Database](/azure/sql-database/sql-database-import).  

## Viewing the export history

All database export activity is logged for auditing purposes. To view the history, choose **Database**, then choose **View Export History** on the environment details page of the environment.

## Users who can export databases

Permission to export databases is limited to specific types of users: internal and delegated administrators. The following users are allowed to export databases.

- Delegated administrators from reselling partners

- Administrators from the organization that subscribes to [!INCLUDE [prod_short](../developer/includes/prod_short.md)] online

Also, these users must have the **D365 BACKUP/RESTORE** permission set assigned to their user account in the environment they're trying to export.

For more information about permissions sets and user groups, see [Assign Permissions to Users and Groups](/dynamics365/business-central/ui-define-granular-permissions).  

## Using the exported data

The BACPAC file contains data that is customer-specific business data. Technically, [!INCLUDE [prod_short](../developer/includes/prod_short.md)] online is a multitenant deployment, which means that each customer tenant has their own business database while the data that defines the application is in a shared application database.  

To export data when moving the customer's [!INCLUDE [prod_short](../developer/includes/prod_short.md)] from an online to an on-premises deployment, get the application data from the installation media for the same version as the online tenant. You find the version number in the [!INCLUDE [prodadmincenter](../developer/includes/prodadmincenter.md)] or on the **Help and Support** page in client.

> [!IMPORTANT]
> While you can import the downloaded BACPAC file into your own SQL Server instance, Microsoft doesn't provide support for creating a working on-premises environment from the BACPAC that you download from [!INCLUDE [prod_short](../developer/includes/prod_short.md)] online.  

For more information, see [Quickstart: Import a BACPAC file to a database in Azure SQL Database](/azure/sql-database/sql-database-import), [Migrating to Single-Tenancy From Multitenancy](../deployment/Merging-an-Application-Database-with-a-Tenant-Database.md), and [When to choose on-premises deployment](../deployment/Deployment.md#when-to-choose-on-premises-deployment).  

> [!NOTE]
> If you're getting an error saying your file contains corrupted data when importing the bacpac file, make sure you're using the .NET Core version of [SqlPackage.exe](/sql/tools/sqlpackage/sqlpackage-download).

## Restoring the exported data to Business Central online

If you decide at some point that you want to restore the exported data to a new environment in [!INCLUDE [prod_short](../includes/prod_short.md)] online, then you must go through the same steps as you went through to migrate from on-premises to [!INCLUDE [prod_short](../includes/prod_short.md)] online. This way, you can prepare the database so that it's ready to migrate to the latest version of [!INCLUDE [prod_short](../includes/prod_short.md)]. For example, you could choose to replicate the data to a sandbox environment for further testing and training. For more information, see [Migrating on-premises data to Business Central online](migrate-data.md).  

## Restoring the exported data to a container

If you want to use the exported data in a container-based developer environment, you can use Windows PowerShell scripts to help you do that, including the [BCContainerHelper PowerShell module](https://github.com/Microsoft/navcontainerhelper).  

## Related information
[SQL Server technical documentation](/sql/sql-server/)  
[Quickstart - import a BACPAC file to a database in Azure SQL Database](/azure/sql-database/sql-database-import)  
[Delegated administrator access to Business Central online](delegated-admin.md)  
[Working with administration tools](administration.md)  
[The Business Central administration center](tenant-admin-center.md)  
[Managing environments](tenant-admin-center-environments.md)  
[Updating environments](tenant-admin-center-update-management.md)  
[Managing tenant notifications](tenant-admin-center-notifications.md)  
[Introduction to automation APIs](itpro-introduction-to-automation-apis.md)  
