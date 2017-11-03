---
title: Azure Resource Manager-mall fungerar - matriser och -objekt | Microsoft Docs
description: "Beskriver funktionerna som ska användas i en Azure Resource Manager-mall för att arbeta med matriser och -objekt."
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
ms.openlocfilehash: 7d040fe55cb46665c97668a76ccbc66adc002f89
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="array-and-object-functions-for-azure-resource-manager-templates"></a>Array- och funktioner för Azure Resource Manager-mallar 

Resource Manager innehåller flera funktioner för att arbeta med matriser och -objekt.

* [matris](#array)
* [Slå samman](#coalesce)
* [concat](#concat)
* [innehåller](#contains)
* [createArray](#createarray)
* [tom](#empty)
* [första](#first)
* [skärningspunkten](#intersection)
* [JSON](#json)
* [senaste](#last)
* [längd](#length)
* [Max](#max)
* [Min](#min)
* [intervallet](#range)
* [Hoppa över](#skip)
* [ta](#take)
* [Union](#union)

En matris med strängvärden som avgränsas med ett värde finns [dela](resource-group-template-functions-string.md#split).

<a id="array" />

## <a name="array"></a>matris
`array(convertToArray)`

Konverterar värdet till en matris.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| convertToArray |Ja |int, string, matris eller objekt |Värdet som ska konverteras till en matris. |

### <a name="return-value"></a>Returvärde

En matris.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/array.json) visar hur du använder funktionen matris med olika typer.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "intToConvert": {
            "type": "int",
            "defaultValue": 1
        },
        "stringToConvert": {
            "type": "string",
            "defaultValue": "a"
        },
        "objectToConvert": {
            "type": "object",
            "defaultValue": {"a": "b", "c": "d"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "intOutput": {
            "type": "array",
            "value": "[array(parameters('intToConvert'))]"
        },
        "stringOutput": {
            "type": "array",
            "value": "[array(parameters('stringToConvert'))]"
        },
        "objectOutput": {
            "type": "array",
            "value": "[array(parameters('objectToConvert'))]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| intOutput | Matris | [1] |
| stringOutput | Matris | [”a”] |
| objectOutput | Matris | [{”a”: ”b”, ”c”: ”d”}] |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

<a id="coalesce" />

## <a name="coalesce"></a>Slå samman
`coalesce(arg1, arg2, arg3, ...)`

Returnerar första icke-null-värde från parametrarna. Tomma strängar, tomma matriser och tomt-objekt är inte null.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int, string, matris eller objekt |Det första värdet för null. |
| ytterligare argument |Nej |int, string, matris eller objekt |Ytterligare värden att testa till null. |

### <a name="return-value"></a>Returvärde

Värdet för de första icke-null-parametrar, som kan vara en sträng, int, matris eller ett objekt. Null om alla parametrar är null. 

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) visar utdata från olika användningsområden för coalesce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "null1": null, 
                "null2": null,
                "string": "default",
                "int": 1,
                "object": {"first": "default"},
                "array": [1]
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').string)]"
        },
        "intOutput": {
            "type": "int",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').int)]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').object)]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').array)]"
        },
        "emptyOutput": {
            "type": "bool",
            "value": "[empty(coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2))]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| stringOutput | Sträng | Standard |
| intOutput | int | 1 |
| objectOutput | Objekt | {”första”: ”default”} |
| arrayOutput | Matris | [1] |
| emptyOutput | bool | True |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

<a id="concat" />

## <a name="concat"></a>concat
`concat(arg1, arg2, arg3, ...)`

Kombinerar flera matriser och returnerar sammanfogad matris eller kombinerar flera strängvärden och returnerar en sammanfogad sträng. 

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris eller sträng |Den första matrisen eller sträng för sammanslagning. |
| ytterligare argument |Nej |matris eller sträng |Ytterligare matriser eller strängar i tur och ordning för sammanslagning. |

Den här funktionen kan ta valfritt antal argument och kan acceptera strängar eller matriser för parametrarna.

### <a name="return-value"></a>Returvärde
En sträng eller en matris med sammanfogade värdena.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) visar hur du kombinerar två matriser.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "firstArray": { 
            "type": "array", 
            "defaultValue": [ 
                "1-1", 
                "1-2", 
                "1-3" 
            ] 
        },
        "secondArray": {
            "type": "array", 
            "defaultValue": [ 
                "2-1", 
                "2-2",
                "2-3" 
            ] 
        }
    },
    "resources": [
    ],
    "outputs": {
        "return": {
            "type": "array",
            "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| Returnera | Matris | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) visar hur du kombinerar två strängvärden och returnerar en sammanfogad sträng.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "prefix": {
            "type": "string",
            "defaultValue": "prefix"
        }
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| concatOutput | Sträng | prefixet 5yj4yjf5mbg72 |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

<a id="contains" />

## <a name="contains"></a>Innehåller
`contains(container, itemToFind)`

Kontrollerar om en matris som innehåller ett värde, ett objekt som innehåller en nyckel eller en sträng som innehåller understrängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| Behållaren |Ja |matris, objekt eller sträng |Det värde som innehåller ett värde att söka efter. |
| itemToFind |Ja |sträng eller ett heltal |Värde att söka efter. |

### <a name="return-value"></a>Returvärde

**SANT** om objektet är hittas, annars **FALSKT**.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) visar hur du använder innehåller med olika typer:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringTrue": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'e')]"
        },
        "stringFalse": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'z')]"
        },
        "objectTrue": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'one')]"
        },
        "objectFalse": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'a')]"
        },
        "arrayTrue": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'three')]"
        },
        "arrayFalse": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'four')]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| stringTrue | bool | True |
