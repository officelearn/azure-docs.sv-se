---
title: Mallfunktioner - distribution
description: Beskriver de funktioner som ska användas i en Azure Resource Manager-mall för att hämta distributionsinformation.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 86a1d3d7e05fedacd7a3c044ecab241ca9d059c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156335"
---
# <a name="deployment-functions-for-arm-templates"></a>Distributionsfunktioner för ARM-mallar 

Resource Manager innehåller följande funktioner för att hämta värden relaterade till den aktuella distributionen av din AZURE Resource Manager-mall (ARM):

* [Distribution](#deployment)
* [Miljö](#environment)
* [Parametrar](#parameters)
* [Variabler](#variables)

Information om hur du hämtar värden från resurser, resursgrupper eller prenumerationer finns i [Resursfunktioner](template-functions-resource.md).

## <a name="deployment"></a>distribution

`deployment()`

Returnerar information om den aktuella distributionsåtgärden.

### <a name="return-value"></a>Returvärde

Den här funktionen returnerar objektet som skickas under distributionen. Egenskaperna i det returnerade objektet skiljer sig åt beroende på om distributionsobjektet skickas som en länk eller som ett in-line-objekt. När distributionsobjektet skickas in-line, till exempel när parametern **-TemplateFile** i Azure PowerShell används för att peka på en lokal fil, har det returnerade objektet följande format:

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

När objektet skickas som en länk, till exempel när parametern **-TemplateUri** används för att peka på ett fjärrobjekt, returneras objektet i följande format: 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

När du [distribuerar till en Azure-prenumeration](deploy-to-subscription.md)i stället `location` för en resursgrupp innehåller returobjektet en egenskap. Platsegenskapen inkluderas vid distribution av antingen en lokal mall eller en extern mall.

### <a name="remarks"></a>Anmärkningar

Du kan använda deployment() för att länka till en annan mall baserat på URI för den överordnade mallen.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

Om du distribuerar om en mall från distributionshistoriken i portalen distribueras mallen som en lokal fil. Egenskapen `templateLink` returneras inte i distributionsfunktionen. Om mallen är `templateLink` beroende av att skapa en länk till en annan mall ska du inte använda portalen för att distribuera om. Använd i stället de kommandon som du använde för att ursprungligen distribuera mallen.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) returnerar distributionsobjektet:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

I föregående exempel returneras följande objekt:

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "subscriptionOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

En mall på prenumerationsnivå som använder distributionsfunktionen finns i [prenumerationsdistributionsfunktionen](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json). Den distribueras med `az deployment create` `New-AzDeployment` antingen eller kommandon.

## <a name="environment"></a>miljö

`environment()`

Returnerar information om Azure-miljön som används för distribution.

### <a name="return-value"></a>Returvärde

Den här funktionen returnerar egenskaper för den aktuella Azure-miljön. I följande exempel visas egenskaperna för globala Azure. Suveräna moln kan returnera lite olika egenskaper.

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>Exempel

Följande exempelmall returnerar miljöobjektet.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "environmentOutput": {
            "value": "[environment()]",
            "type" : "object"
        }
    }
}
```

Föregående exempel returnerar följande objekt när det distribueras till globala Azure:

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Returnerar ett parametervärde. Det angivna parameternamnet måste definieras i parameteravsnittet i mallen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| parameterName (parameterName) |Ja |sträng |Namnet på parametern som ska returneras. |

### <a name="return-value"></a>Returvärde

Värdet för den angivna parametern.

### <a name="remarks"></a>Anmärkningar

Vanligtvis använder du parametrar för att ange resursvärden. I följande exempel anges namnet på webbplatsen på parametervärdet som skickades in under distributionen.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) visar en förenklad användning av parameterfunktionen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
        },
        "crossParameter": {
            "type": "string",
            "defaultValue": "[parameters('stringParameter')]"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "stringOutput": {
            "value": "[parameters('stringParameter')]",
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| stringOutput | String | alternativ 1 |
| intOutput (intOutput) | Int | 1 |
| objectOutput (objektOutput) | Objekt | {"en": "a", "två": "b"} |
| arrayOutput | Matris | [1, 2, 3] |
| crossOutput (korsUtskrift) | String | alternativ 1 |

Mer information om hur du använder parametrar finns [i Mallen Parametrar i Azure Resource Manager](template-parameters.md).

## <a name="variables"></a>Variabler

`variables(variableName)`

Returnerar variabelns värde. Det angivna variabelnamnet måste definieras i variabelavsnittet i mallen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| variableName (variabelNamn) |Ja |String |Namnet på variabeln som ska returneras. |

### <a name="return-value"></a>Returvärde

Värdet för den angivna variabeln.

### <a name="remarks"></a>Anmärkningar

Vanligtvis använder du variabler för att förenkla mallen genom att bara konstruera komplexa värden en gång. I följande exempel skapas ett unikt namn för ett lagringskonto.

```json
"variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
    },
    {
        "type": "Microsoft.Compute/virtualMachines",
        "dependsOn": [
            "[variables('storageName')]"
        ],
        ...
    }
],
```

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) returnerar olika variabelvärden.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
        "var3": "[ variables('var1') ]",
        "var4": {
            "property1": "value1",
            "property2": "value2"
          }
    },
    "resources": [],
    "outputs": {
        "exampleOutput1": {
            "value": "[variables('var1')]",
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| exempelUtst1 | String | myVariable |
| exempelUtflöde2 | Matris | [1, 2, 3, 4] |
| exempelUtflöde3 | String | myVariable |
| exempelUtflöde4 |  Objekt | {"property1": "value1", "property2": "value2"} |

Mer information om hur du använder variabler finns [i Variabler i Azure Resource Manager-mallen](template-variables.md).

## <a name="next-steps"></a>Nästa steg
* En beskrivning av avsnitten i en Azure Resource Manager-mall finns i [Redigera Azure Resource Manager-mallar](template-syntax.md).
* Information om hur du sammanfogar flera mallar finns [i Använda länkade mallar med Azure Resource Manager](linked-templates.md).
* Information om hur du itererar ett angivet antal gånger när du skapar en typ av resurs finns [i Skapa flera instanser av resurser i Azure Resource Manager](copy-resources.md).
* Information om hur du distribuerar mallen som du har skapat finns i [Distribuera ett program med Azure Resource Manager-mallen](deploy-powershell.md).

