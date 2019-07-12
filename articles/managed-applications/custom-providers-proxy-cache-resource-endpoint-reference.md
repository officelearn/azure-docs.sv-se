---
title: Skapa referens för resurscache
description: Anpassad resurs cache-referens för Azure anpassad Resursprovider. Den här artikeln går igenom kraven för slutpunkter som implementerar cache anpassade resurser.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 2970e69e825dacb6f548b3e66a830f221ece0b1c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795232"
---
# <a name="custom-resource-cache-reference"></a>Anpassad resurs Cache-referens

Den här artikeln går igenom kraven för slutpunkter som implementerar cache anpassade resurser. Om du inte känner till Azure anpassade Resursprovidrar [översikt över anpassade resursprovidrar](./custom-providers-overview.md).

## <a name="how-to-define-a-cache-resource-endpoint"></a>Definiera en cache-slutpunkten för resursen

En proxy-resurs kan skapas genom att ange den **routingType** till ”proxyn Cache”.

Exemplet anpassade resource provider:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy, Cache",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-proxy-resource-endpoint"></a>Skapa proxy resursslutpunkt

En **endpoint** som implementerar en ”Proxy, Cache” resurs **endpoint** måste hantera begäranden och svar för den nya API: et i Azure. I det här fallet den **resourceType** genererar en ny resurs i Azure API för `PUT`, `GET`, och `DELETE` att utföra CRUD på en enskild resurs, samt `GET` att hämta alla befintliga resurser:

> [!NOTE]
> Azure API genererar metodbegäranden `PUT`, `GET`, och `DELETE`, och cacheplatsen **endpoint** endast behöver hantera `PUT` och `DELETE`.
> Vi rekommenderar att den **endpoint** implementerar också `GET`.

### <a name="create-a-custom-resource"></a>Skapa en anpassad resurs

Azure API-inkommande begäran:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Den här begäran kommer sedan att vidarebefordras till den **endpoint** i formuläret:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

På samma sätt kan svaret från den **endpoint** sedan vidarebefordras till kunden. Svaret från slutpunkten ska returnera:

- Ett giltigt JSON-objektet dokument. Alla matriser och -strängar bör kapslas under en översta objektet.
- Den `Content-Type` huvudet ska anges till ”application/json; charset = utf-8 ”.
- Anpassade resursprovidern skrivs den `name`, `type`, och `id` fält för begäran.
- Anpassade resursprovidern returnerar endast fält under den `properties` objekt för en cache-slutpunkten.

**Slutpunkten** svaret:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Den `name`, `id`, och `type` kommer automatiskt att genereras för den anpassade resursen fält av anpassade resursprovidern.

Azure anpassade Resursprovidersvar:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>Ta bort en anpassad resurs

Azure API-inkommande begäran:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Den här begäran kommer sedan att vidarebefordras till den **endpoint** i formuläret:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

På samma sätt kan svaret från den **endpoint** sedan vidarebefordras till kunden. Svaret från slutpunkten ska returnera:

- Ett giltigt JSON-objektet dokument. Alla matriser och -strängar bör kapslas under en översta objektet.
- Den `Content-Type` huvudet ska anges till ”application/json; charset = utf-8 ”.
- Azure Custom Resource Provider tar endast bort objektet från sitt cacheminne om ett servernivå 200-svar returneras. Även om resursen inte finns, den **endpoint** ska returnera 204.

**Slutpunkten** svaret:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Azure anpassade Resursprovidersvar:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Hämta en anpassad resurs

Azure API-inkommande begäran:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Begäran kommer **inte** vidarebefordras till den **endpoint**.

Azure anpassade Resursprovidersvar:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>Räkna upp alla anpassade resurser

Azure API-inkommande begäran:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Den här begäran kommer **inte** vidarebefordras till den **endpoint**.

Azure anpassade Resursprovidersvar:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure anpassade Resursprovidrar](./custom-providers-overview.md)
- [Snabbstart: Skapa anpassade Azure-Resursprovidern och distribuera anpassade resurser](./create-custom-provider.md)
- [Självstudier: Skapa anpassade åtgärder och resurser i Azure](./tutorial-custom-providers-101.md)
- [Anvisningar: Att lägga till anpassade åtgärder i Azure REST-API](./custom-providers-action-endpoint-how-to.md)
- [Referens: Anpassad Proxy resursreferens](./custom-providers-proxy-resource-endpoint-reference.md)