| stringFalse | bool | False |
| objectTrue | bool | True |
| objectFalse | bool | False |
| arrayTrue | bool | True |
| arrayFalse | bool | False |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

<a id="createarray" />

## <a name="createarray"></a>createarray
`createArray (arg1, arg2, arg3, ...)`

Skapar en matris av parametrarna.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Sträng, heltal, matris eller objekt |Det första värdet i matrisen. |
| ytterligare argument |Nej |Sträng, heltal, matris eller objekt |Ytterligare värdena i matrisen. |

### <a name="return-value"></a>Returvärde

En matris.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/createarray.json) visar hur du använder createArray med olika typer:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringArray": {
            "type": "array",
            "value": "[createArray('a', 'b', 'c')]"
        },
        "intArray": {
            "type": "array",
            "value": "[createArray(1, 2, 3)]"
        },
        "objectArray": {
            "type": "array",
            "value": "[createArray(parameters('objectToTest'))]"
        },
        "arrayArray": {
            "type": "array",
            "value": "[createArray(parameters('arrayToTest'))]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| stringArray | Matris | [”a”, ”b”, ”c”] |
| intArray | Matris | [1, 2, 3] |
| objectArray | Matris | [{”1”: ”a”, ”två”: ”b”, ”tre”: ”c”}] |
| arrayArray | Matris | [[”1”, ”två”, ”tre”]] |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

<a id="empty" />

## <a name="empty"></a>tom

`empty(itemToTest)`

Anger om en matris, objekt eller sträng är tom.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |matris, objekt eller sträng |Värdet för att kontrollera om den är tom. |

### <a name="return-value"></a>Returvärde

Returnerar **SANT** om värdet är tomt, annars **FALSKT**.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) kontrollerar om en matris och objektet sträng är tom.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": []
        },
        "testObject": {
            "type": "object",
            "defaultValue": {}
        },
        "testString": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testArray'))]"
        },
        "objectEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testObject'))]"
        },
        "stringEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testString'))]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayEmpty | bool | True |
| objectEmpty | bool | True |
| stringEmpty | bool | True |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

<a id="first" />

## <a name="first"></a>första
`first(arg1)`

Returnerar det första elementet i matrisen eller första tecknet i strängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris eller sträng |Värdet för att hämta det första elementet eller tecken. |

### <a name="return-value"></a>Returvärde

