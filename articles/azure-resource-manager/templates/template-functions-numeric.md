---
title: Mallfunktioner - numeriska
description: Beskriver de funktioner som ska användas i en Azure Resource Manager-mall för att arbeta med siffror.
ms.topic: conceptual
ms.date: 11/08/2017
ms.openlocfilehash: 2ca5c539036d002b83b8141132a0ebf2530dc6af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156352"
---
# <a name="numeric-functions-for-arm-templates"></a>Numeriska funktioner för ARM-mallar

Resource Manager innehåller följande funktioner för att arbeta med heltal i din AZURE Resource Manager -mall (ARM):

* [Add](#add)
* [copyIndex (på)](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [Max](#max)
* [Min](#min)
* [Mod](#mod)
* [mul (mul)](#mul)
* [Sub](#sub)

<a id="add" />

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="add"></a>add
`add(operand1, operand2)`

Returnerar summan av de två angivna heltalen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
|operand1 (på andra plats) |Ja |int |Första numret att lägga till. |
|operand2 (operand) |Ja |int |Andra numret att lägga till. |

### <a name="return-value"></a>Returvärde

Ett heltal som innehåller summan av parametrarna.

### <a name="example"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) läggs två parametrar till.

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| addResult | Int | 8 |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex (på)
`copyIndex(loopName, offset)`

Returnerar indexet för en iterationsloop.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| loopName (loopName) | Inga | sträng | Namnet på loopen för att få iterationen. |
| offset |Inga |int |Talet som ska läggas till i det nollbaserade iterationsvärdet. |

### <a name="remarks"></a>Anmärkningar

Den här funktionen används **copy** alltid med ett kopieringsobjekt. Om inget värde anges för **förskjutning**returneras det aktuella iterationsvärdet. Iterationsvärdet börjar på noll. Du kan använda iterationsloopar när du definierar antingen resurser eller variabler.

Med egenskapen **loopName** kan du ange om copyIndex refererar till en resursiteration eller egenskapsiteration. Om inget värde anges för **loopName**används den aktuella resurstypeniterationen. Ange ett värde för **loopName** när denerar på en egenskap.

En fullständig beskrivning av hur du använder **copyIndex**finns [i Skapa flera instanser av resurser i Azure Resource Manager](copy-resources.md).

Ett exempel på hur du använder **copyIndex** när du definierar en variabel finns i [Variabler](template-syntax.md#variables).

### <a name="example"></a>Exempel

I följande exempel visas en kopieringsloop och det indexvärde som ingår i namnet.

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

Ett heltal som representerar det aktuella indexet för iterationen.

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

Returnerar heltalsfördelningen för de två angivna heltalen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| operand1 (på andra plats) |Ja |int |Numret delas. |
| operand2 (operand) |Ja |int |Talet som används för att dela upp. Kan inte vara 0. |

### <a name="return-value"></a>Returvärde

Ett heltal som representerar divisionen.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) delar en parameter upp med en annan parameter.

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| divResult (olika resultat) | Int | 2 |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

<a id="float" />

## <a name="float"></a>float
`float(arg1)`

Konverterar värdet till ett flyttalsnummer. Du använder bara den här funktionen när du skickar anpassade parametrar till ett program, till exempel en logikapp.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |sträng eller int |Värdet som ska konverteras till ett flyttalsnummer. |

### <a name="return-value"></a>Returvärde
Ett flyttalsnummer.

### <a name="example"></a>Exempel

I följande exempel visas hur du använder float för att skicka parametrar till en logikapp:

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
| värdeTillKonvertera |Ja |sträng eller int |Värdet som ska konverteras till ett heltal. |

### <a name="return-value"></a>Returvärde

Ett heltal med det konverterade värdet.

### <a name="example"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) konverteras parametervärdet som tillhandahålls av användaren till heltal.

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| intResult (intResult) | Int | 4 |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

<a id="max" />

## <a name="max"></a>Max
`max (arg1)`

Returnerar det maximala värdet från en matris med heltal eller en kommaavgränsad lista med heltal.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |matris med heltal eller kommaavgränsad lista över heltal |Samlingen för att få det maximala värdet. |

### <a name="return-value"></a>Returvärde

Ett heltal som representerar det maximala värdet från samlingen.

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput (intOutput) | Int | 5 |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>min
`min (arg1)`

Returnerar minimivärdet från en matris med heltal eller en kommaavgränsad lista med heltal.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |matris med heltal eller kommaavgränsad lista över heltal |Samlingen för att få det lägsta värdet. |

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput (intOutput) | Int | 0 |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="mod" />

## <a name="mod"></a>Mod
`mod(operand1, operand2)`

Returnerar resten av heltalsdelningen med hjälp av de två angivna heltalen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| operand1 (på andra plats) |Ja |int |Numret delas. |
| operand2 (operand) |Ja |int |Talet som används för att dela, Kan inte vara 0. |

### <a name="return-value"></a>Returvärde
Ett heltal som representerar resten.

### <a name="example"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) returneras resten av att dividera en parameter med en annan parameter.

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| modResult (olika) | Int | 1 |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

<a id="mul" />

## <a name="mul"></a>mul (mul)
`mul(operand1, operand2)`

Returnerar multiplikationen för de två angivna heltalen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| operand1 (på andra plats) |Ja |int |Första talet att multiplicera. |
| operand2 (operand) |Ja |int |Andra talet att multiplicera. |

### <a name="return-value"></a>Returvärde

Ett heltal som representerar multiplikationen.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) multiplicerar en parameter med en annan parameter.

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| mulResult (mulResult) | Int | 15 |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

<a id="sub" />

## <a name="sub"></a>Sub
`sub(operand1, operand2)`

Returnerar subtraktionen för de två angivna heltalen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| operand1 (på andra plats) |Ja |int |Numret som subtraheras från. |
| operand2 (operand) |Ja |int |Numret som subtraheras. |

### <a name="return-value"></a>Returvärde
Ett heltal som representerar subtraktionen.

### <a name="example"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) subtraherar en parameter från en annan parameter.

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| subResult (subResult) | Int | 4 |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

## <a name="next-steps"></a>Nästa steg
* En beskrivning av avsnitten i en Azure Resource Manager-mall finns i [Redigera Azure Resource Manager-mallar](template-syntax.md).
* Information om hur du sammanfogar flera mallar finns [i Använda länkade mallar med Azure Resource Manager](linked-templates.md).
* Information om hur du itererar ett angivet antal gånger när du skapar en typ av resurs finns [i Skapa flera instanser av resurser i Azure Resource Manager](copy-resources.md).
* Information om hur du distribuerar mallen som du har skapat finns i [Distribuera ett program med Azure Resource Manager-mallen](deploy-powershell.md).

