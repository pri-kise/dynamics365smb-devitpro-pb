---
title: (v1.0) Get customer defaultDimensions
description: (v1.0) Gets a customer default dimensions in Dynamics 365 Business Central.
author: SusanneWindfeldPedersen
ms.custom: evergreen
ms.topic: reference
ms.devlang: al
ms.date: 05/01/2024
ms.update-cycle: 1095-days
ms.author: solsen
ms.reviewer: solsen
---

# Get customer defaultDimensions (v1.0)
Gets the default dimensions of the customer in [!INCLUDE[prod_short](../../../includes/prod_short.md)].

## HTTP request
Replace the URL prefix for [!INCLUDE[prod_short](../../../includes/prod_short.md)] depending on environment following the [guideline](../../v1.0/endpoints-apis-for-dynamics.md). 
The following example gets the default dimensions of the customer entity in the response body.

```
GET businesscentralPrefix/companies({companyId})/customers({customerId})/defaultDimensions
```
## Request header

|Header|Value|
|------|-----|
|Authorization| Bearer {token}. Required.|

## Request body (v1.0)
Do not supply a request body for this method.

## Response (v1.0) 

If successful, this method returns a `200 OK` response code and the **default dimensions** in the response body.

## Example (v1.0) 

**Request**
Here is an example of a request. 

```json
GET https://{businesscentralPrefix}/api/v1.0/companies({companyId})/customers({customerId})/defaultDimensions
```

**Response**  
Here is an example of the response.

> [!NOTE]  
> The response object shown here may be truncated for brevity. All of the properties will be returned from an actual call.

```json
{
    "@odata.context":"https://api.businesscentral.dynamics.com/v1.0/api/v1.0/$metadata#companies(5106c77d-af37-4e2d-bb88-45d87aba1033)/customers(b3fbe87a-61b8-4a6c-85de-0555f1627a67)/defaultDimensions",
    "value":
    [
        {
            "@odata.etag":"W/\"JzQ0OzNPaHFuS0ZQdk5oc3ZkSW9KdzVkdXk2LytjcmNqeHJJOU05SjZ1aFBYVjQ9MTswMDsn\"",
            "parentId":"b3fbe87a-61b8-4a6c-85de-0555f1627a67","dimensionId":"d5fc81ea-8687-4e9d-9c49-7fde28ccdb1a",
            "dimensionCode":"DEPARTMENT",
            "dimensionValueId":"1045a902-070a-4d31-b2b1-b9431e9e5b26",
            "dimensionValueCode":"PROD",
            "postingValidation":"Same Code"
        }
    ]
} 
```

## Related information
[Tips for working with the APIs](../../../developer/devenv-connect-apps-tips.md)  

[Customers](../resources/dynamics_customer.md)  
[Create customer defaultDimensions](dynamics_customer_create_defaultdimensions.md)  
[Update customer defaultDimensions](dynamics_customer_update_defaultdimensions.md)  
[Delete customer defaultDimensions](dynamics_customer_delete_defaultdimensions.md)  

