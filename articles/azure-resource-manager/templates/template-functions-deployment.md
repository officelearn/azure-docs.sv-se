---
title: Mall funktioner – distribution
description: Beskriver de funktioner som används i en Azure Resource Manager-mall för att hämta distributions information.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: b241aaf43ee3204c9960d0099ce3c61d4c1a80ee
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355071"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Distributions funktioner för Azure Resource Manager mallar 

Resource Manager innehåller följande funktioner för att hämta värden som är relaterade till den aktuella distributionen:

* [spridningen](#deployment)
* [miljö](#environment)
* [parameters](#parameters)
* [användarvariabler](#variables)

För att hämta värden från resurser, resurs grupper eller prenumerationer, se [resurs funktioner](template-functions-resource.md).

## <a name="deployment"></a>distribution

`deployment()`

Returnerar information om den aktuella distributions åtgärden.

### <a name="return-value"></a>Returvärde

Den här funktionen returnerar det objekt som skickas under distributionen. Egenskaperna i det returnerade objektet skiljer sig åt beroende på om distributions objekt skickas som en länk eller som ett infogat objekt. När distributions objekt skickas direkt, till exempel när du använder parametern **-TemplateFile** i Azure PowerShell för att peka på en lokal fil, har det returnerade objektet följande format:

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

När objektet skickas som en länk, till exempel när du använder parametern **-TemplateUri** för att peka på ett fjärrobjekt, returneras objektet i följande format: 

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

När du [distribuerar till en Azure-prenumeration](deploy-to-subscription.md), i stället för en resurs grupp, innehåller retur-objektet en `location`-egenskap. Egenskapen Location ingår när du distribuerar antingen en lokal mall eller en extern mall.

### <a name="remarks"></a>Anmärkningar

Du kan använda Deployment () för att länka till en annan mall baserat på den överordnade mallens URI.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

Om du distribuerar om en mall från distributions historiken i portalen distribueras mallen som en lokal fil. Egenskapen `templateLink` returneras inte i distributions funktionen. Om mallen använder `templateLink` för att skapa en länk till en annan mall ska du inte använda portalen för att distribuera om den. Använd i stället de kommandon som du använde för att distribuera mallen från början.

### <a name="example"></a>Exempel

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) returnerar distributions objekt:

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

Föregående exempel returnerar följande objekt:

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

En mall för en prenumerations nivå som använder distributions funktionen finns i [funktionen för prenumerations distribution](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json). Den distribueras med antingen `az deployment create`-eller `New-AzDeployment` kommandon.

## <a name="environment"></a>miljö

`environment()`

Returnerar information om den Azure-miljö som används för distribution.

### <a name="return-value"></a>Returvärde

Den här funktionen returnerar egenskaper för den aktuella Azure-miljön. I följande exempel visas egenskaperna för Global Azure. Suveräna moln kan returnera något annorlunda egenskaper.

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

Följande exempel-mall returnerar miljö objekt.

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

Föregående exempel returnerar följande objekt när det distribueras till Global Azure:

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

Returnerar ett parameter värde. Det angivna parameter namnet måste definieras i avsnittet Parameters i mallen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| parameterName |Ja |sträng |Namnet på den parameter som ska returneras. |

### <a name="return-value"></a>Returvärde

Värdet för den angivna parametern.

### <a name="remarks"></a>Anmärkningar

Normalt använder du parametrar för att ange resurs värden. I följande exempel anges namnet på webbplatsen till det parameter värde som överfördes under distributionen.

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

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) visas en förenklad användning av funktionen Parameters.

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
| intOutput | Int | 1 |
| objectOutput | Objekt | {"One": "a", "två": "b"} |
| arrayOutput | Matris | [1, 2, 3] |
| crossOutput | String | alternativ 1 |

Mer information om hur du använder parametrar finns [i parametrar i Azure Resource Manager mall](template-parameters.md).

## <a name="variables"></a>användarvariabler

`variables(variableName)`

Returnerar värdet för variabeln. Det angivna variabel namnet måste definieras i avsnittet Variables i mallen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| variableName |Ja |String |Namnet på variabeln som ska returneras. |

### <a name="return-value"></a>Returvärde

Värdet för den angivna variabeln.

### <a name="remarks"></a>Anmärkningar

Normalt använder du variabler för att förenkla din mall genom att endast konstruera komplexa värden en gång. I följande exempel skapas ett unikt namn för ett lagrings konto.

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

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) returnerar olika variabel värden.

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
| exampleOutput1 | String | Variabel |
| exampleOutput2 | Matris | [1, 2, 3, 4] |
| exampleOutput3 | String | Variabel |
| exampleOutput4 |  Objekt | {"Egenskap1": "värde1", "Egenskap2": "värde2"} |

Mer information om hur du använder variabler finns [i variabler i Azure Resource Manager mall](template-variables.md).

## <a name="next-steps"></a>Nästa steg
* En beskrivning av avsnitten i en Azure Resource Manager mall finns i [redigera Azure Resource Manager mallar](template-syntax.md).
* Information om hur du sammanfogar flera mallar finns i [använda länkade mallar med Azure Resource Manager](linked-templates.md).
* Om du vill iterera ett visst antal gånger när du skapar en typ av resurs, se [skapa flera instanser av resurser i Azure Resource Manager](copy-resources.md).
* Information om hur du distribuerar mallen som du har skapat finns i [distribuera ett program med Azure Resource Manager mall](deploy-powershell.md).

