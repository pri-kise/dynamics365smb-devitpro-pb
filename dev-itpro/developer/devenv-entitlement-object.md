---
title: Entitlement object
description: Discover how to define and use entitlement objects in AL for Business Central.
author: SusanneWindfeldPedersen
ms.date: 03/19/2025
ms.update-cycle: 1095-days
ms.topic: article
ms.author: solsen
ms.reviewer: solsen
ms.custom: evergreen
---

# Entitlement object

[!INCLUDE [2021_releasewave1](../includes/2021_releasewave1.md)]

[!INCLUDE[azure-ad-to-microsoft-entra-id](~/../shared-content/shared/azure-ad-to-microsoft-entra-id.md)]

The entitlement object in [!INCLUDE [prod_short](includes/prod_short.md)] describes which objects in [!INCLUDE [prod_short](includes/prod_short.md)] a customer is entitled to use according to the license that they purchased or the role that they have in Microsoft Entra ID.

An entitlement consists of many [PermissionSet objects](devenv-permissionset-object.md) put together to constitute a set of meaningful permissions for a user. An entitlement can only include permission set objects, which reference the objects that are included within the same app. This is to ensure that the entitlements included with one app can't alter or redefine the entitlements included with another app. Being entitled defines the maximum permissions a user is entitled to. Actual permissions are the intersection between the permissions the user is entitled to and the permissions the user is assigned.

Entitlements can only be used with the online version of [!INCLUDE [prod_short](includes/prod_short.md)].

## Supporting transactability for AppSource apps

With [!INCLUDE [prod_short](includes/prod_short.md)] 2023 release wave 2, entitlements support transactability for AppSource apps by binding entitlements to offers. Learn more in [Selling Business Central apps through AppSource](devenv-sell-apps-appsource.md).

<!--
> [!NOTE]  
> In the current version of [!INCLUDE [prod_short](includes/prod_short.md)] entitlements can only be included with Microsoft apps (enforced by the AppSource cop rules and the technical validation checks that we run for the apps submitted to AppSource). These objects will become available for the ISV apps when we introduce ability to monetize AppSource apps in one of our future releases.
-->

## Snippet support

Typing the shortcut `tentitlement` creates the basic layout for an entitlement object when using the [!INCLUDE[d365al_ext_md](../includes/d365al_ext_md.md)] in Visual Studio Code.

[!INCLUDE[intelli_shortcut](includes/intelli_shortcut.md)]

## Entitlement examples

