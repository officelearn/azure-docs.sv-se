---
title: Mallfunktioner Azure Resource Manager - distribution | Microsoft Docs
description: "Beskriver funktionerna du använder i en Azure Resource Manager-mall för att hämta information om distribution."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2017
ms.author: tomfitz
ms.openlocfilehash: 17fe2bc467acc5542d021961a066940dbecf6120
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Distribution av funktioner för Azure Resource Manager-mallar 

Hanteraren för filserverresurser innehåller följande funktioner för att hämta värden från avsnitt i mallen och värden som rör distributionen:

* [distribution](#deployment)
* [parametrar](#parameters)
* [variabler](#variables)

För att hämta värden från resurser, resursgrupper eller prenumerationer, finns [resursfunktioner](resource-group-template-functions-resource.md).

<a id="deployment" />

## <a name="deployment"></a>distribution
`deployment()`

Returnerar information om den aktuella Distributionsåtgärden.

### <a name="return-value"></a>Returvärde

Den här funktionen returnerar det objekt som överförs under distributionen. Egenskaperna i det returnerade objektet variera beroende på om distributionsobjektet skickas som en länk eller som ett objekt i rad. När distributionsobjektet skickas i rad, som när du använder den **- TemplateFile** parametern i Azure PowerShell för att peka till en lokal fil det returnerade objektet har följande format:

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

När objektet har skickats som en länk som när du använder den **- TemplateUri** parametern för att peka på ett fjärrobjekt objektet returneras i följande format: 

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

### <a name="remarks"></a>Kommentarer

Du kan använda deployment() för att länka till en annan mall baserat på URI: N för den överordnade mallen.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

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

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

<a id="parameters" />

## <a name="parameters"></a>parameters
`parameters(parameterName)`

Returnerar ett parametervärde. Det angivna parameternamnet måste definieras i avsnittet parametrar i mallen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| parameterName |Ja |Sträng |Namnet på parametern för att returnera. |

### <a name="return-value"></a>Returvärde

Värdet för den angivna parametern.

### <a name="remarks"></a>Kommentarer

Normalt använder du parametrarna för att ange värden för resursen. I följande exempel anger namnet på webbplatsen till parametervärdet skickades under distributionen.

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

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) visar en förenklad användning av funktionen parametrar.

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

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| stringOutput | Sträng | Alternativ 1 |
| intOutput | int | 1 |
| objectOutput | Objekt | {”1”: ”a”, ”två”: ”b”} |
| arrayOutput | Matris | [1, 2, 3] |
| crossOutput | Sträng | Alternativ 1 |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

<a id="variables" />

## <a name="variables"></a>variabler
`variables(variableName)`

Returnerar värdet för variabeln. Det angivna variabelnamnet måste definieras i avsnittet variabler i mallen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| Variabelnamn |Ja |Sträng |Namnet på variabeln för att returnera. |

### <a name="return-value"></a>Returvärde

Värdet för den angivna variabeln.

### <a name="remarks"></a>Kommentarer

Normalt använder du variabler för att förenkla din mall genom att skapa komplexa värden bara en gång. I följande exempel skapas ett unikt namn för ett lagringskonto.

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

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| exampleOutput1 | Sträng | myVariable |
| exampleOutput2 | Matris | [1, 2, 3, 4] |
| exampleOutput3 | Sträng | myVariable |
| exampleOutput4 |  Objekt | {”egenskap1”: ”värde1”, ”egenskap2”: ”value2”} |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

## <a name="next-steps"></a>Nästa steg
* En beskrivning av avsnitt i en Azure Resource Manager-mallen finns [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Om du vill slå samman flera mallar, se [använda länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).
* Iterera ett angivet antal gånger när du skapar en typ av resurs finns [skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md).
* Information om hur du distribuerar mallen som du har skapat finns [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md).

