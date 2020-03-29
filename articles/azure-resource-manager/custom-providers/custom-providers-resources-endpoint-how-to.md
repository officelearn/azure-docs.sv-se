---
title: Lägga till anpassade resurser i Azure REST API
description: Lär dig hur du lägger till anpassade resurser i Azure REST API. Den här artikeln kommer att gå igenom krav och metodtips för slutpunkter som vill implementera anpassade resurser.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650531"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Lägga till anpassade resurser i Azure REST API

Den här artikeln kommer att gå igenom de krav och metodtips för att skapa Azure Custom Resource Provider slutpunkter som implementerar anpassade resurser. Om du inte känner till Azure Custom Resource Providers läser du [översikten över anpassade resursleverantörer](overview.md).

## <a name="how-to-define-a-resource-endpoint"></a>Så här definierar du en resursslutpunkt

En **slutpunkt** är en URL som pekar på en tjänst som implementerar det underliggande kontraktet mellan den och Azure. Slutpunkten definieras i den anpassade resursleverantören och kan vara alla offentligt tillgängliga URL. Exemplet nedan har en `myCustomResource` **resourceType** `endpointURL`som anropas av .

Exempel **ResourceProvider:**

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResource",
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

## <a name="building-a-resource-endpoint"></a>Skapa en resursslutpunkt

En **slutpunkt** som implementerar en **resourceType** måste hantera begäran och svar för det nya API:et i Azure. När en anpassad resursleverantör med en **resourceType** skapas genererar den en ny uppsättning API:er i Azure. I det här fallet genererar **resourceType** ett `PUT`nytt `GET`Azure-resurs-API för , och `DELETE` `GET` för att utföra CRUD på en enda resurs samt hämta alla befintliga resurser:

Manipulera en resurs`PUT` `GET`( `DELETE`, , och ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Hämta alla`GET`resurser ( ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

För anpassade resurser erbjuder anpassade resursleverantörer två typer`Proxy`av **routingTypes**: " " och "`Proxy, Cache`".

### <a name="proxy-routing-type"></a>proxyroutningstyp

Den`Proxy`" " **routingType** proxys alla begäransmetoder till **slutpunkten** som anges i den anpassade resursprovidern. När ska`Proxy`du använda " ":

- Full kontroll över svaret behövs.
- Integrera system med befintliga resurser.

Mer information om`Proxy`" " resurser finns [i den anpassade resursproxyreferensen](proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>routningstyp för proxycache

Den`Proxy, Cache`" " **routingType** `PUT` proxyservrar och `DELETE` begära metoder till **slutpunkten** som anges i den anpassade resursprovidern. Den anpassade resursleverantören returnerar `GET` automatiskt begäranden baserat på vad den har lagrat i cacheminnet. Om en anpassad resurs markeras med cache, kommer den anpassade resursprovidern också lägga till / skriva över fält i svaret att göra API:erna Azure-kompatibla. När ska`Proxy, Cache`du använda " ":

- Skapa ett nytt system som inte har några befintliga resurser.
- Arbeta med befintliga Azure-ekosystem.

Mer information om`Proxy, Cache`" " resurser finns [i den anpassade resurscachereferensen](proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Skapa en anpassad resurs

Det finns två huvudsakliga sätt att skapa en anpassad resurs bort från en anpassad resursleverantör:

- Azure CLI
- Azure Resource Manager-mallar

### <a name="azure-cli"></a>Azure CLI

Skapa en anpassad resurs:

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName} \
                   --properties \
                    '{
                        "location": "eastus",
                        "properties": {
                            "myProperty1": "myPropertyValue1",
                            "myProperty2": {
                                "myProperty3": "myPropertyValue3"
                            }
                        }
                    }'
```

Parameter | Krävs | Beskrivning
---|---|---
är-full-objekt | *Ja* | Anger att egenskapsobjektet innehåller andra alternativ, till exempel plats, taggar, sku och/eller plan.
id | *Ja* | Resurs-ID för den anpassade resursen. Detta bör finnas bort av **ResourceProvider**
properties | *Ja* | Det förfråkomsstext som ska skickas till **slutpunkten**.

Ta bort en anpassad Azure-resurs:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parameter | Krävs | Beskrivning
---|---|---
id | *Ja* | Resurs-ID för den anpassade resursen. Detta bör finnas bort från **ResourceProvider**.

Hämta en anpassad Azure-resurs:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parameter | Krävs | Beskrivning
---|---|---
id | *Ja* | Resurs-ID för den anpassade resursen. Detta bör finnas bort av **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

> [!NOTE]
> Resurser kräver att svaret `id`innehåller `name`en `type` lämplig , och från **slutpunkten**.

Azure Resource Manager-mallar `name`kräver `type` att `id`, och returneras korrekt från slutpunkten för underordnade led. Ett returnerat resurssvar ska vara i form:

Exempel på **slutpunktssvar:**

``` JSON
{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3": "myPropertyValue3"
    }
  },
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{customResourceName}",
  "name": "{customResourceName}",
  "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}"
}
```

Exempel på Azure Resource Manager-mall:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}",
            "name": "{resourceProviderName}/{customResourceName}",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3": "myPropertyValue3"
                }
            }
        }
    ]
}
```

Parameter | Krävs | Beskrivning
---|---|---
resourceTypeName | *Ja* | **Namnet** på **resourceType** som definierats i den anpassade providern.
resursProviderNamn | *Ja* | Instansnamnet för den anpassade resursprovidern.
customResourceName | *Ja* | Det anpassade resursnamnet.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Custom Resource Providers](overview.md)
- [Snabbstart: Skapa Azure Custom Resource Provider och distribuera anpassade resurser](./create-custom-provider.md)
- [Självstudiekurs: Skapa anpassade åtgärder och resurser i Azure](./tutorial-get-started-with-custom-providers.md)
- [Så här lägger du till anpassade åtgärder i Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Referens: Anpassad resursproxyreferens](proxy-resource-endpoint-reference.md)
- [Referens: Anpassad resurscachereferens](proxy-cache-resource-endpoint-reference.md)
