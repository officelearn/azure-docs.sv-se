---
title: Mall-funktioner – jämförelse
description: Beskriver de funktioner som används i en Azure Resource Manager-mall för att jämföra värden.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 15afc4d721c6577de9fe3e78483fdbfae5b493c6
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203785"
---
# <a name="comparison-functions-for-arm-templates"></a>Jämförelse funktioner för ARM-mallar

Resource Manager innehåller flera funktioner för att göra jämförelser i dina Azure Resource Manager-mallar (ARM).

* [coalesce](#coalesce)
* [lika med](#equals)
* [större än](#greater)
* [större än eller lika med](#greaterorequals)
* [mindre än](#less)
* [mindre än eller lika med](#lessorequals)

## <a name="coalesce"></a>coalesce

`coalesce(arg1, arg2, arg3, ...)`

Returnerar det första värdet som inte är null från parametrarna. Tomma strängar, tomma matriser och tomma objekt är inte null.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |heltal, sträng, matris eller objekt |Det första värdet som ska testas för null. |
| ytterligare argument |Nej |heltal, sträng, matris eller objekt |Ytterligare värden att testa för null. |

### <a name="return-value"></a>Returvärde

Värdet för de första icke-null-parametrarna, som kan vara en sträng, en heltals mat ris eller ett objekt. Null om alla parametrar är null.

### <a name="example"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) visas utdata från olika användnings områden för sammanslagning.

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

| Name | Typ | Värde |
| ---- | ---- | ----- |
| stringOutput | Sträng | standard |
| intOutput | Int | 1 |
| objectOutput | Objekt | {"First": "standard"} |
| arrayOutput | Matris | 81.1 |
| emptyOutput | Bool | Sant |

## <a name="equals"></a>lika med

`equals(arg1, arg2)`

Kontrollerar om två värden är lika med varandra.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |heltal, sträng, matris eller objekt |Det första värdet för att kontrol lera om det är lika. |
| arg2 |Ja |heltal, sträng, matris eller objekt |Det andra värdet för att kontrol lera om det är lika. |

### <a name="return-value"></a>Returvärde

Returnerar **Sant** om värdena är lika. annars **false**.

### <a name="remarks"></a>Anmärkningar

Funktionen Equals används ofta tillsammans med `condition` elementet för att testa om en resurs har distribuerats.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

### <a name="example"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) kontrol leras olika typer av värden för likhet. Alla standardvärden returnerar true.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 1
        },
        "firstString": {
            "type": "string",
            "defaultValue": "a"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "firstObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[equals(parameters('firstString'), parameters('secondString'))]"
        },
        "checkArrays": {
            "type": "bool",
            "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
        },
        "checkObjects": {
            "type": "bool",
            "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Name | Typ | Värde |
| ---- | ---- | ----- |
| Incheckningar | Bool | Sant |
| checkStrings | Bool | Sant |
| checkArrays | Bool | Sant |
| checkObjects | Bool | Sant |

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) använder [inte](template-functions-logical.md#not) **lika**med.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
}
```

Utdata från föregående exempel är:

| Name | Typ | Värde |
| ---- | ---- | ----- |
| checkNotEquals | Bool | Sant |

## <a name="greater"></a>större än

`greater(arg1, arg2)`

Kontrollerar om det första värdet är större än det andra värdet.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int eller string |Det första värdet för större jämförelse. |
| arg2 |Ja |int eller string |Det andra värdet för större jämförelse. |

### <a name="return-value"></a>Returvärde

Returnerar **True** om det första värdet är större än det andra värdet. annars **false**.

### <a name="example"></a>Exempel

Följande [exempel mal len](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) kontrollerar om det ena värdet är större än det andra.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greater(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Name | Typ | Värde |
| ---- | ---- | ----- |
| Incheckningar | Bool | Falskt |
| checkStrings | Bool | Sant |

## <a name="greaterorequals"></a>större än eller lika med

`greaterOrEquals(arg1, arg2)`

Kontrollerar om det första värdet är större än eller lika med det andra värdet.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int eller string |Det första värdet för större eller lika jämförelse. |
| arg2 |Ja |int eller string |Det andra värdet för större eller lika jämförelse. |

### <a name="return-value"></a>Returvärde

Returnerar **True** om det första värdet är större än eller lika med det andra värdet. annars **false**.

### <a name="example"></a>Exempel

Följande [exempel mal len](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) kontrollerar om det ena värdet är större än eller lika med det andra.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Name | Typ | Värde |
| ---- | ---- | ----- |
| Incheckningar | Bool | Falskt |
| checkStrings | Bool | Sant |

## <a name="less"></a>mindre än

`less(arg1, arg2)`

Kontrollerar om det första värdet är mindre än det andra värdet.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int eller string |Det första värdet för mindre jämförelse. |
| arg2 |Ja |int eller string |Det andra värdet för mindre jämförelse. |

### <a name="return-value"></a>Returvärde

Returnerar **True** om det första värdet är mindre än det andra värdet. annars **false**.

### <a name="example"></a>Exempel

Följande [exempel mal len](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) kontrollerar om det ena värdet är mindre än det andra.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[less(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Name | Typ | Värde |
| ---- | ---- | ----- |
| Incheckningar | Bool | Sant |
| checkStrings | Bool | Falskt |

## <a name="lessorequals"></a>mindre än eller lika med

`lessOrEquals(arg1, arg2)`

Kontrollerar om det första värdet är mindre än eller lika med det andra värdet.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int eller string |Det första värdet för mindre eller lika med jämförelse. |
| arg2 |Ja |int eller string |Det andra värdet för jämförelsen som är mindre eller lika med. |

### <a name="return-value"></a>Returvärde

Returnerar **True** om det första värdet är mindre än eller lika med det andra värdet. annars **false**.

### <a name="example"></a>Exempel

Följande [exempel mal len](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) kontrollerar om det ena värdet är mindre än eller lika med det andra.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Name | Typ | Värde |
| ---- | ---- | ----- |
| Incheckningar | Bool | Sant |
| checkStrings | Bool | Falskt |

## <a name="next-steps"></a>Nästa steg

* En beskrivning av avsnitten i en Azure Resource Manager mall finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
