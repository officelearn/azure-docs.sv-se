---
title: Skapa referens för resursproxy
description: Anpassad resursproxyreferens för Azure Custom Resource Providers. Den här artikeln kommer att gå igenom kraven för slutpunkter implementera proxy anpassade resurser.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 46b38686b39836f3d4bfb80686d514f932a79bf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650466"
---
# <a name="custom-resource-proxy-reference"></a>Referens för anpassad resursproxy

Den här artikeln kommer att gå igenom kraven för slutpunkter implementera proxy anpassade resurser. Om du inte känner till Azure Custom Resource Providers läser du [översikten över anpassade resursleverantörer](overview.md).

## <a name="how-to-define-a-proxy-resource-endpoint"></a>Så här definierar du en proxyresursslutpunkt

En proxyresurs kan skapas genom att ange **routingType** till "Proxy".

Exempel på anpassad resursprovider:

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

## <a name="building-proxy-resource-endpoint"></a>Slutpunkt för skapa proxyresurs

En **slutpunkt** som implementerar en "Proxy"-resursslutpunkt måste hantera begäran och svaret för det nya API:et i Azure. **endpoint** I det här fallet genererar **resourceType** ett `PUT`nytt `GET`Azure-resurs-API för , och `DELETE` för `GET` att utföra CRUD på en enda resurs samt hämta alla befintliga resurser.

> [!NOTE]
> `id`Fälten `name`, `type` och är inte obligatoriska, men behövs för att integrera den anpassade resursen med befintliga Azure-ekosystem.

Exempel på resurs:

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

Parameterreferens:

Egenskap | Exempel | Beskrivning
---|---|---
namn | '{myCustomResourceName} | Namnet på den anpassade resursen.
typ | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName} | Namnområdet för resurstyp.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>leverantörer/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{myCustomResourceName} | Resurs-ID.

### <a name="create-a-custom-resource"></a>Skapa en anpassad resurs

Inkommande Azure API-begäran:

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

Den här begäran vidarebefordras sedan till **slutpunkten** i formuläret:

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

På samma sätt vidarebefordras svaret från **slutpunkten** tillbaka till kunden. Svaret från slutpunkten ska returneras:

- Ett giltigt JSON-objektdokument. Alla matriser och strängar ska kapslas under ett överkant objekt.
- Huvudet `Content-Type` ska ställas in på "application/json; charset=utf-8".

**Slutpunkt** Svar:

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

Azure Custom Resource Provider Svar:

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

Inkommande Azure API-begäran:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Den här begäran vidarebefordras sedan till **slutpunkten** i formuläret:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

På samma sätt vidarebefordras svaret från **slutpunkten** tillbaka till kunden. Svaret från slutpunkten ska returneras:

- Giltigt JSON-objektdokument. Alla matriser och strängar ska kapslas under ett överkant objekt.
- Huvudet `Content-Type` ska ställas in på "application/json; charset=utf-8".

**Slutpunkt** Svar:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Azure Custom Resource Provider Svar:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Hämta en anpassad resurs

Inkommande Azure API-begäran:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Den här begäran vidarebefordras sedan till **slutpunkten** i formuläret:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

På samma sätt vidarebefordras svaret från **slutpunkten** tillbaka till kunden. Svaret från slutpunkten ska returneras:

- Ett giltigt JSON-objektdokument. Alla matriser och strängar ska kapslas under ett överkant objekt.
- Huvudet `Content-Type` ska ställas in på "application/json; charset=utf-8".

**Slutpunkt** Svar:

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

Azure Custom Resource Provider Svar:

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

Inkommande Azure API-begäran:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Den här begäran vidarebefordras sedan till **slutpunkten** i formuläret:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

På samma sätt vidarebefordras svaret från **slutpunkten** tillbaka till kunden. Svaret från slutpunkten ska returneras:

- Ett giltigt JSON-objektdokument. Alla matriser och strängar ska kapslas under ett överkant objekt.
- Huvudet `Content-Type` ska ställas in på "application/json; charset=utf-8".
- Listan över resurser bör placeras under `value` egenskapen på den översta nivån.

**Slutpunkt** Svar:

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

Azure Custom Resource Provider Svar:

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

- [Översikt över Azure Custom Resource Providers](overview.md)
- [Snabbstart: Skapa Azure Custom Resource Provider och distribuera anpassade resurser](./create-custom-provider.md)
- [Självstudiekurs: Skapa anpassade åtgärder och resurser i Azure](./tutorial-get-started-with-custom-providers.md)
- [Så här lägger du till anpassade åtgärder i Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Referens: Anpassad resurscachereferens](proxy-cache-resource-endpoint-reference.md)
