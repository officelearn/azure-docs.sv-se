---
title: Mall funktioner – objekt
description: Beskriver de funktioner som används i en Azure Resource Manager mall för att arbeta med objekt.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: fede4d6c71e45b119e500d4c9c6f91765d052036
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84676802"
---
# <a name="object-functions-for-arm-templates"></a>Objekt funktioner för ARM-mallar

Resource Manager innehåller flera funktioner för att arbeta med objekt i din Azure Resource Manager-mall (ARM).

* [ingår](#contains)
* [tomt](#empty)
* [överlappning](#intersection)
* [utgör](#json)
* [krävande](#length)
* [Union](#union)

## <a name="contains"></a>innehåller

`contains(container, itemToFind)`

Kontrollerar om en matris innehåller ett värde, ett objekt innehåller en nyckel eller en sträng innehåller en under sträng. Sträng jämförelsen är Skift läges känslig. Men när du testar om ett objekt innehåller en nyckel är jämförelsen Skift läges okänslig.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| container |Ja |matris, objekt eller sträng |Värdet som innehåller värdet som ska hittas. |
| itemToFind |Ja |sträng eller heltal |Det värde som ska hittas. |

### <a name="return-value"></a>Returvärde

**Sant** om objektet hittas; annars **false**.

### <a name="example"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) visas hur du använder med olika typer:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Name | Typ | Värde |
| ---- | ---- | ----- |
| stringTrue | Bool | Sant |
| stringFalse | Bool | Falskt |
| objectTrue | Bool | Sant |
| objectFalse | Bool | Falskt |
| arrayTrue | Bool | Sant |
| arrayFalse | Bool | Falskt |

## <a name="empty"></a>tomt

`empty(itemToTest)`

Anger om en matris, ett objekt eller en sträng är tom.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |matris, objekt eller sträng |Värdet för att kontrol lera om det är tomt. |

### <a name="return-value"></a>Returvärde

Returnerar **Sant** om värdet är tomt. annars **false**.

### <a name="example"></a>Exempel

Följande [exempel mal len](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) kontrollerar om en matris, ett objekt och en sträng är tomma.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Name | Typ | Värde |
| ---- | ---- | ----- |
| arrayEmpty | Bool | Sant |
| objectEmpty | Bool | Sant |
| stringEmpty | Bool | Sant |

## <a name="intersection"></a>överlappning

`intersection(arg1, arg2, arg3, ...)`

Returnerar en enskild matris eller ett objekt med de gemensamma elementen från parametrarna.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris eller objekt |Det första värdet som ska användas för att hitta vanliga element. |
| arg2 |Ja |matris eller objekt |Det andra värdet som ska användas för att hitta vanliga element. |
| ytterligare argument |No |matris eller objekt |Ytterligare värden som ska användas för att hitta vanliga element. |

### <a name="return-value"></a>Returvärde

En matris eller ett objekt med gemensamma element.

### <a name="example"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) visas hur du använder snitt med matriser och objekt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Name | Typ | Värde |
| ---- | ---- | ----- |
| objectOutput | Objekt | {"One": "a", "tre": "c"} |
| arrayOutput | Matris | ["två", "tre"] |

## <a name="json"></a>json

`json(arg1)`

Returnerar ett JSON-objekt.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |sträng |Värdet som ska konverteras till JSON. |

### <a name="return-value"></a>Returvärde

JSON-objektet från den angivna strängen, eller ett tomt objekt när **Null** har angetts.

### <a name="remarks"></a>Kommentarer

Om du behöver inkludera ett parameter värde eller en variabel i JSON-objektet använder du funktionen [concat](template-functions-string.md#concat) för att skapa den sträng som du skickar till funktionen.

### <a name="example"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) visas hur du använder JSON-funktionen. Observera att du kan antingen skicka i en sträng som representerar objektet eller använda **Null** när inget värde krävs.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "jsonObject1": {
            "type": "string",
            "defaultValue": "null"
        },
        "jsonObject2": {
            "type": "string",
            "defaultValue": "{\"a\": \"b\"}"
        },
        "testValue": {
            "type": "string",
            "defaultValue": "demo value"
        }
    },
    "resources": [
    ],
    "outputs": {
        "jsonOutput1": {
            "type": "bool",
            "value": "[empty(json(parameters('jsonObject1')))]"
        },
        "jsonOutput2": {
            "type": "object",
            "value": "[json(parameters('jsonObject2'))]"
        },
        "paramOutput": {
            "type": "object",
            "value": "[json(concat('{\"a\": \"', parameters('testValue'), '\"}'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Name | Typ | Värde |
| ---- | ---- | ----- |
| jsonOutput1 | Boolesk | Sant |
| jsonOutput2 | Objekt | {"a": "b"} |
| paramOutput | Objekt | {"a": "demo värde"}

## <a name="length"></a>length

`length(arg1)`

Returnerar antalet element i en matris, tecken i en sträng eller på rot nivå egenskaper i ett objekt.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris, sträng eller objekt |Den matris som ska användas för att hämta antalet element, strängen som ska användas för att hämta antalet tecken, eller objektet som ska användas för att hämta antalet rot nivå egenskaper. |

### <a name="return-value"></a>Returvärde

En int.

### <a name="example"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) visas hur du använder length med en matris och en sträng:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Name | Typ | Värde |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="union"></a>Union

`union(arg1, arg2, arg3, ...)`

Returnerar en enskild matris eller ett objekt med alla element från parametrarna. Duplicerade värden eller nycklar ingår bara en gång.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris eller objekt |Det första värdet som ska användas för att koppla ihop element. |
| arg2 |Ja |matris eller objekt |Det andra värdet som ska användas för att koppla ihop element. |
| ytterligare argument |No |matris eller objekt |Ytterligare värden som ska användas för att koppla ihop element. |

### <a name="return-value"></a>Returvärde

En matris eller ett objekt.

### <a name="example"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) visas hur du använder union med matriser och objekt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Name | Typ | Värde |
| ---- | ---- | ----- |
| objectOutput | Objekt | {"One": "a", "två": "b", "tre": "C2", "fyra": "d", "fem": "e"} |
| arrayOutput | Matris | ["One", "två", "tre", "fyra"] |

## <a name="next-steps"></a>Nästa steg

* En beskrivning av avsnitten i en Azure Resource Manager mall finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
