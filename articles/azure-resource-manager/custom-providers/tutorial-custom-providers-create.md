---
title: Skapa och använda en anpassad leverantör
description: Den här självstudien visar hur du skapar och använder en Azure Custom Provider. Använd anpassade leverantörer för att ändra arbetsflöden på Azure.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 09df78955de6423244c2d8ec94e1e1c06ecab257
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650032"
---
# <a name="create-and-use-a-custom-provider"></a>Skapa och använda en anpassad leverantör

En anpassad provider är ett kontrakt mellan Azure och en slutpunkt. Med anpassade leverantörer kan du ändra arbetsflöden på Azure. Den här självstudien visar processen för att skapa en anpassad leverantör. Om du inte känner till Azure Custom Providers läser du [översikten över Azure Custom Resource Providers](overview.md).

## <a name="create-a-custom-provider"></a>Skapa en anpassad leverantör

> [!NOTE]
> Den här självstudien visar inte hur du skapar en slutpunkt. Om du inte har en RESTFUL slutpunkt, följ [handledningen om att skapa RESTful slutpunkter](./tutorial-custom-providers-function-authoring.md), som är grunden för den aktuella handledningen.

När du har skapat en slutpunkt kan du skapa en anpassad provider för att generera ett kontrakt mellan providern och slutpunkten. Med en anpassad leverantör kan du ange en lista över slutpunktsdefinitioner:

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Egenskap | Krävs | Beskrivning
---|---|---
**Namn** | Ja | Namnet på slutpunktsdefinitionen. Azure exponerar det här namnet via dess API under /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders<br>/resourceProviders/{resourceProviderName}/{endpointDefinitionName}
**routingType** | Inga | Kontraktstypen för slutpunkt. Om värdet inte anges är det som standard "Proxy".
**Slutpunkt** | Ja | Slutpunkten som begäranden ska dirigeras till. Den här slutpunkten hanterar svaret och eventuella biverkningar av begäran.

Värdet för **slutpunkten** är utlösar-URL:en för Azure-funktionsappen. Platshållarna `<yourapp>`, `<funcname>`och `<functionkey>` måste ersättas med värden för den skapade funktionsappen.

## <a name="define-custom-actions-and-resources"></a>Definiera anpassade åtgärder och resurser

Den anpassade providern innehåller en lista över slutpunktsdefinitioner som modelleras under **egenskaperna åtgärder** och **resourceTypes.** Egenskapen **Actions** mappar till anpassade åtgärder som exponeras av den anpassade providern och egenskapen **resourceTypes** är anpassade resurser. I den här självstudien har `myCustomAction` den anpassade providern `myCustomResources`en åtgärdsegenskap som heter och egenskapen **resourceTypes** med namnet . **actions**

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ],
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ]
  },
  "location": "eastus"
}
```

## <a name="deploy-the-custom-provider"></a>Distribuera den anpassade providern

> [!NOTE]
> Du måste ersätta **slutpunktsvärdena** med utlösar-URL:en från funktionsappen som skapades i föregående självstudiekurs.

Du kan distribuera den tidigare anpassade providern med hjälp av en Azure Resource Manager-mall:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders",
            "name": "myCustomProvider",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "actions": [
                    {
                        "name": "myCustomAction",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ],
                "resourceTypes": [
                    {
                        "name": "myCustomResources",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ]
            }
        }
    ]
}
```

## <a name="use-custom-actions-and-resources"></a>Använda anpassade åtgärder och resurser

När du har skapat en anpassad provider kan du använda de nya Azure-API:erna. På följande flikar förklara hur du ringer och använder en anpassad leverantör.

### <a name="custom-actions"></a>Anpassade åtgärder

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> Du måste `{subscriptionId}` ersätta `{resourceGroupName}` platshållarna och platshållarna med prenumerations- och resursgruppen där du distribuerade den anpassade providern.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Parameter | Krävs | Beskrivning
---|---|---
*Åtgärder* | Ja | Namnet på åtgärden som definierats i den anpassade providern
*Id* | Ja | Den anpassade providerns resurs-ID
*begäran-organ* | Inga | Det begärandeorgan som skickas till slutpunkten

# <a name="template"></a>[Mall](#tab/template)

Inga.

---

### <a name="custom-resources"></a>Anpassade resurser

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> Du måste `{subscriptionId}` ersätta `{resourceGroupName}` platshållarna och platshållarna med prenumerations- och resursgruppen där du distribuerade den anpassade providern.

#### <a name="create-a-custom-resource"></a>Skapa en anpassad resurs

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1 \
                   --properties
                    '{
                        "location": "eastus",
                        "properties": {
                            "hello" : "world"
                        }
                    }'
```

Parameter | Krävs | Beskrivning
---|---|---
*är-full-objekt* | Ja | Anger om egenskapsobjektet innehåller andra alternativ som plats, taggar, SKU eller plan.
*Id* | Ja | Resurs-ID för den anpassade resursen. Det här ID:t är ett tillägg till det anpassade providerresurs-ID:t.
*Egenskaper* | Ja | Det förfråkomsstext som skickas till slutpunkten.

#### <a name="delete-a-custom-resource"></a>Ta bort en anpassad resurs

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parameter | Krävs | Beskrivning
---|---|---
*Id* | Ja | Resurs-ID för den anpassade resursen. Det här ID:t är ett tillägg till det anpassade providerresurs-ID:t.

#### <a name="retrieve-a-custom-resource"></a>Hämta en anpassad resurs

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parameter | Krävs | Beskrivning
---|---|---
*Id* | Ja | Resurs-ID för den anpassade resursen. Det här ID:t är ett tillägg till det anpassade providerresurs-ID:t.

# <a name="template"></a>[Mall](#tab/template)

En exempelmall för Resource Manager:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "name": "myCustomProvider/myTestResourceName1",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "hello": "world"
            }
        }
    ]
}
```

Parameter | Krävs | Beskrivning
---|---|---
*resourceTypeName* | Ja | `name` Värdet för egenskapen **resourceTypes** som definierats i den anpassade providern.
*resursProviderNamn* | Ja | Det anpassade providerinstansnamnet.
*customResourceName* | Ja | Det anpassade resursnamnet.

---

> [!NOTE]
> När du har distribuerat och använt den anpassade providern, kom ihåg att rensa alla skapade resurser, inklusive Azure-funktionsappen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om anpassade leverantörer. Mer information finns i:

- [Så här lägger du till anpassade åtgärder i Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Så här lägger du till anpassade resurser i Azure REST API](./custom-providers-resources-endpoint-how-to.md)
