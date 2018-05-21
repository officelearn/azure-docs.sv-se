---
title: Azure Resource Manager Mallfunktioner - numeriska | Microsoft Docs
description: Beskriver funktionerna du använder i en Azure Resource Manager-mall för att arbeta med siffror.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 4fc17b997c44560199e65edb01d20c6a24e49877
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="numeric-functions-for-azure-resource-manager-templates"></a>Numeriska funktioner för Azure Resource Manager-mallar

Hanteraren för filserverresurser innehåller följande funktioner för att arbeta med heltal:

* [Lägg till](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [flyttal](#float)
* [int](#int)
* [max](#max)
* [Min](#min)
* [MOD](#mod)
* [mul](#mul)
* [Sub](#sub)

<a id="add" />

## <a name="add"></a>lägg till
`add(operand1, operand2)`

Returnerar summan av de två angivna heltal.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- | 
|operand1 |Ja |int |Första numret att lägga till. |
|operand2 |Ja |int |Andra talet att lägga till. |

### <a name="return-value"></a>Returvärde

Ett heltal som innehåller summan av parametrar.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) lägger till två parametrar.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to add"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "addResult": {
            "type": "int",
            "value": "[add(parameters('first'), parameters('second'))]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| addResult | Int | 8 |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json 
```

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(loopName, offset)`

Returnerar index för en upprepning loop. 

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| loopName | Nej | sträng | Namnet på slingan för att hämta upprepning. |
| förskjutning |Nej |int |Siffra och lägger till värdet nollbaserade iteration. |

### <a name="remarks"></a>Kommentarer

Den här funktionen används alltid med en **kopiera** objekt. Om inget värde har angetts för **offset**, det aktuella värdet för upprepning returneras. Värdet för upprepning börjar på noll. Du kan använda iteration slingor när du definierar resurser eller variabler.

Den **loopName** egenskapen kan du ange om copyIndex hänvisar till en resurs iteration eller egenskapen iteration. Om inget värde har angetts för **loopName**, den aktuella resursen typen iterationen används. Ange ett värde för **loopName** när iteration av en egenskap. 
 
En fullständig beskrivning av hur du använder **copyIndex**, se [skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md).

Ett exempel på hur du använder **copyIndex** när du definierar en variabel, se [variabler](resource-group-authoring-templates.md#variables).

### <a name="example"></a>Exempel

I följande exempel visas en kopia loop och indexvärdet ingår i namnet. 

```json
"resources": [ 
  { 
    "name": "[concat('examplecopy-', copyIndex())]", 
    "type": "Microsoft.Web/sites", 
    "copy": { 
      "name": "websitescopy", 
      "count": "[parameters('count')]" 
    }, 
    ...
  }
]
```

### <a name="return-value"></a>Returvärde

Ett heltal som representerar det aktuella indexet för upprepning.

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

Returnerar heltalsdivisionen av två angivna heltal.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| operand1 |Ja |int |Det tal som delas. |
| operand2 |Ja |int |Det tal som används för att dela upp. Det går inte att vara 0. |

### <a name="return-value"></a>Returvärde

Ett heltal som representerar delningen.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) delar en parameter av en annan parametern.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 8,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[div(parameters('first'), parameters('second'))]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| divResult | Int | 2 |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json 
```

<a id="float" />

## <a name="float"></a>flyt
`float(arg1)`

Konverterar värdet till ett flyttal peka nummer. Du kan bara använda den här funktionen vid sändning av anpassade parametrar till ett program, till exempel en Logikapp.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |sträng eller ett heltal |Värdet som ska konverteras till ett flyttal peka nummer. |

### <a name="return-value"></a>Returvärde
En flytande peka nummer.

### <a name="example"></a>Exempel

I följande exempel visas hur du använder float för att skicka parametrar till en Logikapp:

```json
{
    "type": "Microsoft.Logic/workflows",
    "properties": {
        ...
        "parameters": {
            "custom1": {
                "value": "[float('3.0')]"
            },
            "custom2": {
                "value": "[float(3)]"
            },
```

<a id="int" />

## <a name="int"></a>int
`int(valueToConvert)`

Konverterar det angivna värdet till ett heltal.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| valueToConvert |Ja |sträng eller ett heltal |Värdet som ska konverteras till ett heltal. |

### <a name="return-value"></a>Returvärde

Ett heltal av det konvertera värdet.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) konverterar som användaren tillhandahållit parametervärdet heltal.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToConvert": { 
            "type": "string",
            "defaultValue": "4"
        }
    },
    "resources": [
    ],
    "outputs": {
        "intResult": {
            "type": "int",
            "value": "[int(parameters('stringToConvert'))]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| intResult | Int | 4 |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

<a id="max" />

## <a name="max"></a>max
`max (arg1)`

Returnerar det största värdet från en matris av heltal eller en kommaavgränsad lista med heltal.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris med heltal eller en kommaavgränsad lista med heltal |Samlingen för att hämta det högsta värdet. |

### <a name="return-value"></a>Returvärde

Ett heltal som representerar det högsta värdet från samlingen.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) visar hur du använder max med en matris och en lista med heltal:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>min.
`min (arg1)`

Returnerar det lägsta värdet från en heltalsmatris eller en kommaavgränsad lista med heltal.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris med heltal eller en kommaavgränsad lista med heltal |Samlingen för att hämta det minsta värdet. |

### <a name="return-value"></a>Returvärde

Ett heltal som representerar minimivärdet från samlingen.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) visar hur du använder min med en matris och en lista med heltal:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="mod" />

## <a name="mod"></a>MOD
`mod(operand1, operand2)`

Returnerar resten av Heltalsdivision med två angivna heltal.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| operand1 |Ja |int |Det tal som delas. |
| operand2 |Ja |int |Talet som används för att dela upp, får inte vara 0. |

### <a name="return-value"></a>Returvärde
Ett heltal som representerar resten.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) returnerar resten av att dividera en parameter av en annan parametern.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "modResult": {
            "type": "int",
            "value": "[mod(parameters('first'), parameters('second'))]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| modResult | Int | 1 |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

<a id="mul" />

## <a name="mul"></a>mul
`mul(operand1, operand2)`

Returnerar multiplicering av två angivna heltal.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| operand1 |Ja |int |Första talet som multipliceras. |
| operand2 |Ja |int |Andra talet som multipliceras. |

### <a name="return-value"></a>Returvärde

Ett heltal som representerar multiplikationen.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) multiplicerar en parameter av en annan parametern.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to multiply"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "mulResult": {
            "type": "int",
            "value": "[mul(parameters('first'), parameters('second'))]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

<a id="sub" />

## <a name="sub"></a>Sub
`sub(operand1, operand2)`

Returnerar subtraktion av två angivna heltal.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| operand1 |Ja |int |Talet som subtraktionen från. |
| operand2 |Ja |int |Numret som subtraheras. |

### <a name="return-value"></a>Returvärde
Ett heltal som representerar subtraktion.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) subtraherar en parameter från en annan parameter.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer to subtract"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "subResult": {
            "type": "int",
            "value": "[sub(parameters('first'), parameters('second'))]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| subResult | Int | 4 |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

## <a name="next-steps"></a>Nästa steg
* En beskrivning av avsnitt i en Azure Resource Manager-mallen finns [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Om du vill slå samman flera mallar, se [använda länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).
* Iterera ett angivet antal gånger när du skapar en typ av resurs finns [skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md).
* Information om hur du distribuerar mallen som du har skapat finns [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md).

