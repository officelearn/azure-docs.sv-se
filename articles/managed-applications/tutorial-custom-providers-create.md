---
title: Skapa och använda en anpassad Provider för Azure
description: Den här självstudien får du gå igenom hur du skapar och använder en anpassad provider.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 65a8e60d8216e1da16af987c9e699e24ecaec3ec
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800003"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Redigering av en RESTful-slutpunkt för anpassade providers

Anpassade providers kan du anpassa arbetsflöden på Azure. En anpassad provider är ett avtal mellan Azure och en `endpoint`. Den här självstudien går igenom processen för att skapa en anpassad provider. Om du inte känner till Azure anpassade Providers, se [översikt över anpassade resursprovidrar](./custom-providers-overview.md).

Den här självstudien är uppdelad i följande steg:

- Vad är en anpassad provider
- Definiera anpassade åtgärder och resurser
- Distribuera den anpassade providern

Den här självstudiekursen skapar följande självstudiekurser:

- [Redigering av en RESTful-slutpunkt för anpassade providers](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-a-custom-provider"></a>Skapa en anpassad provider

> [!NOTE]
> Den här självstudiekursen kommer inte gå igenom redigering av en slutpunkt. Följ den [självstudiekurs redigering RESTful-slutpunkter](./tutorial-custom-providers-function-authoring.md) om du inte har en RESTful-slutpunkt.

När den `endpoint` har skapats kan du generera skapa en anpassad provider för att generera ett avtal mellan den och `endpoint`. En anpassad provider kan du ange en lista över slutpunktsdefinitionerna.

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Egenskap | Krävs | Beskrivning
---|---|---
name | *Ja* | Namnet på slutpunktsdefinitionen. Azure visas detta namn via dess API under ”/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *no* | Anger kontraktstypen med den `endpoint`. Om inte anges kommer det standard ”Proxy”.
endpoint | *Ja* | Slutpunkt för att dirigera begäranden till. Det hanterar svaret samt effekterna på serversidan av begäran.

I det här fallet den `endpoint` är utlösare-URL: en för Azure-funktion. Den `<yourapp>`, `<funcname>`, och `<functionkey>` ska ersättas med värden för din skapade funktion.

## <a name="defining-custom-actions-and-resources"></a>Definiera anpassade åtgärder och resurser

Den anpassade providern innehåller en lista över slutpunktsdefinitionerna modelleras `actions` och `resourceTypes`. `actions` mappa till de anpassade åtgärderna visas av den anpassade providern, medan `resourceTypes` är anpassade resurser. Den här självstudien definierar vi en anpassad provider med en `action` kallas `myCustomAction` och en `resourceType` kallas `myCustomResources`.

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

Ersätt `endpoint` med URL-Adressen för utlösare från funktionen skapade tidigare i den föregående självstudiekursen.

## <a name="deploying-the-custom-provider"></a>Distribuera den anpassade providern

> [!NOTE]
> Den `endpoint` ska ersättas med funktionens URL.

Den anpassade providern ovan kan distribueras med hjälp av en Azure Resource Manager-mall.

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

## <a name="using-custom-actions-and-resources"></a>Med hjälp av anpassade åtgärder och resurser

När vi har skapat en anpassad provider, kan vi använda den nya Azure-API: er. Nästa avsnitt förklarar hur du anropar och använda en anpassad provider.

### <a name="custom-actions"></a>Anpassade åtgärder

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> Den `{subscriptionId}` och `{resourceGroupName}` ska ersättas med prenumerationen och resursgruppen där den anpassade providern har distribuerats.

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
action | *Ja* | Namnet på åtgärden som definieras i den skapa anpassade providern.
ID: n | *Ja* | Resurs-ID för den skapa anpassade providern.
brödtext för begäran | *no* | Begärandetexten som ska skickas till den `endpoint`.

# <a name="templatetabtemplate"></a>[Mall](#tab/template)

Ingen.

---

### <a name="custom-resources"></a>Anpassade resurser

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> Den `{subscriptionId}` och `{resourceGroupName}` ska ersättas med prenumerationen och resursgruppen där den anpassade providern har distribuerats.

Skapa en anpassad resurs:

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
is-full-object | *Ja* | Anger att egenskapsobjektet innehåller andra alternativ, till exempel plats, taggar, sku och/eller plan.
id | *Ja* | Resurs-ID för den anpassade resursen. Detta ska finnas på den skapa anpassade providern.
properties | *Ja* | Begärandetexten som ska skickas till den `endpoint`.

Ta bort en anpassad Azure-resurs:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parameter | Krävs | Beskrivning
---|---|---
id | *Ja* | Resurs-ID för den anpassade resursen. Detta ska finnas på den skapa anpassade providern.

Hämta en Azure-anpassad resurs:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parameter | Krävs | Beskrivning
---|---|---
id | *Ja* | Resurs-ID för den anpassade resursen. Detta ska finnas på den skapa anpassade providern.

# <a name="templatetabtemplate"></a>[Mall](#tab/template)

Exemplet Azure Resource Manager-mall:

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
resourceTypeName | *Ja* | Den `name` av den *resourceType* definieras i den anpassade providern.
resourceProviderName | *Ja* | Anpassad provider-instansnamnet.
customResourceName | *Ja* | Anpassade resursnamnet.

---

> [!NOTE]
> När du är klar distribuerar och använder den anpassade providern, Kom ihåg att rensa alla skapade resurser, inklusive Azure Function.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om anpassade providers. Gå till nästa artikel för att skapa en anpassad provider.

- [Anvisningar: Att lägga till anpassade åtgärder i Azure REST-API](./custom-providers-action-endpoint-how-to.md)
- [Anvisningar: Att lägga till anpassade resurser i Azure REST-API](./custom-providers-resources-endpoint-how-to.md)
