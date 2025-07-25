---
title: (v1.0) Get subscriptions
description: (v1.0) Gets a subscriptions object in Dynamics 365 Business Central.
author: SusanneWindfeldPedersen
ms.custom: evergreen
ms.topic: reference
ms.devlang: al
ms.date: 05/01/2024
ms.update-cycle: 1095-days
ms.author: solsen
ms.reviewer: solsen
---

# Get subscriptions (v1.0)
Retrieves the properties of webhook subscription objects for [!INCLUDE[prod_short](../../../includes/prod_short.md)].

## Custom APIs

If you're subscribing to a custom API page, both the URL you send the subscription HTTP request to and the resource path you wish to subscribe to must include the `<APIPublisher>`, `<APIGroup>`, and `<APIVersion>` elements equivalent to: `api/<APIPublisher>/<APIGroup>/<APIVersion>/subscriptions`. For example, if your API publisher is `pub`, your API group is `grp`, and the version is 1.0, part of the URL will contain these elements `api/pub/grp/v1.0/subscriptions`.


## HTTP request
Replace the URL prefix for [!INCLUDE[prod_short](../../../includes/prod_short.md)] depending on environment following the [guideline](../../v1.0/endpoints-apis-for-dynamics.md).
```
GET businesscentralPrefix/subscriptions
```

## Request headers (v1.0)
|Header|Value|
|------|-----|
|Authorization  |Bearer {token}. Required. |

## Request body (v1.0)
Do not supply a request body for this method.

## Response (v1.0)
If successful, this method returns a `200 OK` response code and a **subscription** object in the response body.

## Example (v1.0)

**Request**

Here is an example of the request.
```json
https://{businesscentralPrefix}/api/v1.0/subscriptions 
```

**Response**

Here is an example of the response. 

> [!NOTE]  
> The response object shown here may be truncated for brevity. All of the properties will be returned from an actual call.

```json
{
  "subscriptionId": "c670ea73cacb459bb51dc1740da2f1db",
  "notificationUrl": "https://contoso.com/myCallbacks",
  "resource": "/api/v1.0/companies(f64eba74-dacd-4854-a584-1834f68cfc3a)/customers",
  "userId": "00000000-0000-0000-0000-000000000001",
  "lastModifiedDateTime": "2018-10-12T12:32:35Z",
  "clientState": "anytextvalueof2048",
  "expirationDateTime": "2018-10-15T12:32:35Z"
}
```


## Related information
[Tips for working with the APIs](../../../developer/devenv-connect-apps-tips.md)  



[Error Codes](../dynamics_error_codes.md)  
[Subscription Resource Type](../resources/dynamics_subscription.md)  
[Create subscriptions](dynamics_subscription_create.md)  
[Update subscriptions](dynamics_subscription_update.md)  
[Delete subscriptions](dynamics_subscription_delete.md)  
[Microsoft REST API Guidelines](https://github.com/Microsoft/api-guidelines/blob/vNext/Guidelines.md#15-push-notifications-via-webhooks)