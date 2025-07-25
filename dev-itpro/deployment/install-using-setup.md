---
title: Install Business Central Using Setup
description: Install Business Central using Setup to quickly deploy production, demo, or development environments. Follow step-by-step guidance.
ms.date: 06/20/2025
ms.topic: install-set-up-deploy
ms.author: jswymer
author: jswymer
ms.custom: bap-template
ms.reviewer: jswymer
---

# Installing Business Central using Setup

You use [!INCLUDE[prodsetup](../developer/includes/prodsetup.md)] to install the different components that comprise a [!INCLUDE[prod_short](../developer/includes/prod_short.md)] production, demonstration, or development environment. For a list of components, see [Components and Topology](product-and-architecture-overview.md).

## About Setup

Setup is available on the installation media (DVD) in the setup.exe file. When you run the setup.exe file, a wizard leads you through installation process. You can install individual components or select predefined options that install a logical collection of components.

### Configuration settings

During Setup, you're presented with various configuration settings. Some settings require that you set them. Other  settings have a default value. The default value in many cases is sufficient for the initial installation. After you run Setup, you can change the configuration settings by using other tools like the [!INCLUDE[adminshell](../developer/includes/adminshell.md)].

### Prerequisite installations by Setup

There are some components that require other software to run. For example, the database requires SQL Server and the Web client requires Internet Information Services (IIS). Setup installs several of these prerequisites, like installing SQL Server Express and enabling IIS. You can view which prerequisites Setup installs in the [System Requirements](system-requirement-business-central.md).  

## Downloading [!INCLUDE[prod_short](../developer/includes/prod_short.md)] for installation 

[!INCLUDE[prod_short](../developer/includes/prod_short.md)] is available for downloading from Microsoft Support. For each major release, minor updates are published regularly. The downloaded files contain the installation media, which includes the setup.exe file.

> [!IMPORTANT]
> We recommend that you install the latest update for the release you want to install. However, if you're installing a version for upgrade, make sure that you choose a target version that is compatible with the version that you'll be upgrading. For more information, see [Dynamics 365 Business Central Upgrade Compatibility Matrix](../upgrade/upgrade-v14-v15-compatibility.md).

### Download the files

