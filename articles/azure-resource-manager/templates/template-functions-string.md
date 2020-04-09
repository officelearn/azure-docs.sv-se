---
title: Mallfunktioner - sträng
description: Beskriver de funktioner som ska användas i en Azure Resource Manager-mall för att arbeta med strängar.
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: c0517375b273384f263e8ba421995d4afb6c193b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982422"
---
# <a name="string-functions-for-arm-templates"></a>Strängfunktioner för ARM-mallar

Resource Manager innehåller följande funktioner för att arbeta med strängar i dina ARM-mallar (Azure Resource Manager):

* [base64](#base64)
* [bas64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [Concat](#concat)
* [Innehåller](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [Tom](#empty)
* [slutarMed](#endswith)
* [Första](#first)
* [Format](#format)
* [Guid](#guid)
* [indexOf](#indexof)
* [Senaste](#last)
* [lastIndexOf](#lastindexof)
* [Längd](#length)
* [nyaGuid](#newguid)
* [padVänd](#padleft)
* [Ersätta](#replace)
* [Hoppa över](#skip)
* [split](#split)
* [börjarMed](#startswith)
* [Sträng](#string)
* [Delsträng](#substring)
* [Ta](#take)
* [toLower (TillLågare)](#tolower)
* [tillUpper](#toupper)
* [Trimma](#trim)
* [uniqueString (unikSträngning)](#uniquestring)
* [Uri](#uri)
* [uriKompent](#uricomponent)
* [uriKompent TillString](#uricomponenttostring)

## <a name="base64"></a>base64

`base64(inputString)`

Returnerar bas64-representationen för indatasträngen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| inputString |Ja |sträng |Värdet som ska returneras som en base64-representation. |

### <a name="return-value"></a>Returvärde

En sträng som innehåller base64-representationen.

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
| bas64Utflöde | Sträng | b25lLCB0d28sIHRocmVl |
| toStringOutput | Sträng | Ett två tre |
| tillJsonOutput | Objekt | {"en": "a", "två": "b"} |

## <a name="base64tojson"></a>bas64ToJson

`base64tojson`

Konverterar en base64-representation till ett JSON-objekt.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| bas64Värde |Ja |sträng |Den base64 representation att konvertera till ett JSON-objekt. |

### <a name="return-value"></a>Returvärde

Ett JSON-objekt.

### <a name="examples"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) används funktionen base64ToJson för att konvertera ett base64-värde:

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
| bas64Utflöde | Sträng | b25lLCB0d28sIHRocmVl |
| toStringOutput | Sträng | Ett två tre |
| tillJsonOutput | Objekt | {"en": "a", "två": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Konverterar en base64-representation till en sträng.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| bas64Värde |Ja |sträng |Base64-representationen för att konvertera till en sträng. |

### <a name="return-value"></a>Returvärde

En sträng med det konverterade bas64-värdet.

### <a name="examples"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) används funktionen base64ToString för att konvertera ett base64-värde:

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
| bas64Utflöde | Sträng | b25lLCB0d28sIHRocmVl |
| toStringOutput | Sträng | Ett två tre |
| tillJsonOutput | Objekt | {"en": "a", "två": "b"} |

## <a name="concat"></a>Concat

`concat (arg1, arg2, arg3, ...)`

Kombinerar flera strängvärden och returnerar den sammanfogade strängen, eller kombinerar flera matriser och returnerar den sammanfogade matrisen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |sträng eller matris |Den första strängen eller matrisen för sammanfogning. |
| ytterligare argument |Inga |sträng eller matris |Ytterligare strängar eller matriser i sekventiell ordning för sammanfogning. |

Den här funktionen kan ta valfritt antal argument och kan acceptera antingen strängar eller matriser för parametrarna. Du kan dock inte ange både matriser och strängar för parametrar. Strängar sammanfogas endast med andra strängar.

### <a name="return-value"></a>Returvärde

En sträng eller matris med sammanfogade värden.

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
| concatOutput (concatOutput) | Sträng | prefix-5yj4yjf5mbg72 |

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

## <a name="contains"></a>innehåller

`contains (container, itemToFind)`

Kontrollerar om en matris innehåller ett värde, ett objekt innehåller en nyckel eller en sträng innehåller en delsträng. Strängjämförelsen är skiftlägeskänslig. Men när du testar om ett objekt innehåller en nyckel är jämförelsen skiftlägesokänslig.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| container |Ja |matris, objekt eller sträng |Värdet som innehåller värdet att hitta. |
| itemToFind |Ja |sträng eller int |Värdet att hitta. |

### <a name="return-value"></a>Returvärde

**Sant** om objektet hittas. annars **falskt**.

### <a name="examples"></a>Exempel

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

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Konverterar ett värde till en data-URI.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| strängTillKonvertera |Ja |sträng |Värdet som ska konverteras till en data-URI. |

### <a name="return-value"></a>Returvärde

En sträng formaterad som en data-URI.

### <a name="examples"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) konverteras ett värde till en data-URI och en data-URI konverteras till en sträng:

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
| dataUriOutput | Sträng | data:text/oformaterad;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Sträng | Hej världen! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Konverterar ett data-URI-formaterat värde till en sträng.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Ja |sträng |Data-URI-värdet som ska konverteras. |

### <a name="return-value"></a>Returvärde

En sträng som innehåller det konverterade värdet.

### <a name="examples"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) konverteras ett värde till en data-URI och en data-URI konverteras till en sträng:

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
| dataUriOutput | Sträng | data:text/oformaterad;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Sträng | Hej världen! |

## <a name="empty"></a>tomt

`empty(itemToTest)`

Avgör om en matris, ett objekt eller en sträng är tom.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |matris, objekt eller sträng |Värdet för att kontrollera om det är tomt. |

### <a name="return-value"></a>Returvärde

Returnerar **Sant** om värdet är tomt. annars **falskt**.

### <a name="examples"></a>Exempel

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

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Avgör om en sträng slutar med ett värde. Jämförelsen är skiftlägesokänslig.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| strängTillsökning |Ja |sträng |Värdet som innehåller artikeln att hitta. |
| strängTillhittad |Ja |sträng |Värdet att hitta. |

### <a name="return-value"></a>Returvärde

**Sant** om strängens sista tecken eller tecken matchar värdet. annars **falskt**.

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
| startsTrue (StartsTrue) | Bool | True |
| startsCapTrue (StartsCapTrue) | Bool | True |
| börjarFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="first"></a>Första

`first(arg1)`

Returnerar det första tecknet i strängen eller det första elementet i matrisen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |matris eller sträng |Värdet för att hämta det första elementet eller tecknet. |

### <a name="return-value"></a>Returvärde

En sträng med det första tecknet, eller typen (sträng, int, array eller objekt) för det första elementet i en matris.

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
| arrayOutput | Sträng | en |
| stringOutput | Sträng | O |

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

Skapar en formaterad sträng från indatavärden.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| formatString | Ja | sträng | Den sammansatta formatsträngen. |
| arg1 (arg1) | Ja | sträng, heltal eller booleska | Värdet som ska inkluderas i den formaterade strängen. |
| ytterligare argument | Inga | sträng, heltal eller booleska | Ytterligare värden som ska inkluderas i den formaterade strängen. |

### <a name="remarks"></a>Anmärkningar

Använd den här funktionen om du vill formatera en sträng i mallen. Den använder samma formateringsalternativ som metoden [System.String.Format](/dotnet/api/system.string.format) i .NET.

### <a name="examples"></a>Exempel

Följande exempelmall visar hur du använder formatfunktionen.

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
| formatTest | Sträng | Hej, användare. Formaterat tal: 8 175 133 |

## <a name="guid"></a>Guid

`guid(baseString, ...)`

Skapar ett värde i formatet för en globalt unik identifierare baserat på de värden som anges som parametrar.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| basSträngning |Ja |sträng |Värdet som används i hash-funktionen för att skapa GUID. |
| ytterligare parametrar efter behov |Inga |sträng |Du kan lägga till så många strängar som behövs för att skapa värdet som anger nivån på unikheten. |

### <a name="remarks"></a>Anmärkningar

Den här funktionen är användbar när du behöver skapa ett värde i formatet för en globalt unik identifierare. Du anger parametervärden som begränsar omfattningen av unika värden för resultatet. Du kan ange om namnet är unikt ned till prenumeration, resursgrupp eller distribution.

Det returnerade värdet är inte en slumpmässig sträng, utan snarare resultatet av en hash-funktion på parametrarna. Det returnerade värdet är 36 tecken långt. Det är inte globalt unikt. Om du vill skapa ett nytt GUID som inte baseras på det hash-värdet för parametrarna använder du funktionen [newGuid.](#newguid)

Följande exempel visar hur du använder guid för att skapa ett unikt värde för vanliga nivåer.

Unikt begränsat till prenumeration

```json
"[guid(subscription().subscriptionId)]"
```

Unikt scoped till resursgrupp

```json
"[guid(resourceGroup().id)]"
```

Unikt scoped till distribution för en resursgrupp

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Returvärde

En sträng som innehåller 36 tecken i formatet för en globalt unik identifierare.

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

Returnerar den första positionen för ett värde i en sträng. Jämförelsen är skiftlägesokänslig.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| strängTillsökning |Ja |sträng |Värdet som innehåller artikeln att hitta. |
| strängTillhittad |Ja |sträng |Värdet att hitta. |

### <a name="return-value"></a>Returvärde

Ett heltal som representerar positionen för artikeln att hitta. Värdet är nollbaserat. Om objektet inte hittas returneras -1.

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
| firstT (första) | Int | 0 |
| lastT | Int | 3 |
| firstString (förstaSträngs- | Int | 2 |
| lastString | Int | 0 |
| notFound (notFound) | Int | -1 |

## <a name="last"></a>Senaste

`last (arg1)`

Returnerar strängens sista tecken eller det sista elementet i matrisen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |matris eller sträng |Värdet för att hämta det sista elementet eller tecknet. |

### <a name="return-value"></a>Returvärde

En sträng med det sista tecknet, eller typen (sträng, int, array eller objekt) för det sista elementet i en matris.

### <a name="examples"></a>Exempel

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
| arrayOutput | Sträng | tre |
| stringOutput | Sträng | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Returnerar den sista positionen för ett värde i en sträng. Jämförelsen är skiftlägesokänslig.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| strängTillsökning |Ja |sträng |Värdet som innehåller artikeln att hitta. |
| strängTillhittad |Ja |sträng |Värdet att hitta. |

### <a name="return-value"></a>Returvärde

Ett heltal som representerar den sista positionen för objektet att hitta. Värdet är nollbaserat. Om objektet inte hittas returneras -1.

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
| firstT (första) | Int | 0 |
| lastT | Int | 3 |
| firstString (förstaSträngs- | Int | 2 |
| lastString | Int | 0 |
| notFound (notFound) | Int | -1 |

## <a name="length"></a>length

`length(string)`

Returnerar antalet tecken i en sträng, element i en matris eller egenskaper på rotnivå i ett objekt.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 (arg1) |Ja |matris, sträng eller objekt |Matrisen som ska användas för att hämta antalet element, strängen som ska användas för att hämta antalet tecken eller det objekt som ska användas för att få antalet rotnivåegenskaper. |

### <a name="return-value"></a>Returvärde

En int. 

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

## <a name="newguid"></a>nyaGuid

`newGuid()`

Returnerar ett värde i formatet för en globalt unik identifierare. **Den här funktionen kan bara användas i standardvärdet för en parameter.**

### <a name="remarks"></a>Anmärkningar

Du kan bara använda den här funktionen i ett uttryck för standardvärdet för en parameter. Om du använder den här funktionen någon annanstans i en mall returneras ett fel. Funktionen är inte tillåten i andra delar av mallen eftersom den returnerar ett annat värde varje gång den anropas. Om du distribuerar samma mall med samma parametrar skulle inte samma resultat ge samma resultat på ett tillförlitligt sätt.

Funktionen newGuid skiljer sig från [guid-funktionen](#guid) eftersom den inte tar några parametrar. När du anropar guid med samma parameter returneras samma identifierare varje gång. Använd guid när du behöver tillförlitligt generera samma GUID för en viss miljö. Använd newGuid när du behöver en annan identifierare varje gång, till exempel distribuera resurser till en testmiljö.

Den nya Guid-funktionen använder [Guid-strukturen](/dotnet/api/system.guid) i .NET Framework för att generera den globalt unika identifieraren.

Om du använder [alternativet för att distribuera om en tidigare lyckad distribution](rollback-on-error.md)och den tidigare distributionen innehåller en parameter som använder newGuid, omvärderas inte parametern. I stället återanvänds parametervärdet från den tidigare distributionen automatiskt i återställningsdistributionen.

I en testmiljö kan du behöva distribuera resurser som bara visas flera gånger under en kort tid. I stället för att skapa unika namn kan du använda newGuid med [uniqueString](#uniquestring) för att skapa unika namn.

Var försiktig med att distribuera om en mall som är beroende av den nya Guid-funktionen för ett standardvärde. När du distribuerar om och inte anger något värde för parametern omvärderas funktionen. Om du vill uppdatera en befintlig resurs i stället för att skapa en ny, skicka in parametervärdet från den tidigare distributionen.

### <a name="return-value"></a>Returvärde

En sträng som innehåller 36 tecken i formatet för en globalt unik identifierare.

### <a name="examples"></a>Exempel

I följande exempelmall visas en parameter med en ny identifierare.

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

Utdata från föregående exempel varierar för varje distribution, men liknar:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| guidOutput | sträng | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

I följande exempel används funktionen newGuid för att skapa ett unikt namn för ett lagringskonto. Den här mallen kan fungera för testmiljö där lagringskontot finns under en kort tid och inte distribueras om.

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

Utdata från föregående exempel varierar för varje distribution, men liknar:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| nameOutput | sträng | lagringnziwvyru7uxie |


## <a name="padleft"></a>padVänd

`padLeft(valueToPad, totalLength, paddingCharacter)`

Returnerar en högerjusterad sträng genom att lägga till tecken till vänster tills den totala angivna längden har uppnåtts.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| värdeToPad |Ja |sträng eller int |Värdet för att högerjustera. |
| totalt Längd |Ja |int |Det totala antalet tecken i den returnerade strängen. |
| stoppningTecken |Inga |ett tecken |Tecknet som ska användas för vänsterutfyllnad tills den totala längden har uppnåtts. Standardvärdet är ett blanksteg. |

Om den ursprungliga strängen är längre än antalet tecken som ska pad, läggs inga tecken till.

### <a name="return-value"></a>Returvärde

En sträng med minst antalet angivna tecken.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) visar hur du ska pad parametervärdet för användaren genom att lägga till nolltecknet tills det totala antalet tecken nårs. 

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
| stringOutput | Sträng | 0000000123 |

## <a name="replace"></a>Ersätta

`replace(originalString, oldString, newString)`

Returnerar en ny sträng med alla förekomster av en sträng ersatta av en annan sträng.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| originalSträngning |Ja |sträng |Värdet som har alla förekomster av en sträng ersatt av en annan sträng. |
| oldString (gammalSträng |Ja |sträng |Strängen som ska tas bort från den ursprungliga strängen. |
| nyaSträng |Ja |sträng |Strängen som ska läggas till i stället för den borttagna strängen. |

### <a name="return-value"></a>Returvärde

En sträng med de ersatta tecknen.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) visar hur du tar bort alla streck från den användarinförda strängen och hur du ersätter en del av strängen med en annan sträng.

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
        "secondOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| firstOutput (förstaUtklippet) | Sträng | 1231231234 |
| andraUtskrift | Sträng | 123-123-xxxx |

## <a name="skip"></a>hoppa över

`skip(originalValue, numberToSkip)`

Returnerar en sträng med alla tecken efter det angivna antalet tecken, eller en matris med alla element efter det angivna antalet element.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| originalVärde |Ja |matris eller sträng |Den matris eller sträng som ska användas för att hoppa över. |
| numberToSkip |Ja |int |Antalet element eller tecken som ska hoppas över. Om det här värdet är 0 eller mindre returneras alla element eller tecken i värdet. Om den är större än längden på matrisen eller strängen returneras en tom matris eller sträng. |

### <a name="return-value"></a>Returvärde

En matris eller sträng.

### <a name="examples"></a>Exempel

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
| stringOutput | Sträng | två tre |

## <a name="split"></a>split

`split(inputString, delimiter)`

Returnerar en matris med strängar som innehåller delsträngarna för indatasträngen som avgränsas av de angivna avgränsarna.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| inputString |Ja |sträng |Strängen att dela. |
| avgränsare |Ja |sträng eller matris med strängar |Avgränsaren som ska användas för att dela strängen. |

### <a name="return-value"></a>Returvärde

En matris med strängar.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) delar indatasträngen med ett kommatecken och med antingen ett kommatecken eller ett semikolon.

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
| firstOutput (förstaUtklippet) | Matris | ["en", "två", "tre"] |
| andraUtskrift | Matris | ["en", "två", "tre"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Avgör om en sträng börjar med ett värde. Jämförelsen är skiftlägesokänslig.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| strängTillsökning |Ja |sträng |Värdet som innehåller artikeln att hitta. |
| strängTillhittad |Ja |sträng |Värdet att hitta. |

### <a name="return-value"></a>Returvärde

**Sant** om strängens första tecken eller tecken matchar värdet. annars **falskt**.

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
| startsTrue (StartsTrue) | Bool | True |
| startsCapTrue (StartsCapTrue) | Bool | True |
| börjarFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="string"></a>sträng

`string(valueToConvert)`

Konverterar det angivna värdet till en sträng.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| värdeTillKonvertera |Ja | Alla |Värdet som ska konverteras till sträng. Alla typer av värden kan konverteras, inklusive objekt och matriser. |

### <a name="return-value"></a>Returvärde

En sträng med det konverterade värdet.

### <a name="examples"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) visas hur du konverterar olika typer av värden till strängar:

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
| objectOutput (objektOutput) | Sträng | {"valueA":10,"valueB":"Exempeltext"} |
| arrayOutput | Sträng | ["a","b","c"] |
| intOutput (intOutput) | Sträng | 5 |

## <a name="substring"></a>Delsträng

`substring(stringToParse, startIndex, length)`

Returnerar en delsträng som börjar på den angivna teckenpositionen och innehåller det angivna antalet tecken.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToParse |Ja |sträng |Den ursprungliga strängen som delsträngen extraheras från. |
| startIndex (startIndex) |Inga |int |Den nollbaserade startteckenpositionen för delsträngen. |
| length |Inga |int |Antalet tecken för delsträngen. Måste referera till en plats i strängen. Måste vara noll eller större. |

### <a name="return-value"></a>Returvärde

Delsträngen. Eller en tom sträng om längden är noll.

### <a name="remarks"></a>Anmärkningar

Funktionen misslyckas när delsträngen sträcker sig utanför strängens ände eller när längden är mindre än noll. Följande exempel misslyckas med felet "Parametrarna index och längd måste referera till en plats i strängen. Indexparametern: "0", längdparametern :'11', längden på strängparametern: '10'."

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) extraheras en delsträng från en parameter.

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
| delsträngUtomslag | Sträng | två |

## <a name="take"></a>Ta

`take(originalValue, numberToTake)`

Returnerar en sträng med det angivna antalet tecken från början av strängen, eller en matris med det angivna antalet element från början av matrisen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| originalVärde |Ja |matris eller sträng |Matrisen eller strängen som elementen ska ta från. |
| numberToTake |Ja |int |Antalet element eller tecken som ska vidtas. Om det här värdet är 0 eller mindre returneras en tom matris eller sträng. Om den är större än längden på den angivna matrisen eller strängen returneras alla element i matrisen eller strängen. |

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
| arrayOutput | Matris | ["en", "två"] |
| stringOutput | Sträng | på |

## <a name="tolower"></a>toLower (TillLågare)

`toLower(stringToChange)`

Konverterar den angivna strängen till gemener.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| strängTilländ |Ja |sträng |Värdet som ska konverteras till gemener. |

### <a name="return-value"></a>Returvärde

Strängen konverteras till gemener.

### <a name="examples"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) konverteras ett parametervärde till gemener och versaler.

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
| toLowerOutput | Sträng | Ett två tre |
| tillUpperOutput | Sträng | Ett två tre |

## <a name="toupper"></a>tillUpper

`toUpper(stringToChange)`

Konverterar den angivna strängen till versaler.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| strängTilländ |Ja |sträng |Värdet som ska konverteras till versaler. |

### <a name="return-value"></a>Returvärde

Strängen konverteras till versaler.

### <a name="examples"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) konverteras ett parametervärde till gemener och versaler.

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
| toLowerOutput | Sträng | Ett två tre |
| tillUpperOutput | Sträng | Ett två tre |

## <a name="trim"></a>Trimma

`trim (stringToTrim)`

Tar bort alla inledande och efterföljande blankstegstecken från den angivna strängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToTrim |Ja |sträng |Värdet som ska trimmas. |

### <a name="return-value"></a>Returvärde

Strängen utan inledande och avslutande blankstegstecken.

### <a name="examples"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) trimmas blankstegstecken från parametern.

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
| återgå | Sträng | Ett två tre |

## <a name="uniquestring"></a>uniqueString (unikSträngning)

`uniqueString (baseString, ...)`

Skapar en deterministisk hash-sträng baserat på de värden som anges som parametrar. 

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| basSträngning |Ja |sträng |Värdet som används i hash-funktionen för att skapa en unik sträng. |
| ytterligare parametrar efter behov |Inga |sträng |Du kan lägga till så många strängar som behövs för att skapa värdet som anger nivån på unikheten. |

### <a name="remarks"></a>Anmärkningar

Den här funktionen är användbar när du behöver skapa ett unikt namn för en resurs. Du anger parametervärden som begränsar omfattningen av unika värden för resultatet. Du kan ange om namnet är unikt ned till prenumeration, resursgrupp eller distribution. 

Det returnerade värdet är inte en slumpmässig sträng, utan snarare resultatet av en hash-funktion. Det returnerade värdet är 13 tecken långt. Det är inte globalt unikt. Du kanske vill kombinera värdet med ett prefix från namnkonventionen för att skapa ett namn som är meningsfullt. I följande exempel visas formatet på det returnerade värdet. Det faktiska värdet varierar beroende på de angivna parametrarna.

    tcvhiyu5h2o5o

Följande exempel visar hur du använder uniqueString för att skapa ett unikt värde för vanliga nivåer.

Unikt begränsat till prenumeration

```json
"[uniqueString(subscription().subscriptionId)]"
```

Unikt scoped till resursgrupp

```json
"[uniqueString(resourceGroup().id)]"
```

Unikt scoped till distribution för en resursgrupp

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

I följande exempel visas hur du skapar ett unikt namn för ett lagringskonto baserat på din resursgrupp. I resursgruppen är namnet inte unikt om det skapas på samma sätt.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Om du behöver skapa ett nytt unikt namn varje gång du distribuerar en mall och inte tänker uppdatera resursen kan du använda [funktionen utcNow](template-functions-date.md#utcnow) med uniqueString. Du kan använda den här metoden i en testmiljö. Ett exempel finns i [utcNow](template-functions-date.md#utcnow).

### <a name="return-value"></a>Returvärde

En sträng med 13 tecken.

### <a name="examples"></a>Exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) returnerar resultat från unikstring:

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

## <a name="uri"></a>Uri

`uri (baseUri, relativeUri)`

Skapar en absolut URI genom att kombinera baseUri och relativeUri-strängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| baseUri (av ) |Ja |sträng |Basen uri strängen. Var noga med att observera beteendet när det gäller hanteringen av det efterföljande snedstrecket ('/'), som beskrivs i följande tabell.  |
| relativeUri (relativUri) |Ja |sträng |Den relativa uri strängen att lägga till bas uri strängen. |

* Om **baseUri** slutar i ett avslutande snedstreck, är resultatet helt enkelt **baseUri** följt av **relativeUri**.

* Om **baseUri** inte slutar i en avslutande slash en av två saker händer.  

   * Om **baseUri** inte har några snedstreck alls (bortsett från "//" nära framsidan) är resultatet helt enkelt **baseUri** följt av **relativeUri**.

   * Om **baseUri** har några snedstreck, men inte slutar med ett snedstreck, tas allt från det sista snedstrecket framåt bort från **baseUri** och resultatet är **baseUri** följt av **relativeUri**.
     
Här följer några exempel:

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```
För fullständig information, **baseUri** och **relativeUri** parametrar matchas som anges i [RFC 3986, avsnitt 5](https://tools.ietf.org/html/rfc3986#section-5).

### <a name="return-value"></a>Returvärde

En sträng som representerar den absoluta URI för bas- och relativa värden.

### <a name="examples"></a>Exempel

I följande exempel visas hur du skapar en länk till en kapslad mall baserat på värdet för den överordnade mallen.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) visas hur du använder uri, uriComponent och uriComponentToString:

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
| uriOutput (uriOutput) | Sträng | `http://contoso.com/resources/nested/azuredeploy.json` |
| komponentUtflöde | Sträng | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Sträng | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriKompent

`uricomponent(stringToEncode)`

Kodar en URI.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToEncode |Ja |sträng |Värdet som ska kodas. |

### <a name="return-value"></a>Returvärde

En sträng med det URI-kodade värdet.

### <a name="examples"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) visas hur du använder uri, uriComponent och uriComponentToString:

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
| uriOutput (uriOutput) | Sträng | `http://contoso.com/resources/nested/azuredeploy.json` |
| komponentUtflöde | Sträng | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Sträng | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriKompent TillString

`uriComponentToString(uriEncodedString)`

Returnerar en sträng med ett URI-kodat värde.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Ja |sträng |Uri-kodat värde som ska konverteras till en sträng. |

### <a name="return-value"></a>Returvärde

En avkodad sträng med URI-kodat värde.

### <a name="examples"></a>Exempel

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) visas hur du använder uri, uriComponent och uriComponentToString:

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
| uriOutput (uriOutput) | Sträng | `http://contoso.com/resources/nested/azuredeploy.json` |
| komponentUtflöde | Sträng | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Sträng | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="next-steps"></a>Nästa steg
* En beskrivning av avsnitten i en Azure Resource Manager-mall finns i [Redigera Azure Resource Manager-mallar](template-syntax.md).
* Information om hur du sammanfogar flera mallar finns [i Använda länkade mallar med Azure Resource Manager](linked-templates.md).
* Information om hur du itererar ett angivet antal gånger när du skapar en typ av resurs finns [i Skapa flera instanser av resurser i Azure Resource Manager](copy-resources.md).
* Information om hur du distribuerar mallen som du har skapat finns i [Distribuera ett program med Azure Resource Manager-mallen](deploy-powershell.md).

