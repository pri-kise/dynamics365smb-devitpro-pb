---
title: Lifecycle of apps and extensions
description: Overview of the process of updating an app for Business Central.
author: SusanneWindfeldPedersen
ms.topic: overview
ms.author: solsen
ms.date: 06/02/2025
ms.reviewer: solsen
---

# The lifecycle of apps and extensions for Business Central

When you build an app or extension to [!INCLUDE[prod_short](includes/prod_short.md)] and get that published to AppSource, it becomes an app like so many others - the app itself can be updated, and the platform that it sits on, [!INCLUDE[prod_short](includes/prod_short.md)] online itself, also gets updated. But what happens after your app gets published?

When your app has passed all of our validations and is live on AppSource, customers can install your extension and use it for their business. But you're expected to keep it compliant with the service and update it if something changes.  

The following sections describe the different upgrade scenarios that we see play out as we update [!INCLUDE[prod_short](includes/prod_short.md)]. Learn more about your responsibility for keeping your app updated and the resources that are available to you in [Maintain AppSource apps and per-tenant extensions](app-maintain.md).  

## Scenario 1: Business Central service update

You don't need to make any bug fixes, feature adds, or app changes to your app. It continues to work fine without any interaction on your part.  

### Impact of service updates

The monthly service upgrades to [!INCLUDE[prod_short](includes/prod_short.md)] don't affect your app. Your app just gets moved along and no upgrade code from your app needs to get used. [!INCLUDE[prod_short](includes/prod_short.md)] itself gets upgraded on your tenant, and once complete, the customer sees no difference with your app.

## Scenario 2: App update

You (our partner) add some features to your app and also some minor bug fixes. The app is submitted for validation. The app passes validation and is checked into the service. This is now the active app for any new tenants and also for existing tenants that have never had your app installed before.

### Impact of app updates

Internal and delegated administrators can update AppSource apps from the [[!INCLUDE[prodadmincenter](../developer/includes/prodadmincenter.md)]](../administration/tenant-admin-center-manage-apps.md), and regular users can do so by uninstalling and reinstalling an app in the environment. AppSource apps are automatically updated to the latest version during a major update to an environment, unless the **App Update Cadence** has been set to **With minor and major updates** for the environment in the [!INCLUDE[prodadmincenter](../developer/includes/prodadmincenter.md)].

## Scenario 3: Reported bugs in your app

You (our partner) have various customers report some bugs that are impacting their usage of the app. The bugs aren't critical but they're important. The partner makes the fixes in the app and resubmits for validation. The app passes validation and gets checked into the service. This is now the active app for any new tenants and also for existing tenants that have never had your app installed before.

### Impact of bugs

We still don't force the upgrade of this app to this latest version on all of the tenants. Some tenants might not be using the functionality that includes this bug and continue to work fine on the current version of the app. Therefore, you should work directly with all of the impacted customer tenants to uninstall and reinstall to get the latest app version that contains fixes for the bug.

## Scenario 4: Critical bug in your app

If a bug, which leads to core functionality being broken or data loss/corruption/misrepresentation, is found in the application, and the issue prevents customers from performing time-critical tasks, the validation and deployment of the application can be prioritized. The partner must create a support ticket for this case and they must immediately provide a fixed app for validation through Partner Center. The validation team makes this a top priority and performs validation as soon as possible. If the fixed application passes validation, it's checked into the service and will become available for environment administrators to install.

## Scenario 5: Microsoft feature breaks your app

Microsoft has to break your app file for a needed [!INCLUDE[prod_short](includes/prod_short.md)] core change. Some reasons for breaking could be security, bugs in the underlying code, high priority feature adds, and so on. Keep in mind, we do our best to not break your app through our changes. We try to find proper ways of doing the changes without breaking your app. However, if we can't find a proper (nonbreaking) way, then we could break your app. This isn't as likely in a minor update release (unless a security change is required on our part and that's the change that breaks you), but it can be more likely in our major (every six-month) releases.

### Impact of breaking changes

Here's our process when this takes place:

- First of all, Microsoft isn't making a breaking change in the production environment at any point. Therefore, existing tenants aren't expected to see this breaking change occur.
- When we make a breaking change, we do it in a build branch that is for a future release (monthly service minor or major release).
- We notify the partner in advance and give the partner ample time to fix their app, get it validated, and have it ready.
- The fixed app will already be in our service and slotted as required for when your tenant is to be moved to the [!INCLUDE[prod_short](includes/prod_short.md)] release that has the app breaking change.
- As a result, the customer (tenant owner) should never see their [!INCLUDE[prod_short](includes/prod_short.md)] break. Because the tenant gets moved from one monthly service update of [!INCLUDE[prod_short](includes/prod_short.md)] to another, the tenant is being upgraded to the release of ours that breaks the specific app. However, our service detects that there's a new required version of that app (your fixed version). Therefore, we auto install the fixed version of the app for the tenant.

## Conclusions

You're responsible for your app. You own the process of updating the app and providing upgrade code if the schema changes between versions of the app. If a customer uninstalls your app, and then installs it again later, then when they install the app the second time, they get the latest version from AppSource.  

### How Microsoft handles your app

When Microsoft upgrades a tenant with a service update, your app is tested against the new service version. If the app breaks, Microsoft rolls back to the previous healthy state. Your customer never learns that anything was about to break.  

When a tenant uninstalls and reinstalls an extension via the **Extension Management** page or AppSource, there's platform logic that determines whether an *Install* or an *Upgrade* must take place. We detect which version of the extension the tenant previously had installed and perform the appropriate action. Therefore, the result of manually uninstalling/installing the extension is the exact same as an automated upgrade.  

Additionally, there isn't any data loss during uninstall, install, or upgrade actions. Data for extensions is stored in its own tables in the tenant database. Before an extension gets installed, it first gets synchronized on the tenant database. This step is implicit and happens automatically when a tenant installs an extension. This synchronization process creates the database tables for the extension. Once the extension is installed and the tenant is using it, extension-specific data get stored in these tables.  

When an extension gets uninstalled, these tables don't get removed. Therefore, when the extension gets reinstalled (or upgraded), the data is still available. You don't need to worry about data loss for choosing the uninstall/install route. However, do keep in mind that if any actions are being performed on the tenant while the extension is uninstalled, the extension's *events and such aren't firing*, and your app might miss the creation of new data. Try to perform the uninstall/install while the tenant isn't online.  

Learn more in [When apps or PTEs can't be updated by Microsoft](app-maintain.md#when-microsoft-cant-update-apps-or-ptes).  

## Related information

[Publishing and installing an extension](devenv-how-publish-and-install-an-extension-v2.md)  
[Retaining table data after publishing](devenv-retaining-data-after-publishing.md)  
[Upgrading extensions](devenv-upgrading-extensions.md)  
[Add your app to AppSource](../administration/appsource.md)  
[Checklist for submitting your app](devenv-checklist-submission.md)  
[Upgrading AppSource apps in production](devenv-upgrade-appsource-app-in-prod.md)  
[Maintain AppSource apps and per-tenant extensions](app-maintain.md)  
