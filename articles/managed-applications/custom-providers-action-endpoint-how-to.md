---
title: Att lägga till anpassade åtgärder i Azure REST API
description: Lär dig hur du lägger till anpassade åtgärder i Azure REST-API. Den här artikeln beskriver krav och bästa praxis för slutpunkter som vill implementera anpassade åtgärder.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6fbd20c201e1b141b7276e3283599b00cdefd118
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795306"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Att lägga till anpassade åtgärder i Azure REST-API

Den här artikeln går igenom de krav och bästa praxis för att skapa anpassade Azure-Resursprovidern slutpunkter som implementerar anpassade åtgärder. Om du inte känner till Azure anpassade Resursprovidrar [översikt över anpassade resursprovidrar](./custom-providers-overview.md).

## <a name="how-to-define-an-action-endpoint"></a>Definiera en slutpunkt för åtgärden

En **endpoint** är en URL som pekar på en tjänst som implementerar underliggande kontraktet mellan dem och Azure. Slutpunkten har definierats i anpassade resursprovidern och kan vara valfri offentligt tillgänglig URL. Exemplet nedan har en **åtgärd** kallas `myCustomAction` implementeras av `endpointURL`.

Exemplet **ResourceProvider**:

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

## <a name="building-an-action-endpoint"></a>Att skapa en slutpunkt för åtgärden

En **endpoint** som implementerar en **åtgärd** måste hantera begäranden och svar för den nya API: et i Azure. När en anpassad resurs-provider med en **åtgärd** har skapats kan den att generera en ny uppsättning API: er i Azure. I det här fallet åtgärden genererar en ny Azure åtgärd API för `POST` anrop:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Azure API-inkommande begäran:

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

Den här begäran kommer sedan att vidarebefordras till den **endpoint** i formuläret:

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

På samma sätt kan svaret från den **endpoint** sedan vidarebefordras till kunden. Svaret från slutpunkten ska returnera:

- Ett giltigt JSON-objektet dokument. Alla matriser och -strängar bör kapslas under en översta objektet.
- Den `Content-Type` huvudet ska anges till ”application/json; charset = utf-8 ”.

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

Azure anpassade Resursprovidersvar:

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

Det finns två huvudsakliga sätt att anropa en anpassad åtgärd från en anpassad resurs-provider:

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
action | *Ja* | Namnet på åtgärden som definieras i den **ResourceProvider**.
ID: n | *Ja* | Resurs-ID för den **ResourceProvider**.
brödtext för begäran | *no* | Begärandetexten som ska skickas till den **endpoint**.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

> [!NOTE]
> Åtgärder har begränsat stöd i Azure Resource Manager-mallar. För åtgärden som ska anropas inuti en mall, måste den innehålla den [ `list` ](../azure-resource-manager/resource-group-template-functions-resource.md#list) prefixet i sitt namn.

Exemplet **ResourceProvider** med liståtgärd:

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

Exemplet Azure Resource Manager-mall:

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
resourceIdentifier | *Ja* | Resurs-ID för den **ResourceProvider**.
apiVersion | *Ja* | API-versionen av resursen runtime. Du bör alltid ”2018-09-01-preview”.
functionValues | *no* | Begärandetexten som ska skickas till den **endpoint**.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure anpassade Resursprovidrar](./custom-providers-overview.md)
- [Snabbstart: Skapa anpassade Azure-Resursprovidern och distribuera anpassade resurser](./create-custom-provider.md)
- [Självstudier: Skapa anpassade åtgärder och resurser i Azure](./tutorial-custom-providers-101.md)
- [Anvisningar: Att lägga till anpassade resurser i Azure REST-API](./custom-providers-resources-endpoint-how-to.md)