Typen (sträng, int, matris eller objekt) för det första elementet i en matris eller det första tecknet i en sträng.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) visar hur du använder den första funktionen med en matris och en sträng.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[first(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[first('One Two Three')]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayOutput | Sträng | en |
| stringOutput | Sträng | O |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

<a id="intersection" />

## <a name="intersection"></a>skärningspunkten
`intersection(arg1, arg2, arg3, ...)`

Returnerar en enda matris eller ett objekt med vanliga element från parametrarna.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris eller ett objekt |Det första värdet som ska användas för att söka efter vanliga element. |
| arg2 |Ja |matris eller ett objekt |Det andra värdet som ska användas för att söka efter vanliga element. |
| ytterligare argument |Nej |matris eller ett objekt |Ytterligare värden som ska användas för att söka efter vanliga element. |

### <a name="return-value"></a>Returvärde

En matris eller ett objekt med vanliga element.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) visar hur du använder skärningspunkten med matriser och -objekt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "z", "three": "c"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[intersection(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[intersection(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| objectOutput | Objekt | {”1”: ”a”, ”tre”: ”c”} |
| arrayOutput | Matris | [”två”, ”tre”] |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

## <a name="json"></a>JSON
`json(arg1)`

Returnerar ett JSON-objekt.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Sträng |Värdet som ska konverteras till JSON. |


### <a name="return-value"></a>Returvärde

JSON-objekt från den angivna strängen eller ett tomt-objekt när **null** har angetts.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) visar hur du använder funktionen json-matriser och-objekt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "jsonOutput": {
            "type": "object",
            "value": "[json('{\"a\": \"b\"}')]"
        },
        "nullOutput": {
            "type": "bool",
            "value": "[empty(json('null'))]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| jsonOutput | Objekt | {”a”: ”b”} |
| nullOutput | Booleskt värde | True |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

<a id="last" />

## <a name="last"></a>senaste
`last (arg1)`

Returnerar det sista elementet i matrisen eller sista tecknet i strängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris eller sträng |Värdet för att hämta det sista elementet eller tecken. |

### <a name="return-value"></a>Returvärde

Typen (sträng, int, matris eller objekt) för det sista elementet i en matris eller det sista tecknet i en sträng.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) visar hur du använder den senaste funktionen med en matris och strängen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[last(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[last('One Two Three')]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayOutput | Sträng | tre |
| stringOutput | Sträng | E |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

<a id="length" />

## <a name="length"></a>Längd
`length(arg1)`

Returnerar antalet element i en matris eller tecken i en sträng.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris eller sträng |Matrisen ska användas för att hämta antalet element eller strängen som ska användas för att hämta antalet tecken. |

### <a name="return-value"></a>Returvärde

Int. 

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) visar hur du använder längd med en matris och sträng:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "stringToTest": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "arrayLength": {
            "type": "int",
            "value": "[length(parameters('arrayToTest'))]"
        },
        "stringLength": {
            "type": "int",
            "value": "[length(parameters('stringToTest'))]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13 |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Du kan ange antal upprepningar när du skapar resurser genom att använda den här funktionen med en matris. I följande exempel parametern **siteNames** referera till en matris med namn som ska använda när du skapar webbplatser.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

Mer information om hur du använder den här funktionen med en matris finns [skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md).

<a id="max" />

## <a name="max"></a>Max
`max(arg1)`

Returnerar det största värdet från en matris av heltal eller en kommaavgränsad lista med heltal.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris med heltal eller en kommaavgränsad lista med heltal |Samlingen för att hämta det högsta värdet. |

### <a name="return-value"></a>Returvärde

Ett heltal som representerar det högsta värdet.

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
| arrayOutput | int | 5 |
| intOutput | int | 5 |

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
`min(arg1)`

Returnerar det lägsta värdet från en heltalsmatris eller en kommaavgränsad lista med heltal.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris med heltal eller en kommaavgränsad lista med heltal |Samlingen för att hämta det minsta värdet. |

### <a name="return-value"></a>Returvärde

Ett heltal som representerar det minsta värdet.

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
| arrayOutput | int | 0 |
| intOutput | int | 0 |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="range" />

## <a name="range"></a>intervallet
`range(startingInteger, numberOfElements)`

Skapar en heltalsmatris från början heltal och som innehåller ett antal objekt.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| startingInteger |Ja |int |Det första heltal i matrisen. |
| numberofElements |Ja |int |Antal heltal i matrisen. |

### <a name="return-value"></a>Returvärde

En heltalsmatris.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/range.json) visar hur du använder funktionen intervall:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "startingInt": {
            "type": "int",
            "defaultValue": 5
        },
        "numberOfElements": {
            "type": "int",
            "defaultValue": 3
        }
    },
    "resources": [],
    "outputs": {
        "rangeOutput": {
            "type": "array",
            "value": "[range(parameters('startingInt'),parameters('numberOfElements'))]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| rangeOutput | Matris | [5, 6, 7] |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

<a id="skip" />

## <a name="skip"></a>Hoppa över
`skip(originalValue, numberToSkip)`

Returnerar en matris med alla element efter det angivna värdet i matrisen eller returnerar en sträng med alla tecken efter det angivna värdet i strängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| Ursprungligt värde |Ja |matris eller sträng |Matris eller sträng som ska användas för att hoppa över. |
| numberToSkip |Ja |int |Antalet element eller tecken som ska hoppas över. Om det här värdet är 0 eller mindre, returneras alla element eller tecken i-värdet. Om den är större än längden på matrisen eller sträng returneras en tom matris eller sträng. |

### <a name="return-value"></a>Returvärde

En matris eller sträng.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) hoppar över det angivna antalet element i matrisen och det angivna antalet tecken i en sträng.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToSkip": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToSkip": {
            "type": "int",
            "defaultValue": 4
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayOutput | Matris | [”tre”] |
| stringOutput | Sträng | två tre |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

<a id="take" />

## <a name="take"></a>ta
`take(originalValue, numberToTake)`

Returnerar en matris med det angivna antalet element från början av matrisen eller en sträng med det angivna antalet tecken från början av strängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| Ursprungligt värde |Ja |matris eller sträng |Matris eller sträng för att ta elementen från. |
| numberToTake |Ja |int |Antalet element eller tecken som ska ta. Om det här värdet är 0 eller mindre, returneras en tom matris eller sträng. Om den är större än längden på den angivna matrisen eller sträng returneras alla element i en matris eller en sträng. |

### <a name="return-value"></a>Returvärde

En matris eller sträng.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) tar det angivna antalet element från matrisen och tecken från en sträng.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToTake": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToTake": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayOutput | Matris | [””, ”två”] |
| stringOutput | Sträng | på |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

<a id="union" />

## <a name="union"></a>Union
`union(arg1, arg2, arg3, ...)`

Returnerar en enda matris eller ett objekt med alla element från parametrarna. Duplicerade värden eller nycklar är bara ingår en gång.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris eller ett objekt |Det första värdet ska användas för anslutning element. |
| arg2 |Ja |matris eller ett objekt |Det andra värdet som ska användas för anslutning element. |
| ytterligare argument |Nej |matris eller ett objekt |Ytterligare värden som ska användas för att ansluta till element. |

### <a name="return-value"></a>Returvärde

En matris eller ett objekt.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) visar hur du använder union med matriser och -objekt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c1"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"three": "c2", "four": "d", "five": "e"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["three", "four"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[union(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[union(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

Utdata från det föregående exemplet med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| objectOutput | Objekt | {”1”: ”a”, ”två”: ”b”, ”tre”: ”c2”, ”fyra”: ”d”, ”fem”: ”e”} |
| arrayOutput | Matris | [”1”, ”två”, ”tre”, ”fyra”] |

För att distribuera det här exemplet mallen med Azure CLI, använder du:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

Om du vill distribuera den här exempel mallen med PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

## <a name="next-steps"></a>Nästa steg
* En beskrivning av avsnitt i en Azure Resource Manager-mallen finns [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Om du vill slå samman flera mallar, se [använda länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).
* Iterera ett angivet antal gånger när du skapar en typ av resurs finns [skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md).
* Information om hur du distribuerar mallen som du har skapat finns [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md).

