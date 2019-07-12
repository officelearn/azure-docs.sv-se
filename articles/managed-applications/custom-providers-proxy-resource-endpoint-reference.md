---
title: Skapa referens för resursproxy
description: Anpassad resurs proxy-referens för Azure anpassade Resource Providers. Den här artikeln går igenom kraven för slutpunkter som implementerar proxy anpassade resurser.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: fe470165d11b24ffb1df704ecaa9804663fc9e5c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796036"
---
# <a name="custom-resource-proxy-reference"></a>Anpassad Proxy resursreferens

Den här artikeln går igenom kraven för slutpunkter som implementerar proxy anpassade resurser. Om du inte känner till Azure anpassade Resursprovidrar [översikt över anpassade resursprovidrar](./custom-providers-overview.md).

## <a name="how-to-define-a-proxy-resource-endpoint"></a>Hur du definierar en resursslutpunkt för proxy

En proxy-resurs kan skapas genom att ange den **routingType** till ”Proxy”.

Exemplet anpassade resource provider:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
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

En **endpoint** som implementerar en ”Proxy”-resurs **endpoint** måste hantera begäranden och svar för den nya API: et i Azure. I det här fallet den **resourceType** genererar en ny resurs i Azure API för `PUT`, `GET`, och `DELETE` att utföra CRUD på en enskild resurs, samt `GET` att hämta alla befintliga resurser.

> [!NOTE]
> Den `id`, `name`, och `type` krävs inte, men behövs för att integrera den anpassade resursen med befintliga Azure-ekosystemet.

Exemplet resursen:

``` JSON
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

Parametern-referens:

Egenskap | Exempel | Beskrivning
---|---|---
name | '{myCustomResourceName}' | Namnet på den anpassade resursen.
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | Namnområdet för resursen.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{myCustomResourceName}' | Resurs-ID.

### <a name="create-a-custom-resource"></a>Skapa en anpassad resurs

Azure API-inkommande begäran:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resource-provider-name}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
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

**Slutpunkten** svaret:

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

På samma sätt svaret från den **endpoint** sedan vidarebefordras till kunden. Svaret från slutpunkten ska returnera:

- Giltigt dokument med JSON-objekt. Alla matriser och -strängar bör kapslas under en översta objektet.
- Den `Content-Type` huvudet ska anges till ”application/json; charset = utf-8 ”.

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

Den här begäran kommer sedan att vidarebefordras till den **endpoint** i formuläret:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

På samma sätt kan svaret från den **endpoint** sedan vidarebefordras till kunden. Svaret från slutpunkten ska returnera:

- Ett giltigt JSON-objektet dokument. Alla matriser och -strängar bör kapslas under en översta objektet.
- Den `Content-Type` huvudet ska anges till ”application/json; charset = utf-8 ”.

**Slutpunkten** svaret:

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

Den här begäran kommer sedan att vidarebefordras till den **endpoint** i formuläret:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

På samma sätt kan svaret från den **endpoint** sedan vidarebefordras till kunden. Svaret från slutpunkten ska returnera:

- Ett giltigt JSON-objektet dokument. Alla matriser och -strängar bör kapslas under en översta objektet.
- Den `Content-Type` huvudet ska anges till ”application/json; charset = utf-8 ”.
- I listan över resurser ska placeras under den översta `value` egenskapen.

**Slutpunkten** svaret:

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
- [Referens: Anpassad resurs Cache-referens](./custom-providers-proxy-cache-resource-endpoint-reference.md)
