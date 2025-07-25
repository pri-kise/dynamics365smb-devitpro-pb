---
title: "Upgrading Unmodified C/AL Application to Version 18"
description: Describes how to upgrade an unmodified Business Central 14 application to version 18
ms.custom: evergreen
ms.date: 04/18/2024
ms.update-cycle: 1095-days
ms.topic: how-to
ms.author: jswymer
author: jswymer
ms.reviewer: jswymer
---
# Upgrading Unmodified C/AL Application to Version 18

Use this scenario if you have a [!INCLUDE[prod_short](../developer/includes/prod_short.md)] Spring 2018 (version 14) application or earlier that doesn't include any code customization. Your solution might include Microsoft (first party) extensions and customization extensions (3rd-party). With this upgrade, you'll replace the C/AL base application with the new Microsoft System and Base Application extensions. The result will be a fully upgraded Business Central 2021 release wave 1 (version 18) application and platform.

 ![Upgrade on unmodified Business Central application.](../developer/media/bc14-to-18-upgrade-unmodified-app.png "Upgrade on unmodified Business Central application") 

## General information
 
### Single-tenant and multitenant deployments

[!INCLUDE[upgrade_single_vs_multitenant](../developer/includes/upgrade_single_vs_multitenant.md)]

### Personalization and customizations

[!INCLUDE[windows-client-upgrade](../developer/includes/windows-client-upgrade.md)]

## PowerShell variables used in tasks

Many of the steps in this article use PowerShell cmdlets, which require that you provide values for various parameters. To make it easier for copying or scripting in PowerShell, the steps use the following variables for parameter values. Replace the text between the `" "` with the correct values for your environment.

```powershell
$OldBcServerInstance = "The name of the Business Central server instance for your previous version, for example: BC140"
$NewBcServerInstance = "The name of the Business Central server instance for version 19, for example: BC190"
$TenantId = "The ID of the tenant to be upgraded. If not using a multitenant server instance, set the variable to default, or omit -Tenant parameter."
$TenantDatabase = "The name of the Business Central tenant database to be upgraded, for example: Demo Database BC (19-0)" 
$ApplicationDatabase = "The name of the Business Central application database in a multitenant environment, for example: My BC App DB. In a single-tenant deployment, this is the same as the $TenantDatabase" 
$DatabaseServer = "The SQL Server instance that hosts the databases. The value has the format server_name\instance_name, For example: localhost\BCDEMO"
$SystemAppPath = "The file path and name of the System Application extension for the update, for example: C:\DVD\Applications\system application\Source\\Microsoft_System Application.app"
$BaseAppPath = "The file path and name of the Base Application extension for the update, for example: C:\DVD\Applications\BaseApp\Source\Microsoft_Base Application.app"
$ApplicationAppPath = "The path and file name to the Application application extension for the update, for example: C:\DVD\Applications\Application\Source\Microsoft_Application.app"
$NewVersion = "The version number for the current System, Base, and Application extensions that you'll reinstall, for example: 18.1.24582.0"
$PartnerLicense = "The file path and name of the partner license"
$CustomerLicense = "The file path and name of the customer license"
$AddinsFolder = "The file path to the Add-ins folder of version 19 server installation, for example, C:\Program Files\Microsoft Dynamics 365 Business Central\190\Service\Add-ins."
```

## Prerequisite