The following sections illustrate examples of how to define entitlements in AL. For more inspiration, learn from examples in the System Application [BCApps](https://github.com/microsoft/BCApps/tree/main/src/System%20Application/App/Entitlements).

Learn about how telemetry data can help you identify problems a user (or a service) might experience when signing in, in [Analyzing Authorization Trace Telemetry](../administration/telemetry-authorization-trace.md).

### Entitlement example - delegated admin

This example illustrates a simple entitlement object with the [Type property](properties/devenv-type-property.md) set to `Role`, which means that the entitlement is associated with a Microsoft Entra role. When `Type` is set to `Role`, the [RoleType property](properties/devenv-roletype-property.md) distinguishes between local and delegated assignments of the role. In this case, it's `Delegated`. The [ObjectEntitlements property](properties/devenv-objectentitlements-property.md) defines the list of permissions that the entitlement includes.

```al

entitlement "Delegated Admin agent - Partner"
{
    Type = Role;
    RoleType = Delegated;
    Id = '00000000-0000-0000-0000-000000000007';

    ObjectEntitlements = MyApp_PartnerFullAccessPermissionSet;
}

entitlement "Delegated Helpdesk agent - Partner"
{
    Type = Role;
    RoleType = Delegated;
    Id = '00000000-0000-0000-0000-000000000008';

    ObjectEntitlements = MyApp_PartnerFullAccessPermissionSet;
}

entitlement "Dynamics 365 Admin - Partner"
{
    Type = Role;
    RoleType = Delegated;
    Id = '00000000-0000-0000-0000-000000000009';

    ObjectEntitlements = MyApp_PartnerFullAccessPermissionSet;
}

entitlement "Delegated BC Admin agent - Partner"
{
    Type = Role;
    RoleType = Delegated;
    Id = '00000000-0000-0000-0000-000000000010';

    ObjectEntitlements = MyApp_PartnerFullAccessPermissionSet;
}
```

### Entitlement example - per-user plan

An example of an entitlement where `Type` is `PerUserOfferPlan`. This type is used to enable transactability for AppSource apps. The `Id` property is used to map the entitlement to the plan in Partner Center, and must contain the **Service ID** for the plan. For more information, see [Selling Business Central apps through AppSource](devenv-sell-apps-appsource.md).

```al
entitlement BC_PerUserOfferPlan
{
    Type = PerUserOfferPlan;
    Id = 'MyOfferPlan';

    ObjectEntitlements = "MyOfferLicensePermission";
}
```

### Entitlement example - unlicensed

For scenarios when the user isn't licensed through entitlements mapping to AppSource offer plans, the `Unlicensed` type of entitlement is used. This type is used to enable custom licensing for an existing customer, or *side-by-side support*, for transactability-enabled apps on AppSource. For more information, see [Selling Business Central apps through AppSource](devenv-sell-apps-appsource.md).

```al
entitlement BC_Unlicensed
{
    Type = Unlicensed;
    ObjectEntitlements = "Custom license";
}
```

In the following code example, you can see how to check for entitlements in code.

#### Entitlement example - testing for entitlements in code

```al
permissionset 50101 MyFreeLicensePermission
{
    Assignable = false;
    Permissions = table MyTable = X,
                  tabledata MyTable = R;
}

permissionset 50102 MyOfferLicensePermission
{
    Assignable = false;
    Permissions = tabledata MyTable = RMID;
    IncludedPermissionSets = "MyFreeLicensePermission";
}

entitlement BC_Unlicensed
{
    Type = Unlicensed;
    ObjectEntitlements = "MyFreeLicensePermission";
}

entitlement BC_PerUserOfferPlan
{
    Type = PerUserOfferPlan;
    Id = 'MyOfferPlan';
    ObjectEntitlements = "MyOfferLicensePermission";
}

...
procedure CheckingForEntitlementsUsingPermissions()
    var
        myTable: Record MyTable;
    begin
        if myTable.WritePermission() then
            message('User is entitled and has permission to write to MyTable => user is licensed')
        else
            if myTable.ReadPermission() then
                message('User is entitled and has permission to read from MyTable => user is unlicensed')
            else
                Message('User does not have permission to read from MyTable - we do not know if the user is licensed ');
    end;

    procedure CheckingForMyEntitlements()
    begin
        if NavApp.IsUnlicensed() then
            Message('User is assigned my BC_Unlicensed entitlement')
        else
            if NavApp.IsEntitled('BC_PerUserOfferPlan') then
                Message('User is assigned my BC_PerUserOfferPlan entitlement')
            else
                Message('This user is not assigned any of my entitlements, so this code will not run');
    end;

    procedure CheckingForOtherAppEntitlements()
    begin
        if (NavApp.IsEntitled('Delegated Admin agent - Partner', '63ca2fa4-4f03-4f2b-a480-172fef340d3f')) then
            Message('User is assigned the delegated admin agent entitlement defined in the system app: https://github.com/microsoft/BCApps/blob/main/src/System%20Application/App/Entitlements/DelegatedAdminagentPartner.Entitlement.al')
        else
            if (NavApp.IsEntitled('Dynamics 365 Business Central Essentials', '63ca2fa4-4f03-4f2b-a480-172fef340d3f')) then
                Message('User is assigned the essentials entitlement defined in the system app: https://github.com/microsoft/BCApps/blob/main/src/System%20Application/App/Entitlements/Dynamics365BusinessCentralEssentials.Entitlement.al');
    end;
...
```

### Entitlement example - Microsoft Entra group

This example shows an entitlement where `Type` is `Group`. It supports scenarios where a user needs access to the AppSource app with transact support without buying a developer license. The `id` property is the object ID of the Microsoft Entra group. Learn more in [Selling Business Central apps through AppSource](devenv-sell-apps-appsource.md).

```al
entitlement BC_Group
{
    Type = Group;
    Id = '1a2aaaaa-3aa4-5aa6-789a-a1234567aaaa';
}

```

### Entitlement example - Microsoft Entra application access

An example of an entitlement where `Type` is `Application`. It supports scenarios when a vendor needs access to the AppSource app with transact support and no need to buy a license. The `id` property is the client ID of the Microsoft Entra application. Learn more in [Selling Business Central apps through AppSource](devenv-sell-apps-appsource.md).

```al
entitlement BC_SpecificApplication
{
    Type = Application;
    Id = '1a2aaaaa-3aa4-5aa6-789a-a1234567aaaa';
}
```

An example of an entitlement where `Type` is `ApplicationScope`. It supports scenarios when Microsoft Entra application access to the AppSource app with transact support is needed and no license is required. The `id` property is the scope assigned to the Microsoft Entra application. Learn more in [Using Service-to-Service (S2S) Authentication](../administration/automation-apis-using-s2s-authentication.md).

```al
entitlement BC_ApplicationWithAPIRWScope
{
    Type = ApplicationScope;
    Id = 'API.ReadWrite.All';
}
entitlement BC_ApplicationWithAutomationScope
{
    Type = ApplicationScope;
    Id = 'Automation.ReadWrite.All';
}
```

## Related information

[Developing extensions](devenv-dev-overview.md)  
[AL development environment](devenv-reference-overview.md)  
[Entitlements and permission set overview](devenv-entitlements-and-permissionsets-overview.md)  
[Permission set extension object](devenv-permissionset-ext-object.md)  
[Selling Business Central apps through AppSource](devenv-sell-apps-appsource.md)  
