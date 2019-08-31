---
title: Azure Resource Manager Template Functions-Deployment | Microsoft Docs
description: Beskriver de funktioner som används i en Azure Resource Manager-mall för att hämta distributions information.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: tomfitz
ms.openlocfilehash: 236fbb9e4ed3283ecf9147e6eb5033fb906a127b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194350"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Distributions funktioner för Azure Resource Manager mallar 

Resource Manager innehåller följande funktioner för att hämta värden från avsnitt i mallen och värden som är relaterade till distributionen:

* [spridningen](#deployment)
* [parameters](#parameters)
* [variables](#variables)

För att hämta värden från resurser, resurs grupper eller prenumerationer, se [resurs funktioner](resource-group-template-functions-resource.md).

<a id="deployment" />

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment"></a>deployment
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

När du [distribuerar till en Azure-prenumeration](deploy-to-subscription.md), i stället för en resurs grupp, innehåller retur- `location` objektet en egenskap. Egenskapen Location ingår när du distribuerar antingen en lokal mall eller en extern mall.

### <a name="remarks"></a>Kommentarer

Du kan använda Deployment () för att länka till en annan mall baserat på den överordnade mallens URI.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

Om du distribuerar om en mall från distributions historiken i portalen distribueras mallen som en lokal fil. `templateLink` Egenskapen returneras inte i distributions funktionen. Om mallen är beroende av `templateLink` att skapa en länk till en annan mall ska du inte använda portalen för att distribuera om den. Använd i stället de kommandon som du använde för att distribuera mallen från början.

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

Om du vill distribuera den här exempel-mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

Om du vill distribuera den här exempelmall med PowerShell använder du:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

En mall för en prenumerations nivå som använder distributions funktionen finns i [funktionen för prenumerations distribution](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json). Den distribueras med antingen `az deployment create` eller `New-AzDeployment` -kommandona.

<a id="parameters" />

## <a name="parameters"></a>parameters
`parameters(parameterName)`

Returnerar ett parameter värde. Det angivna parameter namnet måste definieras i avsnittet Parameters i mallen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| parameterName |Ja |sträng |Namnet på den parameter som ska returneras. |

### <a name="return-value"></a>Returvärde

Värdet för den angivna parametern.

### <a name="remarks"></a>Kommentarer

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

| Namn | Typ | Value |
| ---- | ---- | ----- |
| stringOutput | Sträng | alternativ 1 |
| intOutput | Int | 1 |
| objectOutput | Object | {"One": "a", "två": "b"} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | Sträng | alternativ 1 |

Om du vill distribuera den här exempel-mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

Om du vill distribuera den här exempelmall med PowerShell använder du:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

<a id="variables" />

## <a name="variables"></a>användarvariabler
`variables(variableName)`

Returnerar värdet för variabeln. Det angivna variabel namnet måste definieras i avsnittet Variables i mallen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| variableName |Ja |Sträng |Namnet på variabeln som ska returneras. |

### <a name="return-value"></a>Returvärde

Värdet för den angivna variabeln.

### <a name="remarks"></a>Kommentarer

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

| Namn | Typ | Value |
| ---- | ---- | ----- |
| exampleOutput1 | Sträng | Variabel |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | Sträng | Variabel |
| exampleOutput4 |  Object | {"Egenskap1": "värde1", "Egenskap2": "värde2"} |

Om du vill distribuera den här exempel-mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

Om du vill distribuera den här exempelmall med PowerShell använder du:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

## <a name="next-steps"></a>Nästa steg
* En beskrivning av avsnitt i en Azure Resource Manager-mall finns i [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Information om hur du sammanfogar flera mallar finns i [använda länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).
* Iterera ett angivet antal gånger när du skapar en typ av resurs, finns i [och skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md).
* Om du vill se hur du distribuerar mallen som du har skapat, se [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md).

