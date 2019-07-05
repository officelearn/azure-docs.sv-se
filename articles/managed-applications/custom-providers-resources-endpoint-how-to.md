---
title: Att lägga till anpassade resurser i Azure REST API
description: Lär dig mer om att lägga till anpassade resurser i Azure REST-API. Den här artikeln beskriver krav och bästa praxis för slutpunkter som vill implementera anpassade resurser.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b94d59b55a62797e142768dc84ec499d714bd067
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67479023"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Att lägga till anpassade resurser i Azure REST-API

Den här artikeln går igenom de krav och bästa praxis för att skapa anpassade Azure-Resursprovidern slutpunkter som implementerar anpassade resurser. Om du inte känner till Azure anpassade Resursprovidrar [översikt över anpassade resursprovidrar](./custom-providers-overview.md).

## <a name="how-to-define-a-resource-endpoint"></a>Hur du definierar en resursslutpunkt

En **endpoint** är en URL som pekar på en tjänst som implementerar underliggande kontraktet mellan dem och Azure. Slutpunkten har definierats i anpassade resursprovidern och kan vara valfri offentligt tillgänglig URL. Exemplet nedan har en **resourceType** kallas `myCustomResource` implementeras av `endpointURL`.

Exemplet **ResourceProvider**:

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

## <a name="building-a-resource-endpoint"></a>Att skapa en resursslutpunkt

En **endpoint** som implementerar en **resourceType** måste hantera begäranden och svar för den nya API: et i Azure. När en anpassad resurs-provider med en **resourceType** har skapats kan den att generera en ny uppsättning API: er i Azure. I det här fallet den **resourceType** genererar en ny resurs i Azure API för `PUT`, `GET`, och `DELETE` att utföra CRUD på en enskild resurs, samt `GET` att hämta alla befintliga resurser:

Ändra enskild resurs (`PUT`, `GET`, och `DELETE`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Hämta alla resurser (`GET`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

För anpassade resurser, anpassade resursprovidrar erbjuder två typer av **routingTypes**”:`Proxy`” och ”`Proxy, Cache`”.

### <a name="proxy-routing-type"></a>Proxy-routningstyp

Den ”`Proxy`” **routingType** proxyservrar alla begär metoder för att den **endpoint** anges i anpassade resursprovidern. När du ska använda ”`Proxy`”:

- Fullständig kontroll över svaret krävs.
- Integrera system med befintliga resurser.

Mer information om ”`Proxy`” resurser finns i [anpassad resurs proxy-referens](./custom-providers-proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>Routning proxytyp för cache

Den ”`Proxy, Cache`” **routingType** proxyservrar endast `PUT` och `DELETE` begär metoder för att den **endpoint** anges i anpassade resursprovidern. Anpassade resursprovidern automatiskt returnerar `GET` förfrågningar baserat på vad det har lagrats i cacheminnet. Om en anpassad resurs är markerad med cache, kommer även anpassade resursprovidern Lägg till / skriver över fält som svar på gör Azure API: er som är kompatibla. När du ska använda ”`Proxy, Cache`”:

- Skapa ett nytt system som inte har några befintliga resurser.
- Arbeta med befintliga Azure-ekosystemet.

Mer information om ”`Proxy, Cache`” resurser finns i [anpassad resurs cache-referens](./custom-providers-proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Skapa en anpassad resurs

Det finns två huvudsakliga sätt att skapa en anpassad resurs från en anpassad resurs-provider:

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

Parameter | Obligatoriskt | Beskrivning
---|---|---
is-full-object | *Ja* | Anger att egenskapsobjektet innehåller andra alternativ, till exempel plats, taggar, sku och/eller plan.
id | *Ja* | Resurs-ID för den anpassade resursen. Detta ska finnas ut från den **ResourceProvider**
properties | *Ja* | Begärandetexten som ska skickas till den **endpoint**.

Ta bort en anpassad Azure-resurs:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parameter | Obligatoriskt | Beskrivning
---|---|---
id | *Ja* | Resurs-ID för den anpassade resursen. Detta ska finnas ut från den **ResourceProvider**.

Hämta en Azure-anpassad resurs:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parameter | Obligatoriskt | Beskrivning
---|---|---
id | *Ja* | Resurs-ID för den anpassade resursen. Detta ska finnas ut från den **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

> [!NOTE]
> Resurser kräver att svaret innehåller ett lämpligt `id`, `name`, och `type` från den **endpoint**.

Azure Resource Manager-mallar kräver att `id`, `name`, och `type` korrekt returneras från den underordnade slutpunkten. Resurssvaret returneras bör vara i formatet:

Exemplet **endpoint** svaret:

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

Exemplet Azure Resource Manager-mall:

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

Parameter | Obligatoriskt | Beskrivning
---|---|---
resourceTypeName | *Ja* | Den **namn** av den **resourceType** definieras i den anpassade providern.
resourceProviderName | *Ja* | Anpassad provider instans resursnamnet.
customResourceName | *Ja* | Anpassade resursnamnet.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure anpassade Resursprovidrar](./custom-providers-overview.md)
- [Självstudie: Skapa anpassade Azure-Resursprovidern och distribuera anpassade resurser](./create-custom-provider.md)
- [Anvisningar: Att lägga till anpassade åtgärder i Azure REST-API](./custom-providers-action-endpoint-how-to.md)
- [Referens: Anpassad Proxy resursreferens](./custom-providers-proxy-resource-endpoint-reference.md)
- [Referens: Anpassad resurs Cache-referens](./custom-providers-proxy-cache-resource-endpoint-reference.md)