1. Go to the update page for the release that you want to install:

   - [Business Central 2025 Release Wave 1 (v26)](https://support.microsoft.com/en-us/topic/released-updates-for-microsoft-dynamics-365-business-central-2025-release-wave-1-8cb260a4-6a57-4325-a4d7-7aa84c3234e6)
   - [Business Central 2024 Release Wave 2 (v25)](https://support.microsoft.com/en-us/topic/released-updates-for-microsoft-dynamics-365-business-central-2024-release-wave-2-a8fc49a4-610e-4123-8bcd-a0aa5f3c9776)
   - [Business Central 2024 Release Wave 1 (v24)](https://support.microsoft.com/en-us/topic/released-updates-for-microsoft-dynamics-365-business-central-2024-release-wave-1-0b644dfa-2eef-4f3e-9d77-bc92dbaafb65)

2. In the **Cumulative Updates** table, select the link in the **Knowledge Base ID** column for the update you want.
3. In the **Resolution** section, select the link under  **How to obtain the Microsoft Dynamics 365 Business Central \<release\> files**.
4. Follow the instructions.

## Before you run Setup

1. Plan your deployment and identify the components that you want to install.
2. Verify that the target computer meets the hardware and software requirements for the components that you want to install. For more information, see [System Requirements](system-requirement-business-central.md).
3. Make sure that you're an administrator on the computer where you run Setup.
4. Determine the HTTP ports to use for components.

    During setup, you have to specify the following HTTP ports:

    |Port | Description|Default|
    |--------|------------|-------|
    | Management services| The listening Transmission Control Protocol (TCP) port for the Business Central Server administration.|7045|
    | Client services| The listening HTTP port for client services.|7085 (7046 in version 20 and earlier)|
    | SOAP services| The listening HTTP port for SOAP web services.|7047|
    | OData service|The listening HTTP port for OData web services.|7048|
    | Developer services|The listening HTTP port for Microsoft Dynamics NAV Developer web services|7049|

    Select a port number that isn't being used by another service. Or, if you use a port that's used by another service, stop the other service before you run setup. For example, if you have an older version of [!INCLUDE[nav_server_md.md](../developer/includes/nav_server_md.md)] or [!INCLUDE[server](../developer/includes/server.md)] installed, then configure the new server instance to use other ports than the old server instance, or stop the old server instance before you install the new one.

## Run Setup

<!--
 or to capture a set of custom setup settings to save in a setup configuration file. In this procedure, you run [!INCLUDE[prodsetup](../developer/includes/prodsetup.md)] without any customization or configuration. Opportunities for customization and configuration are described throughout the procedure.
-->  
1. In the installation media (DVD) folder, double-click the setup.exe.
2. Follow the setup until you get to the **[!INCLUDE[prod_long](../developer/includes/prod_long.md)]** page.

    <!--![Business Central Setup.](../media/setup.png "Business Central Setup")-->

    - Choose **Get a free online trial to sign up**  if you interested in hearing about and trying the cloud experience.
    - Choose **Get the Business Central app from the Microsoft Store** to download a companion app that mimics that Web client but has the same look-and-feel as the mobile apps. For more information, see [Installing the Microsoft Dynamics 365 Business Central App](install-business-central-app.md). 
    
       This option applies to Business Central 2021 release wave 2 (v19) and earlier only.
    - Choose **Advance installation options** to install a demonstration environment or individual components. Then, follow the on-screen instructions to complete the installation.

<!-- 
## <a name="sqlclient"></a>Install prerequisites for [!INCLUDE[nav_dev_long](../developer/includes/nav_dev_long_md.md)] (Business Central 2019 only)

Starting with cumulative update 24 (version 14.25), SQL Server Native Client is no longer be installed by Setup or included on the installation media (DVD). This change doesn't affect the [!INCLUDE[nav_dev_long](../developer/includes/nav_dev_long_md.md)] installation if you upgrading from an earlier version, because the prerequisite should already have been installed. However, for a clean installation of the [!INCLUDE[nav_dev_long](../developer/includes/nav_dev_long_md.md)], you'll have to manually install the SQL Server Native Client; otherwise, you may experience problems connecting the [!INCLUDE[nav_dev_long](../developer/includes/nav_dev_long_md.md)] to the database.   To install the SQL Server Native Client, follow these steps:

1. From [Released Cumulative Updates for Business Central Spring 2019](https://support.microsoft.com/help/4501292), download an earlier cumulative update to the computer where you're installing [!INCLUDE[nav_dev_short](../developer/includes/nav_dev_short_md.md)].
2. Unzip the files.
3. Open the **DVD\Prerequisite Components\Microsoft SQL Server folder**, then double-click either the sqlncli.msi or sqlncli64.msi, depending on whether the computer has an 86-bit or 64-bit operating system respectively.
4. Follow the instructions.-->

## Cancel Setup

Setup doesn't provide a **Cancel** button on all pages. But, you can cancel the installation from any page by choosing the **Close** button. All [!INCLUDE[prod_short](../developer/includes/prod_short.md)] components are removed from the computer. The only software that Setup can't remove are:  

- Database files, such as the Demo database.  

- Prerequisites for [!INCLUDE[prod_short](../developer/includes/prod_short.md)] components that Setup can install, such as the .NET Framework.

## Run Setup from a command prompt

You can run [!INCLUDE[prodsetup](../developer/includes/prodsetup.md)] from a command prompt. Before installation, run it from the installation media. After the initial installation, you can run it from the location where it's  automatically stored on your computer. The default location is:  
  
```  
C:\Program Files (x86)\Common Files\Microsoft Dynamics 365 Business Central\<Version number>\Setup  
```  

You can use the following options with Setup.exe.  
  
|Setup option|Description|  
|------------------|-----------------|  
|**/config \<Setup config file>**|Specifies path and file name information for a Setup configuration file to load.<br /><br /> This option is the only required option.|  
|**/help**|Displays Help about Setup.exe options.|  
|**/log \<log path>**|Specifies path and file name information for a Setup log file created by Setup. The file must not exist before you run Setup.|  
|**/quiet**|Specifies that Setup doesn't display anything on the screen. All configuration information is taken from the specified configuration file.|  
|**/repair**|Repairs the current installation of [!INCLUDE[prod_short](../developer/includes/prod_short.md)].|  
|**/uninstall**|Removes the current installation [!INCLUDE[prod_short](../developer/includes/prod_short.md)].|

## Save, edit, and load a setup configuration file

During Setup, you can save the configuration settings to a file before you finish. Then later, you can load the configuration file. Using a configuration file makes it faster to replicate the same configuration for another deployment.

#### Save to a setup configuration file

1. Select **Save** on the **Specify parameters** page in Setup. This page is available when you run Setup unless you select **Install Demo**, which skips all other Setup pages.  
  
2. Type a file name for the configuration file. An .xml extension is added automatically.  
  
3. Select  **Save**.  
  
     You now return to the **Specify parameters** page, where you can continue with installing software. You can also close Setup if you only have to create a Setup configuration file. 

#### Edit a setup configuration file

You edit the file using an XML editor or text editor. Setup configuration files contain two types of settings.  
  
|Setting type|Purpose|  
|------------------|-------------|  
|Component|For each component, there are three separate values, all displayed on a single line:<br /><br /> -   **ShowOptionNode**<br />     Specifies whether the component should be displayed in Setup. For silent installs, this parameter isn't relevant.<br />-   **State**<br />     There are two possible values: **Local**, indicates that the component is included in the install. **Absent** indicates that the component isn't included.<br />-   **Id**<br />     Identifies the component<br /><br /> You can change value for **State** or **ShowOptionNode**, but not for **Id**. Also, you can't add or remove a component.|  
|Parameter|These settings contain configuration information for components. As with Components, you can modify a parameter's **Value**, but not its **Id**. |  

#### Load a setup configuration file
  
The option to load a setup configuration file is on the **Choose an installation option** page in setup.

> [!NOTE] 
> If you're using a setup configuration file from an earlier [!INCLUDE[prod_short](../developer/includes/prod_short.md)] version, there might be some elements no longer supported because the feature is deprecated. For example, the elements that have the following IDs are no longer supported as on 2019 release wave 2: "RoleTailoredClient", "ExcelAddin, "ClassicClient",
 "ClickOnceInstallerTools", "STOutlookIntegration", "PublicWinBaseUrl", and "ACSUri".

1. On the **Choose an installation option** page, select **Load Configuration**.  
  
    This option is located under **Custom Components**.

    > [!IMPORTANT]  
    > A Setup configuration file contains information about which components to install and which settings to apply to each component. Therefore, you shouldn't customize the list of components or configure components in Setup before you load a Setup configuration file because loading the configuration overwrites all prior customization and configuration.
  
2. In the **Open** dialog box, select or browse to the Setup configuration file that you want to open. Then, double-click the file.  
  
     Setup now shows the **Customize the installation** page, modified according to the component selection in the configuration file.  
  
3. Modify the list of components to install or select **Next** to continue to the **Specify parameters** page.
  
4. Configure these settings or select **Apply** to accept these values and continue.

## Troubleshooting

[!INCLUDE[upgrade_known_issues](../developer/includes/upgrade_known_issues.md)]

## Related information

 [Components](product-and-architecture-overview.md)  
 [Deployment](deployment.md)  