- Upgrade to Business Central Spring 2019 (version 14).

   - If your solution is already on version 14, then no action on this step is required.
   - If you're upgrading from Business Central Fall 2018 (version 13) or Dynamics NAV, we recommend you upgrade to the latest update for version 14 that has a compatible update for version 18. For more information, see [[!INCLUDE[prod_long](../developer/includes/prod_long.md)] Upgrade Compatibility Matrix](upgrade-v14-v15-compatibility.md).

   To download the latest update, go to [Released Cumulative Updates for Microsoft Dynamics 365 Business Central Spring 2019 Update on-premises](https://support.microsoft.com/help/4501292).

   For information about how to do the upgrade, see [Upgrading to Dynamics 365 Business Central On-Premises](upgrading-to-business-central-on-premises.md).

<!-- 
2. Disable data encryption.

    If the current server instance uses data encryption, disable it. You can enable it again after upgrading.

    For more information, see [Managing Encryption and Encryption Keys](how-to-export-and-import-encryption-keys.md#encryption).

    Instead of disabling encryption, you can export the current encryption key, which you'll then import after upgrade. However, we recommend disabling encryption before upgrading.
-->

## Task 1: Install version 18

1. Download the latest available update for version 18 that is compatible with your version 14.

    To download the latest update, go to [Released Updates for Microsoft Dynamics 365 Business Central 2020 Release Wave 2 on-premises](https://support.microsoft.com/help/4528706).
  
    The guidelines in this article assume that you're running the latest available update.

2. Before you install version 18, it can be useful to create desktop shortcuts to the version 14.0 tools, such as the [!INCLUDE[admintool](../developer/includes/admintool.md)], [!INCLUDE[adminshell](../developer/includes/adminshell.md)], and [!INCLUDE[devshell](../developer/includes/devshell.md)] because the Start menu items for these tools will be replaced with the version 18 tools.

3. Install Business Central version 18 components.

    You'll have to keep version 14 installed to complete some steps in the upgrade process. When you install version 18, you must either specify different port numbers for components (like the [!INCLUDE[server](../developer/includes/server.md)] instance and web services) or stop the version 14.0 [!INCLUDE[server](../developer/includes/server.md)] instance before you run the installation. Otherwise, you'll get an error that the [!INCLUDE[server](../developer/includes/server.md)] failed to install.

    For more information, see [Installing Business Central Using Setup](../deployment/install-using-setup.md).

## Task 2: Upgrade permission sets

Version 18 introduces the capability to define permissions sets as AL objects, instead of as data. Permissions sets as AL objects is now the default and recommended model for permissions. For now, you can choose to use the legacy model, where permissions are defined and stored as data in the database. Whichever model you choose, there are permission set-related tasks you'll have to go through before and during upgrade.

For more information, see [Upgrading Permissions Sets and Permissions](upgrade-permissions.md)<!--[Permissions Upgrade Considerations](https://review.learn.microsoft.com/dynamics365/business-central/dev-itpro/developer/devenv-entitlements-and-permissionsets-overview?branch=permissionset#upgrade-considerations)-->.

## Task 3: Prepare version 14 databases

1. Make backup of the databases.
2. Disable data encryption.

    If the current server instance uses data encryption, disable it. You can enable it again after upgrading.

    For more information, see [Managing Encryption and Encryption Keys](how-to-export-and-import-encryption-keys.md#encryption).

    Instead of disabling encryption, you can export the current encryption key, which you'll then import after upgrade. However, we recommend disabling encryption before upgrading.
3. Start [!INCLUDE[adminshell](../developer/includes/adminshell.md)] for version 14 as an administrator.

   [!INCLUDE[open-admin-shell](../developer/includes/open-admin-shell.md)]
   
4. Uninstall all extensions from the old tenants.

    In this step, you uninstall any extensions that are currently installed on the database.

    1. Get a list of installed extensions.

        This step is optional, but it can be useful to the names and versions of the extensions.

        To get a list of installed extensions, use the [Get-NAVAppInfo cmdlet](/powershell/module/microsoft.dynamics.nav.apps.management/get-navappinfo).

        ```powershell 
        Get-NAVAppInfo -ServerInstance $OldBcServerInstance -Tenant $TenantId
        ```

        For a single-tenant deployment, set the `<tenant ID>` to default. 
    2. Uninstall the extensions.

        To uninstall an extension, you use the [Uninstall-NAVApp](/powershell/module/microsoft.dynamics.nav.apps.management/uninstall-navapp) cmdlet.

        ```powershell 
        Uninstall-NAVApp -ServerInstance $OldBcServerInstance -Name <extensions name> -Tenant <tenant ID> -Version <extension version> -Force
        ```

        Replace `<extension name>` and `<extension version>` with the exact name and version the published System Application.

        For example, together with the Get-NAVApp cmdlet, you can uninstall all extensions with a single command:

        ```powershell 
        Get-NAVAppInfo -ServerInstance $OldBcServerInstance -Tenant $TenantId| % { Uninstall-NAVApp -ServerInstance $OldBcServerInstance -Tenant $TenantId -Name $_.Name -Version $_.Version -Force}
        ``` 

5. Unpublish all extensions from the application server instance.

    To unpublish an extension, use the [Unpublish-NAVApp cmdlet](/powershell/module/microsoft.dynamics.nav.apps.management/unpublish-navapp):

    ```powershell 
    Unpublish-NAVApp -ServerInstance $OldBcServerInstance -Name <extension name> -Version <extension version>
    ```

    Together with the [Get-NAVAppInfo cmdlet](/powershell/module/microsoft.dynamics.nav.apps.management/get-navappinfo), you can unpublish all extensions by using a single command:

    ```powershell
    Get-NAVAppInfo -ServerInstance $OldBcServerInstance | % { Unpublish-NAVApp -ServerInstance $OldBcServerInstance -Name $_.Name -Version $_.Version }
    ```

6. Unpublish all system, test, and application symbols.

    To unpublish symbols, use the Unpublish-NAVAPP cmdlet with the `-SymbolsOnly` switch.

    ```powershell 
    Get-NAVAppInfo -ServerInstance $OldBcServerInstance -SymbolsOnly | % { Unpublish-NAVApp -ServerInstance $OldBcServerInstance -Name $_.Name -Version $_.Version }
    ```

    [What are symbols?](upgrade-overview-v15.md#Symbols)  
7. (Multitenant only) Dismount the tenants from the application server instance.

    To dismount a tenant, use the [Dismount-NAVTenant](/powershell/module/microsoft.dynamics.nav.management/dismount-navtenant) cmdlet:

    ```powershell
    Dismount-NAVTenant -ServerInstance $OldBcServerInstance -Tenant $TenantId
    ```

8. Stop the server instance.

    ```powershell
    Stop-NAVServerInstance -ServerInstance $OldBcServerInstance
    ```

## Task 4: Convert the application database to version 18

This task runs a technical upgrade on the application database to convert it from the version 14 platform to the version 18 platform. The conversion updates the system tables of the database to the new schema (data structure). It provides the latest platform features and performance enhancements. The conversion adds the system symbols for the version to the database, so you don't have to manually publish the Systems extension, as you had to do with early releases.

[!INCLUDE[convert_azure_sql_db](../developer/includes/convert_azure_sql_db.md)]
2. Start [!INCLUDE[adminshell](../developer/includes/adminshell.md)] for version 18 as an administrator.
3. Run the Invoke-NAVApplicationDatabaseConversion cmdlet to start the conversion:

    ```powershell
    Invoke-NAVApplicationDatabaseConversion -DatabaseServer $DatabaseServer -DatabaseName $ApplicationDatabase
    ```

    When completed, a message like the following displays in the console:

    ```powershell
    DatabaseServer      : .\BCDEMO
    DatabaseName        : Demo Database BC (14-0)
    DatabaseCredentials :
    DatabaseLocation    :
    Collation           :
    ```

[!INCLUDE[convert_azure_sql_db_timeout](../developer/includes/convert_azure_sql_db_timeout.md)]

## Task 5: Configure version 18 server for DestinationAppsForMigration

When you installed version 18 in **Task 1**, a version 18 [!INCLUDE[server](../developer/includes/server.md)] instance was created. In this task, you change server configuration settings that are required to complete the upgrade. Some of the changes are only required for version 14 to version 18.0 upgrade and can be reverted after you complete the upgrade.

1. Set the server instance to connect to the application database.

    ```powershell
    Set-NAVServerConfiguration -ServerInstance $NewBcServerInstance -KeyName DatabaseName -KeyValue $ApplicationDatabase
    ```

    In a single tenant deployment, this command will mount the tenant automatically. For more information, see [Connecting a Server Instance to a Database](../administration/connect-server-to-database.md).

2. Configure the server instance for migrate extensions to the use the new base application and system application extensions. 

    ```powershell
    Set-NAVServerConfiguration -ServerInstance $NewBcServerInstance -KeyName "DestinationAppsForMigration" -KeyValue '[{"appId":"63ca2fa4-4f03-4f2b-a480-172fef340d3f", "name":"System Application", "publisher": "Microsoft"},{"appId":"437dbf0e-84ff-417a-965d-ed2bb9650972", "name":"Base Application", "publisher": "Microsoft"}]'
    ```

    This setting serves the following purposes:

    - When you run the data upgrade on a tenant, the server will run the data upgrade for the base and system application extensions. The base and system applications will be automatically installed on the tenant also.
    - Lets you republish extensions that haven't been built on version 18. The extensions typically include the third-party extensions that were used in your version 14. When you publish the extensions, the extension manifests are automatically modified with a dependency on the base and system applications.

    For more information about this setting, see [DestinationAppsForMigration](upgrade-destinationappsformigration.md).

3. If you want to use the permission sets defined as data, set the `UserPermissionSetsFromExtensions` setting to `false`.

    ```powershell
    Set-NavServerConfiguration -ServerInstance $NewBcServerInstance -KeyName "UsePermissionSetsFromExtensions" -KeyValue false
    ```

4. Disable task scheduler on the server instance for purposes of upgrade.

    ```powershell
    Set-NavServerConfiguration -ServerInstance $NewBcServerInstance -KeyName "EnableTaskScheduler" -KeyValue false
    ```

    Be sure to re-enable task scheduler after upgrade if needed.
5. Restart the server instance.

    ```powershell
    Restart-NAVServerInstance -ServerInstance $NewBcServerInstance
    ```

## <a name="UploadLicense"></a>Task 6: Import version 18 license

If you've gotten a new [!INCLUDE[prod_short](../developer/includes/prod_short.md)] partner license, make sure that it has been uploaded to the database.

1. To upload the license, use the [Import-NAVServerLicense cmdlet](/powershell/module/microsoft.dynamics.nav.management/import-navserverlicense):

    ```powershell
    Import-NAVServerLicense -ServerInstance $NewBcServerInstance -LicenseFile $PartnerLicense
    ```

2. Restart the server instance.

    ```powershell
    Restart-NAVServerInstance -ServerInstance $NewBcServerInstance
    ```

For more information, see [Uploading a License File for a Specific Database](../cside/cside-upload-license-file.md#UploadtoDatabase).

## Task 7: Publish extensions

In this task, you'll publish the extensions. As minimum, you publish the new base application and system application extensions from the installation media (DVD). You also publish new versions of any Microsoft extensions and third-party extensions that were used on your old deployment.

Publishing an extension adds the extension to the application database that is mounted on the server instance. Once published, it's available for installing on tenants. This task updates internal tables, compiles the components of the extension behind-the-scenes, and builds the necessary metadata objects that are used at runtime.

The steps in this task continue to use the [!INCLUDE[adminshell](../developer/includes/adminshell.md)] for version 18 that you started in the previous task.

<!--
1. Publish version 18 system symbols extension.

    The symbols extension contains the required platform symbols that the base application depends on. The symbols extension package is called **System.app**. You find it where the **AL Development Environment** is installed. The default path is C:\Program Files (x86)\Microsoft Dynamics 365 Business Central\180\AL Development Environment.  

    ```powershell
    Publish-NAVApp -ServerInstance  <server instance name> -Path "<path to system.app>" -PackageType SymbolsOnly
    ```

    [What are symbols?](upgrade-overview-v15.md#Symbols)
-->
1. Publish the **System Application** extension (Microsoft_System Application.app).

    You find the (Microsoft_System Application.app in the **Applications\System Application\Source** folder of installation media (DVD).

    ```powershell
    Publish-NAVApp -ServerInstance $NewBcServerInstance -Path $SystemAppPath
    ```

    [What is the System Application?](upgrade-overview-v15.md#SystemApplication)
2. Publish the Business Central base application extension (Microsoft_Base Application.app).

    The **Base Application** extension contains the application business objects. You find the (Microsoft_Base Application.app in the **Applications\BaseApp\Source** folder of installation media (DVD).

    ```powershell
    Publish-NAVApp -ServerInstance $NewBcServerInstance -Path $BaseAppPath
    ```

3. Publish the Microsoft_Application extension

    The Microsoft_Application extension is a new extension introduced in 15.3. For more information about this extension, see [The Microsoft_Application.app File](../developer/devenv-application-app-file.md).

    ```powershell
    Publish-NAVApp -ServerInstance $NewBcServerInstance -Path $ApplicationAppPath
    ```

4. Publish the new versions of Microsoft extensions.

    In this step, you publish new versions of Microsoft extensions that were used on your version 14 deployment. You find the extensions in the **Applications** folder of the installation media (DVD).

    ```powershell
    Publish-NAVApp -ServerInstance $NewBcServerInstance -Path "<path to Microsoft extension>"
    ```

    For example:

    ```powershell
    Publish-NAVApp -ServerInstance $NewBcServerInstance -Path "C:\W1DVD\Applications\SalesAndInventoryForecast\Source\SalesAndInventoryForecast.app"
    ```

5. Publish 3rd-party extensions.

    Publish 3rd-party extensions that were used on your version 14 solution. If you have new versions of these extensions, built on the Business Central version 18, then publish the new versions. Otherwise, republish the same versions that were previously published in the old deployment.  

    ```powershell
    Publish-NAVApp -ServerInstance $NewBcServerInstance -Path "<path to extension>"
    ```

## Task 8: Restart server instance

Restart the [!INCLUDE[server](../developer/includes/server.md)] to free up resources for completing the upgrade.

```powershell
Restart-NAVServerInstance -ServerInstance $NewBcServerInstance
```

This step is important, otherwise you might experience issues when you run the data upgrade.

## Task 9: Synchronize tenant

In this task, you'll synchronize the tenant's database schema with any schema changes in the application database and extensions.

If you have a multitenant deployment, do these steps for each tenant.

1. (Multitenant only) Mount the tenant to the version 18 server instance.

    To mount the tenant, use the [Mount-NAVTenant](/powershell/module/microsoft.dynamics.nav.management/mount-navtenant) cmdlet:

    ```powershell
    Mount-NAVTenant -ServerInstance $NewBcServerInstance -DatabaseServer $DatabaseServer -DatabaseName $TenantDatabase -Tenant $TenantDatabase -AllowAppDatabaseWrite
    ```

    > [!IMPORTANT]
    > You must use the same tenant ID for the tenant that was used in the old deployment; otherwise you'll get an error when mounting or syncing the tenant. If you want to use a different ID for the tenant, you can either use the `-AlternateId` parameter now or after upgrading, dismount the tenant, then mount it again using the new ID and the `OverwriteTenantIdInDatabase` parameter.  
    >  
    > For upgrade, set the `-AllowAppDatabaseWrite` parameter. After upgrade, you can dismount and mount the tenant again without the parameter if needed.

    At this stage, the tenant state is OperationalWithSyncPending.
2. Synchronize the tenant with the application database.

    Use the [Sync-NAVTenant](/powershell/module/microsoft.dynamics.nav.management/sync-navtenant) cmdlet:

    ```powershell  
    Sync-NAVTenant -ServerInstance $NewBcServerInstance -Tenant $TenantId -Mode Sync
    ```

    With a single-tenant deployment, you can omit the `-Tenant` parameter and value.

3. Synchronize the tenant with the **System Application** extension. 

    Use the [Sync-NAVApp](/powershell/module/microsoft.dynamics.nav.apps.management/sync-navapp) cmdlet:

    ```powershell
    Sync-NAVApp -ServerInstance $NewBcServerInstance -Tenant $TenantId -Name "System Application" -Version $NewVersion
    ```

    Replace `<extension version>` with the exact version of the published System Application. To get the version, you can use the [Get-NAVAppInfo cmdlet](/powershell/module/microsoft.dynamics.nav.apps.management/get-navappinfo).

4. Synchronize the tenant with the **Base Application** extension.

    ```powershell
    Sync-NAVApp -ServerInstance $NewBcServerInstance -Tenant <tenant ID> -Name "Base Application" -Version $NewVersion
    ```

   Replace `<extension version>` with the exact version of the published Base Application.

5. Synchronize the tenant with the **Application** extension.

    ```powershell
    Sync-NAVApp -ServerInstance $NewBcServerInstance -Tenant $TenantId -Name "Application" -Version $NewVersion
    ```

6. Synchronize the tenant with Microsoft and 3rd-party extensions.

    For each extension, run the Sync-NAVApp cmdlet:

    ```powershell
    Sync-NAVApp -ServerInstance $NewBcServerInstance -Tenant $TenantId -Name "<extension name>" -Version $NewVersion
    ```

> [!TIP]
> When you synchronize an extension, the extension takes ownership of any tables that it includes. In SQL Server, you'll notice that the table names will be suffixed with the extension ID. For example, Base Application tables will have `437dbf0e-84ff-417a-965d-ed2bb9650972` in the name. In addition, the systemId column is added to application tables that are not already part of an extension.

## Task 10: Upgrade data

In this task, you run a data upgrade on tables to handle data changes made by platform and extensions.

If you have a multitenant deployment, do these steps for each tenant.

1. Upgrade the data to the platform, system application, and base application.

    1. To run the data upgrade, use the [Start-NavDataUpgrade](/powershell/module/microsoft.dynamics.nav.management/start-navdataupgrade) cmdlet:

        ```powershell
        Start-NAVDataUpgrade -ServerInstance $NewBcServerInstance -Tenant $TenantId -FunctionExecutionMode Serial -SkipAppVersionCheck
        ```

        <!--You only need to use the -SkipAppVersionCheck if you didn't increase the application version in Task 5.--> 
    2. To view the progress of the data upgrade, you can run Get-NavDataUpgrade cmdlet with the `–Progress` switch.

    This step will automatically install the base application and system application on the tenant.

2. Upgrade the new versions of Microsoft extensions and third-party extensions.

    Complete this task to upgrade any Microsoft extension and third-party extension. Microsoft extensions used in the old deployment to new versions on the installation media. The new versions are in the **Application** folder of the DVD. There's a folder for each extension. The extension package (.app file) is in the **Source** folder. 

    1. Install **Application** extension.

        You'll have to install the **Application** extension first, otherwise you can't upgrade Microsoft extensions.

        ```powershell
        Install-NAVApp -ServerInstance $NewBcServerInstance -Tenant $TenantId -Name "Application" -Version $NewVersion
        ```

    2. For each extension, run [Start-NAVAppDataUpgrade cmdlet](/powershell/module/microsoft.dynamics.nav.apps.management/start-navappdataupgrade):

        ```powershell
        Start-NAVAppDataUpgrade -ServerInstance $NewBcServerInstance -Name "<extension name>" -Version "<extension version>"
        ```

        This step will also automatically install the new extension version on the tenant.

3. (Multitenant only) Repeat steps 1 through 3 for each tenant.

## Task 11: Install 3rd-party extensions

Complete this task to install third-party extensions for which a new version wasn't published. For each extension, run the [Install-NAVApp cmdlet](/powershell/module/microsoft.dynamics.nav.apps.management/install-navapp):

```powershell
Install-NAVApp -ServerInstance $NewBcServerInstance -Name <extension name> -Version <extension version>
```

## Task 12: <a name="JSaddins"></a>Upgrade control add-ins

The [!INCLUDE[server](../developer/includes/server.md)] installation includes new versions of the Microsoft-provided Javascript-based control add-ins, like Microsoft.Dynamics.Nav.Client.BusinessChart, Microsoft.Dynamics.Nav.Client.VideoPlayer, and more. If your solution uses any of these control add-ins, upgrade them to the latest version.

To upgrade the control add-ins from the client, do the following steps:

1. Open the [!INCLUDE[prod_short](../developer/includes/prod_short.md)] client.
2. Search for and open the **Control Add-ins** page.
3. Choose **Actions** > **Control Add-in Resource** > **Import**.
4. Locate and select the .zip file for the control add-in and choose **Open**.

    The .zip files are located in the **Add-ins** folder of the [!INCLUDE[server](../developer/includes/server.md)] installation. There's a subfolder for each add-in. For example, the path to the Business Chart control add-in is `C:\Program Files\Microsoft Dynamics 365 Business Central\180\Service\Add-ins\BusinessChart\Microsoft.Dynamics.Nav.Client.BusinessChart.zip`.
5. After you've imported all the new control add-in versions, restart Business Central Server instance.

Alternatively, you can use the [Set-NAVAddin cmdlet](/powershell/module/microsoft.dynamics.nav.management/set-navaddin) of the [!INCLUDE[adminshell](../developer/includes/adminshell.md)]. For example, the following commands update the control add-ins installed by default. Modify the commands to suit:

```powershell
Set-NAVAddIn -ServerInstance $NewBcServerInstance -AddinName 'Microsoft.Dynamics.Nav.Client.BusinessChart' -PublicKeyToken 31bf3856ad364e35 -ResourceFile ($AppName = Join-Path $AddinsFolder 'BusinessChart\Microsoft.Dynamics.Nav.Client.BusinessChart.zip')
Set-NAVAddIn -ServerInstance $NewBcServerInstance -AddinName 'Microsoft.Dynamics.Nav.Client.FlowIntegration' -PublicKeyToken 31bf3856ad364e35 -ResourceFile ($AppName = Join-Path $AddinsFolder 'FlowIntegration\Microsoft.Dynamics.Nav.Client.FlowIntegration.zip')
Set-NAVAddIn -ServerInstance $NewBcServerInstance -AddinName 'Microsoft.Dynamics.Nav.Client.OAuthIntegration' -PublicKeyToken 31bf3856ad364e35 -ResourceFile ($AppName = Join-Path $AddinsFolder 'OAuthIntegration\Microsoft.Dynamics.Nav.Client.OAuthIntegration.zip')
Set-NAVAddIn -ServerInstance $NewBcServerInstance -AddinName 'Microsoft.Dynamics.Nav.Client.PageReady' -PublicKeyToken 31bf3856ad364e35 -ResourceFile ($AppName = Join-Path $AddinsFolder 'PageReady\Microsoft.Dynamics.Nav.Client.PageReady.zip')
Set-NAVAddIn -ServerInstance $NewBcServerInstance -AddinName 'Microsoft.Dynamics.Nav.Client.PowerBIManagement' -PublicKeyToken 31bf3856ad364e35 -ResourceFile ($AppName = Join-Path $AddinsFolder 'PowerBIManagement\Microsoft.Dynamics.Nav.Client.PowerBIManagement.zip')
Set-NAVAddIn -ServerInstance $NewBcServerInstance -AddinName 'Microsoft.Dynamics.Nav.Client.RoleCenterSelector' -PublicKeyToken 31bf3856ad364e35 -ResourceFile ($AppName = Join-Path $AddinsFolder 'RoleCenterSelector\Microsoft.Dynamics.Nav.Client.RoleCenterSelector.zip')
Set-NAVAddIn -ServerInstance $NewBcServerInstance -AddinName 'Microsoft.Dynamics.Nav.Client.SatisfactionSurvey' -PublicKeyToken 31bf3856ad364e35 -ResourceFile ($AppName = Join-Path $AddinsFolder 'SatisfactionSurvey\Microsoft.Dynamics.Nav.Client.SatisfactionSurvey.zip')
Set-NAVAddIn -ServerInstance $NewBcServerInstance -AddinName 'Microsoft.Dynamics.Nav.Client.SocialListening' -PublicKeyToken 31bf3856ad364e35 -ResourceFile ($AppName = Join-Path $AddinsFolder 'SocialListening\Microsoft.Dynamics.Nav.Client.SocialListening.zip')
Set-NAVAddIn -ServerInstance $NewBcServerInstance -AddinName 'Microsoft.Dynamics.Nav.Client.VideoPlayer' -PublicKeyToken 31bf3856ad364e35 -ResourceFile ($AppName = Join-Path $AddinsFolder 'VideoPlayer\Microsoft.Dynamics.Nav.Client.VideoPlayer.zip')
Set-NAVAddIn -ServerInstance $NewBcServerInstance -AddinName 'Microsoft.Dynamics.Nav.Client.WebPageViewer' -PublicKeyToken 31bf3856ad364e35 -ResourceFile ($AppName = Join-Path $AddinsFolder 'WebPageViewer\Microsoft.Dynamics.Nav.Client.WebPageViewer.zip')
Set-NAVAddIn -ServerInstance $NewBcServerInstance -AddinName 'Microsoft.Dynamics.Nav.Client.WelcomeWizard' -PublicKeyToken 31bf3856ad364e35 -ResourceFile ($AppName = Join-Path $AddinsFolder 'WelcomeWizard\Microsoft.Dynamics.Nav.Client.WelcomeWizard.zip')
```

At this point, the upgrade is complete, and you can open the client.

## Task 13: Install upgraded permissions sets

In this task, you install the custom permission sets that you upgraded earlier in this procedure. The steps depend on whether you've decided to use permission sets as AL objects or as data.

### For permission sets as AL objects

1. Publish the extension or extensions that include the permission sets.
2. Sync the extensions with the tenant.
3. Install the extensions on the tenant.

### For permission sets as data in XML

1. Open the [!INCLUDE[webclient](../developer/includes/webclient.md)].
2. Search for and open the **Permission Sets** page.
3. Select **Import Permission Sets**, and follow the instructions to import the XML file.

For more information, see [To export and import a permission set](/dynamics365/business-central/ui-define-granular-permissions#to-export-and-import-a-permission-set).

## Task 14: Change application version

This task serves two purposes. It ensures that personalization works as expected after upgrade. It's also useful for support purposes and answering a common question about the application version.  

On the **Help and Support** page in the client, you'll see an application version, such as 14.0.2345.6. For an explanation of the number, see [Version numbers in Business Central](../administration/version-numbers.md). This version isn't updated automatically when you install an update. If you want the version to reflect the version of the update or your own version, you change it manually.

We recommend setting the value to application build number for the version 17 update. You get the number from theYou get the number from the [Released Updates for Microsoft Dynamics 365 Business Central 2021 Release Wave 1 on-premises](https://support.microsoft.com/help/4528706). <!--TODO fix link-->.

1. Run the [Set-NAVApplication cmdlet](/powershell/module/microsoft.dynamics.nav.management/set-navapplication):

   ```powershell
   Set-NAVApplication -ServerInstance $NewBcServerInstance -ApplicationVersion $NewVersion -Force
   ```

   For example:

   ```powershell
   Set-NAVApplication -ServerInstance BC180 -ApplicationVersion 18.0.38071.0 -Force
   ```

2. Run the [Sync-NAVTenant](/powershell/module/microsoft.dynamics.nav.management/sync-navtenant) cmdlet to synchronize the tenant with the application database.

   ```powershell  
   Sync-NAVTenant -ServerInstance $NewBcServerInstance -Mode Sync -Tenant $TenantId
   ```

   With a single-tenant deployment, you can omit the `-Tenant` parameter and value.

3. Run the [Start-NavDataUpgrade](/powershell/module/microsoft.dynamics.nav.management/start-navdataupgrade) cmdlet to change the version number:

   ```powershell
   Start-NAVDataUpgrade -ServerInstance $NewBcServerInstance -FunctionExecutionMode Serial -Tenant $TenantId
   ```

The data upgrade process will be running in the background after running the above Start-NAVDataUpgrade cmdlet. You check on the progress using the Get-NAVDataUpgrade cmdlet: such as: `Get-NAVDataUpgrade -ServerInstance $NewBcServerInstance -Tenant $TenantId -Progress` or `Get-NAVDataUpgrade -ServerInstance $NewBcServerInstance -Tenant $TenantId -Detailed`.

Don't stop the [!INCLUDE[server](../developer/includes/server.md)] instance until the process is complete, that is, when you see `State = Operational` in the results from the Get-NAVDataUpgrade cmdlet. Also, you won't be able to do operations, like installing extensions, until the state is operational. It can take several minutes before the process completes.

## Post-upgrade tasks

1. Enable task scheduler on the server instance.
2. (Multitenant only) For tenants other than the tenant that you use for administration purposes, if you mounted the tenants using the `-AllowAppDatabaseWrite` parameter, dismount the tenants, then mount them again without using the `-AllowAppDatabaseWrite` parameter.
3. If you want to use data encryption as before, enable it.

   For more information, see [Managing Encryption and Encryption Keys](how-to-export-and-import-encryption-keys.md#encryption).

   Optionally, if you exported the encryption key instead of disabling encryption earlier, import the encryption key file to enable encryption.

4. Grant users permission to the *Open in Excel* and *Edit in Excel* actions.

    Version 18 introduces a system permission that protects these two actions. The permission is granted by the system object **6110 Allow Action Export To Excel**. Because of this change, users who had permission to these actions before upgrading, will lose permission. To grant permission again, do one of the following steps:

    - Assign the **EXCEL EXPORT ACTION** permission set to appropriate users. 
    - Add the system object **6110 Allow Action Export To Excel** permission directly to appropriate permission sets.

     For more information about working with permission sets and permissions, see [Export and Import Permission Sets](/dynamics365/business-central/ui-define-granular-permissions#to-export-and-import-a-permission-set).
5. Complete the setup of the integration with Dynamics 365 Sales.

    If your [!INCLUDE [prod_short](../includes/prod_short.md)] on-premises deployment had an active connection with Dynamics 365 Sales, you must perform the following steps to complete the setup of the connection in [!INCLUDE [prod_short](../includes/prod_short.md)] online:

    - Open the **Microsoft Dynamics 365 Connection Setup** page.  
    - To upgrade the connection to use certificate-based authentication, choose **Connection**, and then choose the **Use Certificate Authentication** action.  
    - Sign in with the administrator credentials for the connected Dynamics 365 Sales organization. Signing in and the subsequent setup of the certificate authentication should take less than a minute.  

        > [!NOTE]
        > This is a required step. For more information, see [Upgrade Connections from Business Central Online to Use Certificate-Based Authentication](/dynamics365/business-central/admin-how-to-set-up-a-dynamics-crm-connection#upgrade-connections-from-business-central-online-to-use-certificate-based-authentication) in the business functionality content.
    - Once the setup of certificate authentication is done, choose **Cloud Migration**, and then choose **Rebuild Coupling Table**.  

        This will schedule the rebuilding of the coupling table and will open the corresponding job queue entry, so you can monitor its progress and restart it if it ends up in error state.  

        > [!NOTE]
        > The step for rebuilding the coupling table is not needed if you have performed cloud migration from [!INCLUDE [prod_short](../includes/prod_short.md)] version 15 or later.

## Related information  

[Publishing and Installing an Extension](../developer/devenv-how-publish-and-install-an-extension-v2.md)  
[Upgrading to Business Central](upgrading-to-business-central.md)  
[Upgrading Extensions](../developer/devenv-upgrading-extensions.md)  
