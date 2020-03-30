---
title: Mallfunktioner - matriser och objekt
description: Beskriver de funktioner som ska användas i en Azure Resource Manager-mall för att arbeta med matriser och objekt.
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 0b4bb80f6d7a7cc20a8b2dcc71e890f2ada7c5be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156383"
---
# <a name="array-and-object-functions-for-arm-templates"></a>Matris- och objektfunktioner för ARM-mallar

Resource Manager innehåller flera funktioner för att arbeta med matriser och objekt i din AZURE Resource Manager -mall (ARM).

* [Array](#array)
* [smälter samman](#coalesce)
* [Concat](#concat)
* [Innehåller](#contains)
* [skapaArray](#createarray)
* [Tom](#empty)
* [Första](#first)
* [Korsningen](#intersection)
* [Json](#json)
* [Senaste](#last)
* [Längd](#length)
* [Max](#max)
* [Min](#min)
* [Utbud](#range)
* [Hoppa över](#skip)
* [Ta](#take)
* [Unionen](#union)

Om du vill få en matris med strängvärden avgränsade efter ett värde läser du [dela](template-functions-string.md#split).

## <a name="array"></a>matris

`array(convertToArray)`

Konverterar värdet till en matris.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| konvertera tillArray |Ja |int, sträng, array eller objekt |Värdet som ska konverteras till en matris. |

### <a name="return-value"></a>Returvärde

En matris.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/array.json) visar hur du använder matrisfunktionen med olika typer.

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
            "defaultValue": "efgh"
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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| intOutput (intOutput) | Matris | [1] |
| stringOutput | Matris | ["efgh"] |
| objectOutput (objektOutput) | Matris | [{"a": "b", "c": "d"}] |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

## <a name="coalesce"></a>smälter samman

`coalesce(arg1, arg2, arg3, ...)`

Returnerar det första icke-null-värdet från parametrarna. Tomma strängar, tomma matriser och tomma objekt är inte null.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |int, sträng, array eller objekt |Det första värdet som ska testas för null. |
| ytterligare args |Inga |int, sträng, array eller objekt |Ytterligare värden att testa för null. |

### <a name="return-value"></a>Returvärde

Värdet för de första icke-null-parametrarna, som kan vara en sträng, int, array eller objekt. Null om alla parametrar är null.

### <a name="example"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) visas utdata från olika användningsområden för sammanslagningar.

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| stringOutput | String | standard |
| intOutput (intOutput) | Int | 1 |
| objectOutput (objektOutput) | Objekt | {"first": "default"} |
| arrayOutput | Matris | [1] |
| emptyOutput | Bool | True |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

## <a name="concat"></a>Concat

`concat(arg1, arg2, arg3, ...)`

Kombinerar flera matriser och returnerar den sammanfogade matrisen, eller kombinerar flera strängvärden och returnerar den sammanfogade strängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |matris eller sträng |Den första matrisen eller strängen för sammanfogning. |
| ytterligare argument |Inga |matris eller sträng |Ytterligare matriser eller strängar i sekventiell ordning för sammanfogning. |

Den här funktionen kan ta valfritt antal argument och kan acceptera antingen strängar eller matriser för parametrarna. Du kan dock inte ange både matriser och strängar för parametrar. Matriser sammanfogas bara med andra matriser.

### <a name="return-value"></a>Returvärde

En sträng eller matris med sammanfogade värden.

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| återgå | Matris | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| concatOutput (concatOutput) | String | prefix-5yj4yjf5mbg72 |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

## <a name="contains"></a>innehåller

`contains(container, itemToFind)`

Kontrollerar om en matris innehåller ett värde, ett objekt innehåller en nyckel eller en sträng innehåller en delsträng. Strängjämförelsen är skiftlägeskänslig. Men när du testar om ett objekt innehåller en nyckel är jämförelsen skiftlägesokänslig.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| container |Ja |matris, objekt eller sträng |Värdet som innehåller värdet att hitta. |
| itemToFind |Ja |sträng eller int |Värdet att hitta. |

### <a name="return-value"></a>Returvärde

**Sant** om objektet hittas. annars **falskt**.

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| strängTrue | Bool | True |
| stringFalse | Bool | False |
| objektTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

## <a name="createarray"></a>createarray (skaparray)

`createArray (arg1, arg2, arg3, ...)`

Skapar en matris från parametrarna.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |Sträng, heltal, matris eller objekt |Det första värdet i matrisen. |
| ytterligare argument |Inga |Sträng, heltal, matris eller objekt |Ytterligare värden i matrisen. |

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| strängArray | Matris | ["a", "b", "c"] |
| intArray (på andra plats) | Matris | [1, 2, 3] |
| objectArray (objektArray) | Matris | [{"en": "a", "två": "b", "tre": "c"}] |
| arrayArray (olikartade) | Matris | [["en", "två", "tre"]] |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

## <a name="empty"></a>tomt

`empty(itemToTest)`

Avgör om en matris, ett objekt eller en sträng är tom.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |matris, objekt eller sträng |Värdet för att kontrollera om det är tomt. |

### <a name="return-value"></a>Returvärde

Returnerar **Sant** om värdet är tomt. annars **falskt**.

### <a name="example"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) kontrolleras om en matris, ett objekt och en sträng är tom.

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objektEmpty | Bool | True |
| strängEmpty | Bool | True |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

## <a name="first"></a>Första

`first(arg1)`

Returnerar det första elementet i matrisen eller det första tecknet i strängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |matris eller sträng |Värdet för att hämta det första elementet eller tecknet. |

### <a name="return-value"></a>Returvärde

Typen (sträng, int, array eller objekt) för det första elementet i en matris eller det första tecknet i en sträng.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) visar hur du använder den första funktionen med en matris och sträng.

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayOutput | String | en |
| stringOutput | String | O |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

## <a name="intersection"></a>Korsningen

`intersection(arg1, arg2, arg3, ...)`

Returnerar en enda matris eller ett objekt med de gemensamma elementen från parametrarna.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |matris eller objekt |Det första värdet som ska användas för att hitta vanliga element. |
| arg2 (arg2) |Ja |matris eller objekt |Det andra värdet som ska användas för att hitta vanliga element. |
| ytterligare argument |Inga |matris eller objekt |Ytterligare värden som ska användas för att hitta vanliga element. |

### <a name="return-value"></a>Returvärde

En matris eller ett objekt med de gemensamma elementen.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) visar hur du använder skärningspunkten med matriser och objekt:

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| objectOutput (objektOutput) | Objekt | {"en": "a", "tre": "c"} |
| arrayOutput | Matris | ["två", "tre"] |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

## <a name="json"></a>json

`json(arg1)`

Returnerar ett JSON-objekt.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |sträng |Värdet som ska konverteras till JSON. |

### <a name="return-value"></a>Returvärde

JSON-objektet från den angivna strängen eller ett tomt objekt när **null** anges.

### <a name="remarks"></a>Anmärkningar

Om du behöver inkludera ett parametervärde eller en variabel i JSON-objektet använder du [funktionen sammanfoga för](template-functions-string.md#concat) att skapa strängen som du skickar till funktionen.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) visar hur du använder json-funktionen med matriser och objekt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testValue": {
            "type": "string",
            "defaultValue": "demo value"
        }
    },
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
        },
        "paramOutput": {
            "type": "object",
            "value": "[json(concat('{\"a\": \"', parameters('testValue'), '\"}'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| jsonOutput (jsonOutput) | Objekt | {"a": "b"} |
| nullOutput (nullOutput) | Boolean | True |
| paramOutput (paramOutput) | Objekt | {"a": "demovärde"}

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

## <a name="last"></a>Senaste

`last (arg1)`

Returnerar det sista elementet i matrisen eller det sista tecknet i strängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |matris eller sträng |Värdet för att hämta det sista elementet eller tecknet. |

### <a name="return-value"></a>Returvärde

Typen (sträng, int, array eller objekt) för det sista elementet i en matris eller det sista tecknet i en sträng.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) visar hur du använder den sista funktionen med en matris och sträng.

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayOutput | String | tre |
| stringOutput | String | e |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

## <a name="length"></a>length

`length(arg1)`

Returnerar antalet element i en matris, tecken i en sträng eller egenskaper på rotnivå i ett objekt.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |matris, sträng eller objekt |Matrisen som ska användas för att hämta antalet element, strängen som ska användas för att hämta antalet tecken eller det objekt som ska användas för att få antalet rotnivåegenskaper. |

### <a name="return-value"></a>Returvärde

En int.

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
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "propA": "one",
                "propB": "two",
                "propC": "three",
                "propD": {
                    "propD-1": "sub",
                    "propD-2": "sub"
                }
            }
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
        },
        "objectLength": {
            "type": "int",
            "value": "[length(parameters('objectToTest'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayLängd | Int | 3 |
| strängLängd | Int | 13 |
| objektLängd | Int | 4 |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Du kan använda den här funktionen med en matris för att ange antalet iterationer när du skapar resurser. I följande exempel **refererar parametern siteNames** till en matris med namn som ska användas när webbplatserna skapas.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

Mer information om hur du använder den här funktionen med en matris finns [i Skapa flera instanser av resurser i Azure Resource Manager](copy-resources.md).

## <a name="max"></a>Max

`max(arg1)`

Returnerar det maximala värdet från en matris med heltal eller en kommaavgränsad lista med heltal.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |matris med heltal eller kommaavgränsad lista över heltal |Samlingen för att få det maximala värdet. |

### <a name="return-value"></a>Returvärde

En int som representerar det maximala värdet.

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

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

## <a name="min"></a>min

`min(arg1)`

Returnerar minimivärdet från en matris med heltal eller en kommaavgränsad lista med heltal.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |matris med heltal eller kommaavgränsad lista över heltal |Samlingen för att få det lägsta värdet. |

### <a name="return-value"></a>Returvärde

En int som representerar minimivärdet.

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

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

## <a name="range"></a>Utbud

`range(startIndex, count)`

Skapar en matris med heltal från ett start heltal och innehåller ett antal objekt.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| startIndex (startIndex) |Ja |int |Det första heltalet i matrisen. Summan av startIndex och antal får inte vara större än 2147483647. |
| count |Ja |int |Antalet heltal i matrisen. Måste vara icke-negativt heltal upp till 10000. |

### <a name="return-value"></a>Returvärde

En matris med heltal.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/range.json) visar hur du använder områdesfunktionen:

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| rangeOutput (intervallUträkning) | Matris | [5, 6, 7] |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

## <a name="skip"></a>hoppa över

`skip(originalValue, numberToSkip)`

Returnerar en matris med alla element efter det angivna talet i matrisen, eller returnerar en sträng med alla tecken efter det angivna talet i strängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| originalVärde |Ja |matris eller sträng |Den matris eller sträng som ska användas för att hoppa över. |
| numberToSkip |Ja |int |Antalet element eller tecken som ska hoppas över. Om det här värdet är 0 eller mindre returneras alla element eller tecken i värdet. Om den är större än längden på matrisen eller strängen returneras en tom matris eller sträng. |

### <a name="return-value"></a>Returvärde

En matris eller sträng.

### <a name="example"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) hoppar du över det angivna antalet element i matrisen och det angivna antalet tecken i en sträng.

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayOutput | Matris | ["tre"] |
| stringOutput | String | två tre |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

## <a name="take"></a>Ta

`take(originalValue, numberToTake)`

Returnerar en matris med det angivna antalet element från början av matrisen, eller en sträng med det angivna antalet tecken från början av strängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| originalVärde |Ja |matris eller sträng |Matrisen eller strängen som elementen ska ta från. |
| numberToTake |Ja |int |Antalet element eller tecken som ska vidtas. Om det här värdet är 0 eller mindre returneras en tom matris eller sträng. Om den är större än längden på den angivna matrisen eller strängen returneras alla element i matrisen eller strängen. |

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayOutput | Matris | ["en", "två"] |
| stringOutput | String | på |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

## <a name="union"></a>Unionen

`union(arg1, arg2, arg3, ...)`

Returnerar en enda matris eller ett objekt med alla element från parametrarna. Dubblettvärden eller nycklar inkluderas bara en gång.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |matris eller objekt |Det första värdet som ska användas för att sammanfoga element. |
| arg2 (arg2) |Ja |matris eller objekt |Det andra värdet som ska användas för att sammanfoga element. |
| ytterligare argument |Inga |matris eller objekt |Ytterligare värden som ska användas för att sammanfoga element. |

### <a name="return-value"></a>Returvärde

En matris eller ett objekt.

### <a name="example"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) visar hur du använder union med matriser och objekt:

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| objectOutput (objektOutput) | Objekt | {"en": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Matris | ["en", "två", "tre", "fyra"] |

Om du vill distribuera den här exempelmallen med Azure CLI använder du:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

Om du vill distribuera den här exempelmallen med PowerShell använder du:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

## <a name="next-steps"></a>Nästa steg

* En beskrivning av avsnitten i en Azure Resource Manager-mall finns i [Redigera Azure Resource Manager-mallar](template-syntax.md).
* Information om hur du sammanfogar flera mallar finns [i Använda länkade mallar med Azure Resource Manager](linked-templates.md).
* Information om hur du itererar ett angivet antal gånger när du skapar en typ av resurs finns [i Skapa flera instanser av resurser i Azure Resource Manager](copy-resources.md).
* Information om hur du distribuerar mallen som du har skapat finns i [Distribuera ett program med Azure Resource Manager-mallen](deploy-powershell.md).

