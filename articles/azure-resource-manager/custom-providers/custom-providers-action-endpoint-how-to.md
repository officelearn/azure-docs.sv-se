---
title: Lägga till anpassade åtgärder i Azure REST API
description: Lär dig hur du lägger till anpassade åtgärder i Azure REST API. Den här artikeln kommer att gå igenom krav och metodtips för slutpunkter som vill implementera anpassade åtgärder.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650401"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Lägga till anpassade åtgärder i Azure REST API

Den här artikeln kommer att gå igenom de krav och metodtips för att skapa Azure Custom Resource Provider slutpunkter som implementerar anpassade åtgärder. Om du inte känner till Azure Custom Resource Providers läser du [översikten över anpassade resursleverantörer](overview.md).

## <a name="how-to-define-an-action-endpoint"></a>Så här definierar du en åtgärdsslutpunkt

En **slutpunkt** är en URL som pekar på en tjänst som implementerar det underliggande kontraktet mellan den och Azure. Slutpunkten definieras i den anpassade resursleverantören och kan vara alla offentligt tillgängliga URL. Exemplet nedan har **action** en `myCustomAction` åtgärd `endpointURL`som kallas implementerad av .

Exempel **ResourceProvider:**

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
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

## <a name="building-an-action-endpoint"></a>Skapa en åtgärdsslutpunkt

En **slutpunkt** som implementerar en **åtgärd** måste hantera begäran och svar för det nya API:et i Azure. När en anpassad resursleverantör med en **åtgärd** skapas genererar den en ny uppsättning API:er i Azure. I det här fallet genererar åtgärden ett `POST` nytt Azure-åtgärds-API för anrop:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Inkommande Azure API-begäran:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Den här begäran vidarebefordras sedan till **slutpunkten** i formuläret:

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

På samma sätt vidarebefordras svaret från **slutpunkten** tillbaka till kunden. Svaret från slutpunkten ska returneras:

- Ett giltigt JSON-objektdokument. Alla matriser och strängar ska kapslas under ett överkant objekt.
- Huvudet `Content-Type` ska ställas in på "application/json; charset=utf-8".

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Azure Custom Resource Provider Svar:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>Anropa en anpassad åtgärd

Det finns två huvudsakliga sätt att anropa en anpassad åtgärd från en anpassad resursleverantör:

- Azure CLI
- Azure Resource Manager-mallar

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

Parameter | Krävs | Beskrivning
---|---|---
åtgärd | *Ja* | Namnet på åtgärden som definierats i **ResourceProvider**.
Id | *Ja* | Resurs-ID för **ResourceProvider**.
begäran-organ | *nej* | Det förfråkomsstext som ska skickas till **slutpunkten**.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

> [!NOTE]
> Åtgärder har begränsat stöd i Azure Resource Manager-mallar. För att åtgärden ska kunna anropas i en [`list`](../templates/template-functions-resource.md#list) mall måste den innehålla prefixet i dess namn.

Exempel **på ResourceProvider** med liståtgärd:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Exempel på Azure Resource Manager-mall:

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

Parameter | Krävs | Beskrivning
---|---|---
resourceIdentifier | *Ja* | Resurs-ID för **ResourceProvider**.
apiVersion | *Ja* | API-versionen av resurskörningen. Detta bör alltid vara "2018-09-01-preview".
funktionVärderar | *nej* | Det förfråkomsstext som ska skickas till **slutpunkten**.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Custom Resource Providers](overview.md)
- [Snabbstart: Skapa Azure Custom Resource Provider och distribuera anpassade resurser](./create-custom-provider.md)
- [Självstudiekurs: Skapa anpassade åtgärder och resurser i Azure](./tutorial-get-started-with-custom-providers.md)
- [Så här lägger du till anpassade resurser i Azure REST API](./custom-providers-resources-endpoint-how-to.md)
