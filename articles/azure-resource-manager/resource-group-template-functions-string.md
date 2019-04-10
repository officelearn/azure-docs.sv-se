---
title: Azure Resource Manager-Mallfunktioner - sträng | Microsoft Docs
description: Beskriver funktionerna du använder i en Azure Resource Manager-mall för att arbeta med strängar.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2019
ms.author: tomfitz
ms.openlocfilehash: bf9faa34c1f0923761ce583c22ba4084d7bd42a8
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278793"
---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Strängfunktioner för Azure Resource Manager-mallar

Resource Manager tillhandahåller följande funktioner för att arbeta med strängar:

* [Base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [innehåller](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [tom](#empty)
* [endsWith](#endswith)
* [första](#first)
* [Format](#format)
* [GUID](#guid)
* [indexOf](#indexof)
* [senaste](#last)
* [lastIndexOf](#lastindexof)
* [Längd](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [Ersätt](#replace)
* [hoppa över](#skip)
* [split](#split)
* [startsWith](#startswith)
* [sträng](#string)
* [delsträngen](#substring)
* [ta](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [trim](#trim)
* [uniqueString](#uniquestring)
* [URI](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)
* [utcNow](#utcnow)

## <a name="base64"></a>Base64

`base64(inputString)`

Returnerar en base64-representation av den inmatade strängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| inputString |Ja |sträng |Värdet som returneras som en base64-representation. |

### <a name="return-value"></a>Returvärde

En sträng som innehåller base64-representation.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) visar hur du använder funktionen base64.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | Ett två tre |
| toJsonOutput | Objekt | {"one": "a", "two": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Konverterar en base64-representation till en JSON-objekt.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| base64Value |Ja |sträng |Base64-representation att konvertera till en JSON-objekt. |

### <a name="return-value"></a>Returvärde

Ett JSON-objekt.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) funktionen base64ToJson för att konvertera ett base64-värde:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | Ett två tre |
| toJsonOutput | Objekt | {"one": "a", "two": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Konverterar en base64-representation till en sträng.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| base64Value |Ja |sträng |Base64-representation att konvertera till en sträng. |

### <a name="return-value"></a>Returvärde

En sträng med konverterade base64-värde.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) funktionen base64ToString för att konvertera ett base64-värde:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | Ett två tre |
| toJsonOutput | Objekt | {"one": "a", "two": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Kombinerar flera strängvärden och returnerar en sammanfogad sträng eller kombinerar flera matriser och returnerar sammanlänkad matris.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |sträng eller matris |Det första värdet för sammanfogning. |
| ytterligare argument |Nej |sträng |Ytterligare värden i sekventiell ordning för sammanfogning. |

### <a name="return-value"></a>Returvärde
En sträng eller en matris med sammansatta värden.

### <a name="examples"></a>Exempel

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
| concatOutput | String | prefix-5yj4yjf5mbg72 |

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
| Gå tillbaka | Matris | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>innehåller

`contains (container, itemToFind)`

Kontrollerar om en matris innehåller ett värde, ett objekt som innehåller en nyckel eller en sträng innehåller en delsträng. Strängjämförelse är skiftlägeskänsligt. När du testar om ett objekt innehåller en nyckel är dock jämförelsen inte skiftlägeskänslig.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| container |Ja |matris, objekt eller sträng |Det värde som innehåller värdet som ska hitta. |
| itemToFind |Ja |sträng eller int |Värde att söka efter. |

### <a name="return-value"></a>Returvärde

**SANT** om objektet är hittades, i annat fall **FALSKT**.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) visar hur du använder innehåller olika typer:

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
| stringTrue | Booleskt | True |
| stringFalse | Booleskt | False |
| objectTrue | Booleskt | True |
| objectFalse | Booleskt | False |
| arrayTrue | Booleskt | True |
| arrayFalse | Booleskt | False |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Konverterar ett värde till ett data-URI.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToConvert |Ja |sträng |Värdet att konvertera till ett data-URI. |

### <a name="return-value"></a>Returvärde

En sträng formaterad som en data-URI.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) konverterar ett värde till ett data-URI och konverterar ett data-URI till en sträng:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hej världen! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Konverterar ett data-URI formaterad värdet till en sträng.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Ja |sträng |URI-värdet att konvertera data. |

### <a name="return-value"></a>Returvärde

En sträng som innehåller det konvertera värdet.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) konverterar ett värde till ett data-URI och konverterar ett data-URI till en sträng:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hej världen! |

## <a name="empty"></a>tom

`empty(itemToTest)`

Anger om en matris, ett objekt eller en sträng är tom.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |matris, objekt eller sträng |Värde att kontrollera om den är tom. |

### <a name="return-value"></a>Returvärde

Returnerar **SANT** om värdet är tomt, i annat fall **FALSKT**.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) kontrollerar om en matris och objekt strängen är tom.

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
| arrayEmpty | Booleskt | True |
| objectEmpty | Booleskt | True |
| stringEmpty | Booleskt | True |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Anger om en sträng som slutar med ett värde. Jämförelse är skiftlägeskänsliga.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |sträng |Det värde som innehåller objektet du vill hitta. |
| stringToFind |Ja |sträng |Värde att söka efter. |

### <a name="return-value"></a>Returvärde

**SANT** om det sista tecknet eller tecknen i strängen matchar värdet; annars **FALSKT**.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) visar hur du använder funktionerna startsWith och endsWith:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| startsTrue | Booleskt | True |
| startsCapTrue | Booleskt | True |
| startsFalse | Booleskt | False |
| endsTrue | Booleskt | True |
| endsCapTrue | Booleskt | True |
| endsFalse | Booleskt | False |

## <a name="first"></a>första

`first(arg1)`

Returnerar det första tecknet i strängen eller första element i matrisen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris eller sträng |Värde att hämta den första element eller tecknet. |

### <a name="return-value"></a>Returvärde

En sträng med det första tecknet eller typ (sträng, int, matrisen eller objekt) för det första elementet i en matris.

### <a name="examples"></a>Exempel

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

## <a name="format"></a>Format

`format(formatString, arg1, arg2, ...)`

Skapar en formaterad sträng från indatavärden.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| formatString | Ja | sträng | Den sammansatta Formatsträngen. |
| arg1 | Ja | sträng, heltal eller booleskt värde | Värdet som ska ingå i den formaterade strängen. |
| ytterligare argument | Nej | sträng, heltal eller booleskt värde | Ytterligare värden som ska ingå i den formaterade strängen. |

### <a name="remarks"></a>Kommentarer

Använd den här funktionen för att formatera en sträng i mallen. Den använder samma formateringsalternativ som den [System.String.Format](/dotnet/api/system.string.format) -metod i .NET.

### <a name="examples"></a>Exempel

Följande exempelmall visar hur du använder funktionen format.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "greeting": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "name": {
            "type": "string",
            "defaultValue": "User"
        },
        "numberToFormat": {
            "type": "int",
            "defaultValue": 8175133
        }
    },
    "resources": [
    ],
    "outputs": {
        "formatTest": {
            "type": "string",
            "value": "[format('{0}, {1}. Formatted number: {2:N0}', parameters('greeting'), parameters('name'), parameters('numberToFormat'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| formatTest | String | Hello, användare. Formaterat nummer: 8,175,133 |

## <a name="guid"></a>GUID

`guid(baseString, ...)`

Skapar ett värde i formatet av en globalt unik identifierare som baserat på de värden som anges som parametrar.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| baseString |Ja |sträng |Det värde som används i hash-funktionen för att skapa GUID. |
| ytterligare parametrar som behövs |Nej |sträng |Du kan lägga till så många strängar som behövs för att skapa det värde som anger vilken nivå av unikhet. |

### <a name="remarks"></a>Kommentarer

Den här funktionen är användbar när du behöver skapa ett värde i formatet av en globalt unik identifierare. Du kan ange parametervärden som begränsar du omfattningen för unikhet för resultatet. Du kan ange om namnet är unikt prenumeration, resursgrupp eller distribution.

Det returnerade värdet är inte en slumpmässig sträng, men i stället resultatet av en hash-funktionen på parametrarna. Det returnerade värdet är 36 tecken långt. Det är inte unikt globalt. Använd för att skapa ett nytt GUID som inte är baserad på det hash-värdet för parametrarna i [newGuid](#newguid) funktion.

I följande exempel visas hur du använder guid för att skapa ett unikt värde för vanliga nivåer.

Unik som hör till prenumerationen

```json
"[guid(subscription().subscriptionId)]"
```

Unik begränsade till resursgrupp

```json
"[guid(resourceGroup().id)]"
```

Unik begränsade till distributionen för en resursgrupp

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Returvärde

En sträng med 36 tecken i form av en globalt unik identifierare.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) returnerar resultat från guid:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "guidPerSubscription": {
            "value": "[guid(subscription().subscriptionId)]",
            "type": "string"
        },
        "guidPerResourceGroup": {
            "value": "[guid(resourceGroup().id)]",
            "type": "string"
        },
        "guidPerDeployment": {
            "value": "[guid(resourceGroup().id, deployment().name)]",
            "type": "string"
        }
    }
}
```

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

Returnerar den första positionen för ett värde i en sträng. Jämförelse är skiftlägeskänsliga.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |sträng |Det värde som innehåller objektet du vill hitta. |
| stringToFind |Ja |sträng |Värde att söka efter. |

### <a name="return-value"></a>Returvärde

Ett heltal som representerar positionen för objektet du vill hitta. Värdet är nollbaserat. Om objektet inte hittas returneras -1.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) visar hur du använder funktionerna indexOf och lastIndexOf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="last"></a>senaste

`last (arg1)`

Returnerar det sista tecknet i strängen eller det sista elementet i matrisen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris eller sträng |Värde att hämta det sista elementet eller tecknet. |

### <a name="return-value"></a>Returvärde

En sträng med det sista tecknet eller typ (sträng, int, matrisen eller objekt) för det sista elementet i en matris.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) visar hur du använder den senaste funktionen med en matris och sträng.

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

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Returnerar den sista positionen för ett värde i en sträng. Jämförelse är skiftlägeskänsliga.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |sträng |Det värde som innehåller objektet du vill hitta. |
| stringToFind |Ja |sträng |Värde att söka efter. |

### <a name="return-value"></a>Returvärde

Ett heltal som representerar den sista positionen för att hitta objektet. Värdet är nollbaserat. Om objektet inte hittas returneras -1.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) visar hur du använder funktionerna indexOf och lastIndexOf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="length"></a>Längd

`length(string)`

Returnerar antalet tecken i en sträng eller ett element i en matris.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris eller sträng |Matris för att använda för att hämta antalet element eller strängen som du använder för att hämta antalet tecken. |

### <a name="return-value"></a>Returvärde

Int. 

### <a name="examples"></a>Exempel

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |

## <a name="newguid"></a>newGuid

`newGuid()`

Returnerar ett värde i formatet av en globalt unik identifierare. **Den här funktionen kan endast användas i standardvärdet för en parameter.**

### <a name="remarks"></a>Kommentarer

Du kan bara använda den här funktionen i ett uttryck för standardvärdet för en parameter. Den här funktionen någon annanstans i en mall för returneras ett fel. Funktionen är inte tillåten i andra delar av mallen eftersom ett annat värde returneras varje gång den anropas. Distribuera mallen med samma parametrar ger inte på ett tillförlitligt sätt samma resultat.

Funktionen newGuid skiljer sig från den [guid](#guid) funktion eftersom det tar inga parametrar. När du anropar guid med samma parameter, returnerar samma identifierare för varje gång. Använd guid när du behöver att generera samma GUID för en viss miljö på ett tillförlitligt sätt. Använd newGuid när du behöver ett annat ID varje gång, till exempel att distribuera resurser till en testmiljö.

Om du använder den [alternativet om du vill distribuera om en tidigare lyckad distribution](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails), och den tidigare distributionen innehåller en parameter som använder newGuid, parametern inte omvärderas. Värdet för parametern från tidigare distributionen återanvänds i stället automatiskt i distributionen för återställning.

I en testmiljö kan du behöva upprepade gånger distribuera resurser som bara live för en kort tid. I stället för att konstruera unika namn, kan du använda newGuid med [uniqueString](#uniquestring) att skapa unika namn.

Var noga med att omdistribuera en mall som är beroende av funktionen newGuid för ett standardvärde. När du distribuerar om och inte ange ett värde för parametern omvärderas funktionen. Om du vill uppdatera en befintlig resurs i stället för att skapa en ny, skicka parametervärdet från tidigare distributionen.

### <a name="return-value"></a>Returvärde

En sträng med 36 tecken i form av en globalt unik identifierare.

### <a name="examples"></a>Exempel

Följande exempelmall visar en parameter med en ny identifierare.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "guidOutput": {
            "type": "string",
            "value": "[parameters('guidValue')]"
        }
    }
}
```

Utdata från föregående exempel varierar för varje distribution, men ska vara detsamma som:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| guidOutput | sträng | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

I följande exempel används funktionen newGuid för att skapa ett unikt namn för ett lagringskonto. Den här mallen kan användas i testmiljö där lagringskontot finns en kort tid och är inte omdistribueras.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "variables": {
        "storageName": "[concat('storage', uniqueString(parameters('guidValue')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "West US",
            "apiVersion": "2018-07-01",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {}
        }
    ],
    "outputs": {
        "nameOutput": {
            "type": "string",
            "value": "[variables('storageName')]"
        }
    }
}
```

Utdata från föregående exempel varierar för varje distribution, men ska vara detsamma som:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| nameOutput | sträng | storagenziwvyru7uxie |


## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Returnerar en högerjusterad sträng genom att lägga till tecken till vänster tills du når den totala angivna längden.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| valueToPad |Ja |sträng eller int |Värdet som Högerjustera. |
| totalLength |Ja |int |Det totala antalet tecken i den returnerade strängen. |
| paddingCharacter |Nej |enskilt tecken |Tecknet som ska användas för vänster-utfyllnad tills den totala längden har uppnåtts. Standardvärdet är ett blanksteg. |

Om den ursprungliga strängen är längre än antalet tecken att fylla, läggs inte tecken.

### <a name="return-value"></a>Returvärde

En sträng med minst antal angivna tecken.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) visar hur du Fyll ut användaren parametervärdet genom att lägga till tecknet noll tills den når det totala antalet tecken. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123"
        }
    },
    "resources": [],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[padLeft(parameters('testString'),10,'0')]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| stringOutput | String | 0000000123 |

## <a name="replace"></a>Ersätt

`replace(originalString, oldString, newString)`

Returnerar en ny sträng med alla instanser av en sträng som har ersatts av en annan sträng.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| originalString |Ja |sträng |Det värde som har alla instanser av en sträng som har ersatts av en annan sträng. |
| oldString |Ja |sträng |Strängen som ska tas bort från den ursprungliga strängen. |
| newString |Ja |sträng |Strängen som du lägger till i stället för strängen som har tagits bort. |

### <a name="return-value"></a>Returvärde

En sträng med Ersatta tecken.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) visar hur du tar bort alla bindestreck från strängen från användare och hur du ersätter en del av strängen med en annan sträng.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123-123-1234"
        }
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'-', '')]"
        },
        "secodeOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| firstOutput | String | 1231231234 |
| secodeOutput | String | 123-123-xxxx |

## <a name="skip"></a>hoppa över

`skip(originalValue, numberToSkip)`

Returnerar en sträng där alla tecken efter angivet antal tecken eller en matris med alla element efter angivet antal element.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| originalValue |Ja |matris eller sträng |Den matris eller sträng som ska användas för att hoppa över. |
| numberToSkip |Ja |int |Antalet element eller tecken som ska hoppas över. Om det här värdet är 0 eller mindre, returneras alla element eller tecken i-värdet. Om den är större än längden på den matris eller sträng returneras en tom matris eller sträng. |

### <a name="return-value"></a>Returvärde

En matris eller sträng.

### <a name="examples"></a>Exempel

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

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| arrayOutput | Matris | ["three"] |
| stringOutput | String | två tre |

## <a name="split"></a>split

`split(inputString, delimiter)`

Returnerar en matris med strängar som innehåller delsträngar indatasträngens som avgränsas med de angivna avgränsarna.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| inputString |Ja |sträng |Strängen som ska delas. |
| avgränsare |Ja |sträng eller en matris med strängar |Avgränsaren som ska användas för uppdelningen av strängen. |

### <a name="return-value"></a>Returvärde

En matris med strängar.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) delar upp strängen med ett kommatecken och med kommatecken eller semikolon.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstString": {
            "type": "string",
            "defaultValue": "one,two,three"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "one;two,three"
        }
    },
    "variables": {
        "delimiters": [ ",", ";" ]
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "array",
            "value": "[split(parameters('firstString'),',')]"
        },
        "secondOutput": {
            "type": "array",
            "value": "[split(parameters('secondString'),variables('delimiters'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| firstOutput | Matris | [”en”, ”två”, ”tre”] |
| secondOutput | Matris | [”en”, ”två”, ”tre”] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Anger om en sträng som börjar med ett värde. Jämförelse är skiftlägeskänsliga.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |sträng |Det värde som innehåller objektet du vill hitta. |
| stringToFind |Ja |sträng |Värde att söka efter. |

### <a name="return-value"></a>Returvärde

**SANT** om det första tecknet eller tecknen i strängen matchar värdet; annars **FALSKT**.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) visar hur du använder funktionerna startsWith och endsWith:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| startsTrue | Booleskt | True |
| startsCapTrue | Booleskt | True |
| startsFalse | Booleskt | False |
| endsTrue | Booleskt | True |
| endsCapTrue | Booleskt | True |
| endsFalse | Booleskt | False |

## <a name="string"></a>sträng

`string(valueToConvert)`

Konverterar det angivna värdet till en sträng.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| valueToConvert |Ja | Alla |Värdet som konverteras till sträng. Någon typ av värde kan konverteras, inklusive objekt och matriser. |

### <a name="return-value"></a>Returvärde

En sträng med det konvertera värdet.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) visar hur du konverterar olika typer av värden till strängar:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testObject": {
            "type": "object",
            "defaultValue": {
                "valueA": 10,
                "valueB": "Example Text"
            }
        },
        "testArray": {
            "type": "array",
            "defaultValue": [
                "a",
                "b",
                "c"
            ]
        },
        "testInt": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "resources": [],
    "outputs": {
        "objectOutput": {
            "type": "string",
            "value": "[string(parameters('testObject'))]"
        },
        "arrayOutput": {
            "type": "string",
            "value": "[string(parameters('testArray'))]"
        },
        "intOutput": {
            "type": "string",
            "value": "[string(parameters('testInt'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| objectOutput | String | {”Värdea”: 10, ”Värdeb”: ”exempeltexten”} |
| arrayOutput | String | ["a","b","c"] |
| intOutput | String | 5 |

## <a name="substring"></a>delsträngen

`substring(stringToParse, startIndex, length)`

Returnerar en understräng som börjar vid angiven position och innehåller det angivna antalet tecken.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToParse |Ja |sträng |Den ursprungliga strängen från vilket delsträngen ska extraheras. |
| startIndex |Nej |int |Nollbaserade tecken startposition delsträngen. |
| Längd |Nej |int |Antalet tillåtna tecken för delsträngen. Måste referera till en plats i strängen. Måste vara noll eller större. |

### <a name="return-value"></a>Returvärde

Delsträngen. Eller en tom sträng om längden är noll.

### <a name="remarks"></a>Kommentarer

Det går inte att funktionen när delsträngen sträcker sig utanför slutet av strängen, eller när längden är mindre än noll. I följande exempel misslyckas med felmeddelandet ”parametrarna index och längd måste referera till en plats i strängen. Indexparametern: ”0”, Längdparametern: '11 ”, längden på strängparametern: '10'.".

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) extraherar en understräng från en parameter.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        }
    },
    "resources": [],
    "outputs": {
        "substringOutput": {
            "value": "[substring(parameters('testString'), 4, 3)]",
            "type": "string"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| substringOutput | String | två |

## <a name="take"></a>ta

`take(originalValue, numberToTake)`

Returnerar en sträng med det angivna antalet tecken från början av strängen eller en matris med det angivna antalet element från början av matrisen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| originalValue |Ja |matris eller sträng |Den matris eller sträng som ska ta elementen från. |
| numberToTake |Ja |int |Antalet element eller tecken som ska ta. Om det här värdet är 0 eller mindre, returneras en tom matris eller sträng. Om den är större än längden på den angivna matris eller sträng returneras alla element i den matris eller sträng. |

### <a name="return-value"></a>Returvärde

En matris eller sträng.

### <a name="examples"></a>Exempel

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
| arrayOutput | Matris | [””, ”två”] |
| stringOutput | String | på |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Konverterar den angivna strängen till gemener.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToChange |Ja |sträng |Värdet att konvertera till gemener. |

### <a name="return-value"></a>Returvärde

Strängen som konverteras till gemener.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) konverterar ett parametervärde gemener och versaler.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| toLowerOutput | String | Ett två tre |
| toUpperOutput | String | ETT TVÅ TRE |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Konverterar den angivna strängen till versaler.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToChange |Ja |sträng |Värdet att konvertera till versaler. |

### <a name="return-value"></a>Returvärde

Strängen som konverteras till versaler.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) konverterar ett parametervärde gemener och versaler.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| toLowerOutput | String | Ett två tre |
| toUpperOutput | String | ETT TVÅ TRE |

## <a name="trim"></a>trim

`trim (stringToTrim)`

Tar bort alla inledande och avslutande blanksteg från den angivna strängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToTrim |Ja |sträng |Värde att trimma. |

### <a name="return-value"></a>Returvärde

Strängen utan inledande och avslutande blanksteg.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) tar bort blanksteg från parametern.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "    one two three   "
        }
    },
    "resources": [],
    "outputs": {
        "return": {
            "type": "string",
            "value": "[trim(parameters('testString'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| Gå tillbaka | String | Ett två tre |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Skapar en deterministisk hash-sträng som baseras på de värden som anges som parametrar. 

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| baseString |Ja |sträng |Det värde som används i hash-funktionen för att skapa en unik sträng. |
| ytterligare parametrar som behövs |Nej |sträng |Du kan lägga till så många strängar som behövs för att skapa det värde som anger vilken nivå av unikhet. |

### <a name="remarks"></a>Kommentarer

Den här funktionen är användbar när du behöver skapa ett unikt namn för en resurs. Du kan ange parametervärden som begränsar du omfattningen för unikhet för resultatet. Du kan ange om namnet är unikt prenumeration, resursgrupp eller distribution. 

Det returnerade värdet är inte en slumpmässig sträng, men i stället resultatet av en hash-funktionen. Det returnerade värdet är 13 tecken lång. Det är inte unikt globalt. Du kanske vill kombinera värdet med ett prefix från din namngivningskonvention för att skapa ett beskrivande namn. I följande exempel visas formatet på värdet som returneras. Det faktiska värdet varierar beroende på de angivna parametrarna.

    tcvhiyu5h2o5o

I följande exempel visas hur du använder uniqueString för att skapa ett unikt värde för vanliga nivåer.

Unik som hör till prenumerationen

```json
"[uniqueString(subscription().subscriptionId)]"
```

Unik begränsade till resursgrupp

```json
"[uniqueString(resourceGroup().id)]"
```

Unik begränsade till distributionen för en resursgrupp

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

I följande exempel visar hur du skapar ett unikt namn för ett lagringskonto baserat på din resursgrupp. I resursgruppen och namnet är inte unika om skapas på samma sätt.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Om du vill skapa ett nytt unikt namn varje gång du distribuerar en mall och inte planerar att uppdatera resursen kan du använda den [utcNow](#utcnow) funktion med uniqueString. Du kan använda den här metoden i en testmiljö. Ett exempel finns i [utcNow](#utcnow).

### <a name="return-value"></a>Returvärde

En sträng som innehåller 13 tecken.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) returnerar resultat från uniquestring:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "uniqueRG": {
            "value": "[uniqueString(resourceGroup().id)]",
            "type" : "string"
        },
        "uniqueDeploy": {
            "value": "[uniqueString(resourceGroup().id, deployment().name)]",
            "type" : "string"
        }
    }
}
```

## <a name="uri"></a>URI

`uri (baseUri, relativeUri)`

Skapar en absolut URI genom att kombinera baseUri och relativeUri strängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| baseUri |Ja |sträng |Bas-uri-sträng. |
| relativeUri |Ja |sträng |Relativ uri-strängen att lägga till bas-uri-strängen. |

Värdet för den **baseUri** parameter kan innehålla en viss fil, men endast bassökväg används när URI: N. Till exempel skicka `http://contoso.com/resources/azuredeploy.json` som baseUri parametern resultaten i en bas-URI för `http://contoso.com/resources/`.

### <a name="return-value"></a>Returvärde

En sträng som representerar absolut URI: N för grundläggande och relativa värden.

### <a name="examples"></a>Exempel

I följande exempel visas hur du skapar en länk till en kapslad mall baserat på värdet för den överordnade mallen.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) visar hur du använder uri, uriComponent och uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Kodar en URI.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToEncode |Ja |sträng |Värdet som ska kodas. |

### <a name="return-value"></a>Returvärde

En sträng med URI-kodad värde.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) visar hur du använder uri, uriComponent och uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Returnerar en sträng med en URI-kodad värde.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Ja |sträng |URI-kodad värdet att konvertera till en sträng. |

### <a name="return-value"></a>Returvärde

En avkodade strängen för URI-kodad värde.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) visar hur du använder uri, uriComponent och uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Returnerar det aktuella (UTC) datetime-värdet i formatet som anges. Om inget format anges, används ISO 8601 (yyyyMMddTHHmmssZ)-format. **Den här funktionen kan endast användas i standardvärdet för en parameter.**

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| Format |Nej |sträng |URI-kodad värdet att konvertera till en sträng. Använd antingen [standard formatsträngar](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [anpassade formatsträngar](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Kommentarer

Du kan bara använda den här funktionen i ett uttryck för standardvärdet för en parameter. Den här funktionen någon annanstans i en mall för returneras ett fel. Funktionen är inte tillåten i andra delar av mallen eftersom ett annat värde returneras varje gång den anropas. Distribuera mallen med samma parametrar ger inte på ett tillförlitligt sätt samma resultat.

Om du använder den [alternativet om du vill distribuera om en tidigare lyckad distribution](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails), och den tidigare distributionen innehåller en parameter som använder utcNow, parametern inte omvärderas. Värdet för parametern från tidigare distributionen återanvänds i stället automatiskt i distributionen för återställning.

Var noga med att omdistribuera en mall som är beroende av funktionen utcNow för ett standardvärde. När du distribuerar om och inte ange ett värde för parametern omvärderas funktionen. Om du vill uppdatera en befintlig resurs i stället för att skapa en ny, skicka parametervärdet från tidigare distributionen.

### <a name="return-value"></a>Returvärde

Aktuella UTC-datum/tid-värde.

### <a name="examples"></a>Exempel

Följande exempelmall visar olika format för datum/tid-värde.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

Utdata från föregående exempel varierar för varje distribution, men ska vara detsamma som:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| utcOutput | sträng | 20190305T175318Z |
| utcShortOutput | sträng | 03/05/2019 |
| utcCustomOutput | sträng | 3 5 |

I nästa exempel visas hur du använder ett värde från funktionen när du anger ett taggvärde.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg
* En beskrivning av avsnitt i en Azure Resource Manager-mall finns i [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Om du vill slå samman flera mallar, se [med länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).
* Iterera ett angivet antal gånger när du skapar en typ av resurs, finns i [och skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md).
* Om du vill se hur du distribuerar mallen som du har skapat, se [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md).

