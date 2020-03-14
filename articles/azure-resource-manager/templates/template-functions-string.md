---
title: Template Functions-String
description: Beskriver de funktioner som används i en Azure Resource Manager mall för att arbeta med strängar.
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: f8d19179461693331a6091ec7a3562f536b959e4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274208"
---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Sträng funktioner för Azure Resource Manager mallar

Resource Manager innehåller följande funktioner för att arbeta med strängar:

* [Base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [ingår](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [saknas](#empty)
* [endsWith](#endswith)
* [förstagångskörningen](#first)
* [formatering](#format)
* [LED](#guid)
* [indexOf](#indexof)
* [pågå](#last)
* [lastIndexOf](#lastindexof)
* [krävande](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [bytt](#replace)
* [Ignorera](#skip)
* [split](#split)
* [startsWith](#startswith)
* [nollängd](#string)
* [under sträng](#substring)
* [gå](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [reducera](#trim)
* [uniqueString](#uniquestring)
* [URI](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)
* [utcNow](#utcnow)

## <a name="base64"></a>Base64

`base64(inputString)`

Returnerar den base64-representation av Indatasträngen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| inputString |Ja |sträng |Värdet som ska returneras som en Base64-representation. |

### <a name="return-value"></a>Returvärde

En sträng som innehåller base64-representation.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) visas hur du använder base64-funktionen.

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
| toJsonOutput | Objekt | {"One": "a", "två": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Konverterar en Base64-representation till ett JSON-objekt.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| base64Value |Ja |sträng |Den base64-representation som ska konverteras till ett JSON-objekt. |

### <a name="return-value"></a>Returvärde

Ett JSON-objekt.

### <a name="examples"></a>Exempel

Följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) använder funktionen base64ToJson för att konvertera ett base64-värde:

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
| toJsonOutput | Objekt | {"One": "a", "två": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Konverterar en Base64-representation till en sträng.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| base64Value |Ja |sträng |Den base64-representation som ska konverteras till en sträng. |

### <a name="return-value"></a>Returvärde

En sträng med det konverterade base64-värdet.

### <a name="examples"></a>Exempel

Följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) använder funktionen base64ToString för att konvertera ett base64-värde:

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
| toJsonOutput | Objekt | {"One": "a", "två": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Kombinerar flera sträng värden och returnerar den sammanfogade strängen, eller kombinerar flera matriser och returnerar den sammanfogade matrisen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |sträng eller matris |Den första strängen eller matrisen för sammanfogning. |
| ytterligare argument |Nej |sträng eller matris |Ytterligare strängar eller matriser i sekventiell ordning för sammanfogning. |

Den här funktionen kan ta valfritt antal argument och kan acceptera antingen strängar eller matriser för parametrarna. Du kan dock inte ange både matriser och strängar för parametrar. Strängar sammanfogas bara med andra strängar.

### <a name="return-value"></a>Returvärde

En sträng eller matris med sammanfogade värden.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) visas hur du kombinerar två sträng värden och returnerar en sammanfogad sträng.

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

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) visas hur du kombinerar två matriser.

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
| returrelaterade | Matris | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>innehåller

`contains (container, itemToFind)`

Kontrollerar om en matris innehåller ett värde, ett objekt innehåller en nyckel eller en sträng innehåller en under sträng. Sträng jämförelsen är Skift läges känslig. Men när du testar om ett objekt innehåller en nyckel är jämförelsen Skift läges okänslig.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| container |Ja |matris, objekt eller sträng |Värdet som innehåller värdet som ska hittas. |
| itemToFind |Ja |sträng eller heltal |Det värde som ska hittas. |

### <a name="return-value"></a>Returvärde

**Sant** om objektet hittas; annars **false**.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) visas hur du använder med olika typer:

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
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Konverterar ett värde till en data-URI.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToConvert |Ja |sträng |Det värde som ska konverteras till en data-URI. |

### <a name="return-value"></a>Returvärde

En sträng formaterad som en data-URI.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) konverteras ett värde till en data-URI och en data-URI konverteras till en sträng:

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
| toStringOutput | String | Hello World! |

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

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) konverteras ett värde till en data-URI och en data-URI konverteras till en sträng:

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
| toStringOutput | String | Hello World! |

## <a name="empty"></a>saknas

`empty(itemToTest)`

Anger om en matris, ett objekt eller en sträng är tom.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |matris, objekt eller sträng |Värdet för att kontrol lera om det är tomt. |

### <a name="return-value"></a>Returvärde

Returnerar **Sant** om värdet är tomt. annars **false**.

### <a name="examples"></a>Exempel

Följande [exempel mal len](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) kontrollerar om en matris, ett objekt och en sträng är tomma.

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
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Anger om en sträng slutar med ett värde. Jämförelsen är inte Skift läges känslig.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |sträng |Det värde som innehåller objektet som ska hittas. |
| stringToFind |Ja |sträng |Det värde som ska hittas. |

### <a name="return-value"></a>Returvärde

**Sant** om det sista tecknet eller tecknen i strängen matchar värdet; annars **false**.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) visas hur du använder funktionerna StartsWith och endsWith:

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
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="first"></a>first

`first(arg1)`

Returnerar det första tecken strängen eller det första elementet i matrisen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris eller sträng |Värdet för att hämta det första elementet eller specialtecknet. |

### <a name="return-value"></a>Returvärde

En sträng med det första tecken eller typen (sträng, heltal, matris eller objekt) för det första elementet i en matris.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) visas hur du använder den första funktionen med en matris och sträng.

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

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

Skapar en formaterad sträng från angivna värden.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| formatString | Ja | sträng | Strängen för sammansatt format. |
| arg1 | Ja | sträng, heltal eller boolesk | Värdet som ska tas med i den formaterade strängen. |
| ytterligare argument | Nej | sträng, heltal eller boolesk | Ytterligare värden som ska tas med i den formaterade strängen. |

### <a name="remarks"></a>Anmärkningar

Använd den här funktionen för att formatera en sträng i mallen. Den använder samma formateringsalternativ som metoden [system. String. format](/dotnet/api/system.string.format) i .net.

### <a name="examples"></a>Exempel

I följande exempel mall visas hur du använder funktionen format.

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
| formatTest | String | Hej, användare. Formaterat tal: 8 175 133 |

## <a name="guid"></a>guid

`guid(baseString, ...)`

Skapar ett värde i formatet för en globalt unik identifierare baserat på de värden som anges som parametrar.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| baseString |Ja |sträng |Värdet som används i hash-funktionen för att skapa GUID. |
| ytterligare parametrar efter behov |Nej |sträng |Du kan lägga till så många strängar som behövs för att skapa värdet som anger nivån av unikhet. |

### <a name="remarks"></a>Anmärkningar

Den här funktionen är användbar när du behöver skapa ett värde i formatet för en globalt unik identifierare. Du anger parameter värden som begränsar omfattningen av unikhet för resultatet. Du kan ange om namnet är unikt för prenumeration, resurs grupp eller distribution.

Det returnerade värdet är inte en slumpmässig sträng, utan i stället resultatet av en hash-funktion på parametrarna. Det returnerade värdet är 36 tecken långt. Den är inte globalt unik. Om du vill skapa ett nytt GUID som inte baseras på det hash-värdet för parametrarna använder du funktionen [newGuid](#newguid) .

I följande exempel visas hur du använder GUID för att skapa ett unikt värde för ofta använda nivåer.

Unikt omfång för prenumeration

```json
"[guid(subscription().subscriptionId)]"
```

Unikt scope till resurs grupp

```json
"[guid(resourceGroup().id)]"
```

Unikt scope till distribution för en resurs grupp

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Returvärde

En sträng som innehåller 36 tecken i formatet för en globalt unik identifierare.

### <a name="examples"></a>Exempel

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) returnerar resultat från GUID:

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

Returnerar den första positionen för ett värde i en sträng. Jämförelsen är inte Skift läges känslig.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |sträng |Det värde som innehåller objektet som ska hittas. |
| stringToFind |Ja |sträng |Det värde som ska hittas. |

### <a name="return-value"></a>Returvärde

Ett heltal som representerar positionen för det objekt som ska hittas. Värdet är noll-baserat. Om objektet inte hittas, returneras-1.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) visas hur du använder funktionerna indexOf och lastIndexOf:

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
| första | Int | 0 |
| Senaste | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="last"></a>last

`last (arg1)`

Returnerar det sista tecken strängen eller det sista elementet i matrisen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris eller sträng |Värdet för att hämta det sista elementet eller specialtecknet. |

### <a name="return-value"></a>Returvärde

En sträng med det sista tecken eller typen (sträng, heltal, matris eller objekt) för det sista elementet i en matris.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) visas hur du använder den sista funktionen med en matris och sträng.

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

Returnerar den sista positionen för ett värde i en sträng. Jämförelsen är inte Skift läges känslig.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |sträng |Det värde som innehåller objektet som ska hittas. |
| stringToFind |Ja |sträng |Det värde som ska hittas. |

### <a name="return-value"></a>Returvärde

Ett heltal som representerar den sista positionen för objektet som ska hittas. Värdet är noll-baserat. Om objektet inte hittas, returneras-1.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) visas hur du använder funktionerna indexOf och lastIndexOf:

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
| första | Int | 0 |
| Senaste | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="length"></a>length

`length(string)`

Returnerar antalet tecken i en sträng, element i en matris eller på rot nivå egenskaper i ett objekt.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matris, sträng eller objekt |Den matris som ska användas för att hämta antalet element, strängen som ska användas för att hämta antalet tecken, eller objektet som ska användas för att hämta antalet rot nivå egenskaper. |

### <a name="return-value"></a>Returvärde

En int. 

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) visas hur du använder length med en matris och en sträng:

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
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="newguid"></a>newGuid

`newGuid()`

Returnerar ett värde i formatet för en globalt unik identifierare. **Den här funktionen kan endast användas i standardvärdet för en parameter.**

### <a name="remarks"></a>Anmärkningar

Du kan bara använda den här funktionen i ett uttryck för standardvärdet för en parameter. Om du använder den här funktionen någon annan stans i en mall returneras ett fel. Funktionen tillåts inte i andra delar av mallen eftersom den returnerar ett annat värde varje gång den anropas. Att distribuera samma mall med samma parametrar skulle inte tillförlitligt producera samma resultat.

Funktionen newGuid skiljer sig från [GUID](#guid) -funktionen eftersom den inte tar några parametrar. När du anropar GUID med samma parameter returneras samma identifierare varje gång. Använd GUID när du behöver generera samma GUID tillförlitligt för en speciell miljö. Använd newGuid när du behöver en annan identifierare varje gång, till exempel att distribuera resurser till en test miljö.

Om du använder [alternativet för att distribuera om en tidigare lyckad distribution](rollback-on-error.md), och den tidigare distributionen innehåller en parameter som använder newGuid, utvärderas inte parametern. I stället återanvänds parametervärdet från den tidigare distributionen automatiskt i återställnings distributionen.

I en test miljö kan du behöva distribuera resurser flera gånger under en kort tid. I stället för att skapa unika namn kan du använda newGuid med [uniqueString](#uniquestring) för att skapa unika namn.

Var noga med att distribuera om en mall som förlitar sig på newGuid-funktionen för ett standardvärde. När du distribuerar om och inte anger något värde för parametern utvärderas funktionen om. Om du vill uppdatera en befintlig resurs i stället för att skapa en ny, måste du skicka värdet i parametervärdet från den tidigare distributionen.

### <a name="return-value"></a>Returvärde

En sträng som innehåller 36 tecken i formatet för en globalt unik identifierare.

### <a name="examples"></a>Exempel

I följande exempel mall visas en parameter med en ny identifierare.

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

Resultatet från föregående exempel varierar för varje distribution, men ser ut ungefär så här:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| guidOutput | sträng | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

I följande exempel används funktionen newGuid för att skapa ett unikt namn för ett lagrings konto. Den här mallen kan fungera för test miljöer där lagrings kontot finns under en kort tid och inte omdistribueras.

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

Resultatet från föregående exempel varierar för varje distribution, men ser ut ungefär så här:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| nameOutput | sträng | storagenziwvyru7uxie |


## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Returnerar en högerjusterad sträng genom att lägga till tecken till vänster tills den totala angivna längden uppnås.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| valueToPad |Ja |sträng eller heltal |Värdet till Högerjustera. |
| totalLength |Ja |int |Det totala antalet tecken i den returnerade strängen. |
| paddingCharacter |Nej |enstaka Character |Det tecken som ska användas för vänster utfyllnad tills den totala längden har uppnåtts. Standardvärdet är ett blank steg. |

Om den ursprungliga strängen är längre än antalet tecken som ska padas läggs inga tecken till.

### <a name="return-value"></a>Returvärde

En sträng med minst antalet angivna tecken.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) visas hur du fyller i det användardefinierade parametervärdet genom att lägga till noll tecknet tills det når det totala antalet tecken. 

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

## <a name="replace"></a>bytt

`replace(originalString, oldString, newString)`

Returnerar en ny sträng med alla instanser av en sträng ersatt av en annan sträng.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| originalString |Ja |sträng |Värdet som innehåller alla instanser av en sträng ersatt av en annan sträng. |
| oldString |Ja |sträng |Strängen som ska tas bort från den ursprungliga strängen. |
| newString |Ja |sträng |Strängen som ska läggas till i stället för den borttagna strängen. |

### <a name="return-value"></a>Returvärde

En sträng med de ersatta tecknen.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) visas hur du tar bort alla streck från den användardefinierade strängen och hur du ersätter delar av strängen med en annan sträng.

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
| firstOutput | String | 1231231234 |
| secondOutput | String | 123-123-xxxx |

## <a name="skip"></a>Ignorera

`skip(originalValue, numberToSkip)`

Returnerar en sträng med alla tecken efter det angivna antalet tecken, eller en matris med alla element efter det angivna antalet element.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| Ursprungligt värde |Ja |matris eller sträng |Matrisen eller strängen som ska användas för att hoppa över. |
| numberToSkip |Ja |int |Det antal element eller tecken som ska hoppas över. Om värdet är 0 eller mindre returneras alla element eller tecken i värdet. Om den är större än längden på matrisen eller strängen returneras en tom matris eller sträng. |

### <a name="return-value"></a>Returvärde

En matris eller sträng.

### <a name="examples"></a>Exempel

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) hoppar över det angivna antalet element i matrisen och det angivna antalet tecken i en sträng.

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
| stringOutput | String | 2 3 |

## <a name="split"></a>split

`split(inputString, delimiter)`

Returnerar en matris med strängar som innehåller under strängarna i Indatasträngen som avgränsas av angivna avgränsare.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| inputString |Ja |sträng |Strängen som ska delas. |
| avgränsare |Ja |sträng eller matris med strängar |Avgränsaren som ska användas för att dela strängen. |

### <a name="return-value"></a>Returvärde

En sträng mat ris.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) delas Indatasträngen med kommatecken, med antingen ett kommatecken eller ett semikolon.

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
| firstOutput | Matris | ["One", "två", "tre"] |
| secondOutput | Matris | ["One", "två", "tre"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Anger om en sträng börjar med ett värde. Jämförelsen är inte Skift läges känslig.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |sträng |Det värde som innehåller objektet som ska hittas. |
| stringToFind |Ja |sträng |Det värde som ska hittas. |

### <a name="return-value"></a>Returvärde

**Sant** om det första tecknet eller tecknen i strängen matchar värdet; annars **false**.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) visas hur du använder funktionerna StartsWith och endsWith:

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
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="string"></a>sträng

`string(valueToConvert)`

Konverterar det angivna värdet till en sträng.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| valueToConvert |Ja | Alla |Värdet som ska konverteras till sträng. Alla typer av värden kan konverteras, inklusive objekt och matriser. |

### <a name="return-value"></a>Returvärde

En sträng med det konverterade värdet.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) visas hur du konverterar olika typer av värden till strängar:

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
| objectOutput | String | {"värdea": 10, "Värdeb": "exempel text"} |
| arrayOutput | String | ["a","b","c"] |
| intOutput | String | 5 |

## <a name="substring"></a>substring

`substring(stringToParse, startIndex, length)`

Returnerar en under sträng som börjar vid den angivna tecken positionen och som innehåller det angivna antalet tecken.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToParse |Ja |sträng |Den ursprungliga sträng som under strängen extraheras från. |
| startIndex |Nej |int |Den nollbaserade start tecken positionen för under strängen. |
| length |Nej |int |Antalet tecken för under strängen. Måste referera till en plats inom strängen. Måste vara noll eller större. |

### <a name="return-value"></a>Returvärde

Under strängen. Eller en tom sträng om längden är noll.

### <a name="remarks"></a>Anmärkningar

Funktionen Miss lyckas när del strängen sträcker sig utanför slutet av strängen eller när längden är mindre än noll. Följande exempel Miss lyckas med felet "index-och längd parametrarna måste referera till en plats inom strängen. Indexparametern: '0', Längdparametern: ‘11’, längden på strängparametern: '10'.".

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Exempel

Följande [exempel mal len](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) extraherar en under sträng från en parameter.

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
| substringOutput | String | tvåsiffrig |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Returnerar en sträng med det angivna antalet tecken från början av strängen, eller en matris med det angivna antalet element från matrisens början.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| Ursprungligt värde |Ja |matris eller sträng |Matrisen eller strängen som elementen ska tas från. |
| numberToTake |Ja |int |Det antal element eller tecken som ska vidtas. Om värdet är 0 eller mindre returneras en tom matris eller sträng. Om det är större än längden på matrisen eller strängen returneras alla element i matrisen eller strängen. |

### <a name="return-value"></a>Returvärde

En matris eller sträng.

### <a name="examples"></a>Exempel

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) använder det angivna antalet element från matrisen och tecken från en sträng.

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
| arrayOutput | Matris | ["One", "två"] |
| stringOutput | String | på |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Konverterar den angivna strängen till gemener.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToChange |Ja |sträng |Värdet som ska konverteras till gemener. |

### <a name="return-value"></a>Returvärde

Strängen konverterad till gemener.

### <a name="examples"></a>Exempel

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) konverterar ett parameter värde till gemener och versaler.

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
| toUpperOutput | String | Ett två tre |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Konverterar den angivna strängen till versaler.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToChange |Ja |sträng |Värdet som ska konverteras till versaler. |

### <a name="return-value"></a>Returvärde

Strängen konverterad till versaler.

### <a name="examples"></a>Exempel

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) konverterar ett parameter värde till gemener och versaler.

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
| toUpperOutput | String | Ett två tre |

## <a name="trim"></a>reducera

`trim (stringToTrim)`

Tar bort alla inledande och avslutande blank stegs tecken från den angivna strängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToTrim |Ja |sträng |Det värde som ska trimmas. |

### <a name="return-value"></a>Returvärde

Strängen utan inledande och avslutande blank stegs tecken.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) trimmas blank stegs tecken från parametern.

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
| returrelaterade | String | Ett två tre |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Skapar en deterministisk hash-sträng baserat på de värden som anges som parametrar. 

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| baseString |Ja |sträng |Värdet som används i hash-funktionen för att skapa en unik sträng. |
| ytterligare parametrar efter behov |Nej |sträng |Du kan lägga till så många strängar som behövs för att skapa värdet som anger nivån av unikhet. |

### <a name="remarks"></a>Anmärkningar

Den här funktionen är användbar när du behöver skapa ett unikt namn för en resurs. Du anger parameter värden som begränsar omfattningen av unikhet för resultatet. Du kan ange om namnet är unikt för prenumeration, resurs grupp eller distribution. 

Det returnerade värdet är inte en slumpmässig sträng, utan i stället resultatet av en hash-funktion. Det returnerade värdet är 13 tecken långt. Den är inte globalt unik. Du kanske vill kombinera värdet med ett prefix från namngivnings konventionen för att skapa ett namn som är meningsfullt. I följande exempel visas formatet för det returnerade värdet. Det faktiska värdet varierar beroende på de angivna parametrarna.

    tcvhiyu5h2o5o

I följande exempel visas hur du använder uniqueString för att skapa ett unikt värde för ofta använda nivåer.

Unikt omfång för prenumeration

```json
"[uniqueString(subscription().subscriptionId)]"
```

Unikt scope till resurs grupp

```json
"[uniqueString(resourceGroup().id)]"
```

Unikt scope till distribution för en resurs grupp

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

I följande exempel visas hur du skapar ett unikt namn för ett lagrings konto baserat på din resurs grupp. Inuti resurs gruppen är namnet inte unikt om det är konstruerat på samma sätt.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Om du behöver skapa ett nytt unikt namn varje gången du distribuerar en mall och inte vill uppdatera resursen, kan du använda funktionen [utcNow](#utcnow) med uniqueString. Du kan använda den här metoden i en test miljö. Ett exempel finns i [utcNow](#utcnow).

### <a name="return-value"></a>Returvärde

En sträng som innehåller 13 tecken.

### <a name="examples"></a>Exempel

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) returnerar resultat från uniquestring:

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

Skapar en absolut URI genom att kombinera baseUri-och relativeUri-strängen.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| baseUri |Ja |sträng |Bas-URI-sträng. Ta hand om det beteende som rör hanteringen av det avslutande snedstrecket ("/"), enligt beskrivningen i den här tabellen.  |
| relativeUri |Ja |sträng |Den relativa URI-sträng som ska läggas till i bas-URI-strängen. |

* Om **baseUri** slutar på ett avslutande snedstreck är resultatet bara **baseUri** , följt av **relativeUri**.

* Om **baseUri** inte avslutas med ett avslutande snedstreck inträffar ett av två saker.  

   * Om **baseUri** inte har några snedstreck alls (förutom från "//" nära fram) är resultatet bara **baseUri** , följt av **relativeUri**.

   * Om **baseUri** har vissa snedstreck, men inte slutar med ett snedstreck, tas allt från det sista snedstrecket bort från **BaseUri** och resultatet är **BaseUri** följt av **relativeUri**.
     
Här följer några exempel:

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```
För fullständig information löses de **baseUri** -och **relativeUri** -parametrarna som anges i [RFC 3986, avsnitt 5](https://tools.ietf.org/html/rfc3986#section-5).

### <a name="return-value"></a>Returvärde

En sträng som representerar den absoluta URI: n för bas-och relativa värden.

### <a name="examples"></a>Exempel

I följande exempel visas hur du skapar en länk till en kapslad mall baserat på värdet för den överordnade mallen.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) visas hur du använder URI, UriComponent och uriComponentToString:

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
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Kodar en URI.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| stringToEncode |Ja |sträng |Det värde som ska kodas. |

### <a name="return-value"></a>Returvärde

En sträng med det URI-kodade värdet.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) visas hur du använder URI, UriComponent och uriComponentToString:

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
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Returnerar en sträng med ett URI-kodat värde.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Ja |sträng |Det URI-kodade värdet som ska konverteras till en sträng. |

### <a name="return-value"></a>Returvärde

En avkodad sträng med URI-kodat värde.

### <a name="examples"></a>Exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) visas hur du använder URI, UriComponent och uriComponentToString:

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
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="utcnow"></a>UtcNow

`utcNow(format)`

Returnerar det aktuella (UTC) datetime-värdet i det angivna formatet. Om inget format anges används formatet ISO 8601 (yyyyMMddTHHmmssZ). **Den här funktionen kan endast användas i standardvärdet för en parameter.**

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| format |Nej |sträng |Det URI-kodade värdet som ska konverteras till en sträng. Använd antingen [standard format strängar](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [anpassade format strängar](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Anmärkningar

Du kan bara använda den här funktionen i ett uttryck för standardvärdet för en parameter. Om du använder den här funktionen någon annan stans i en mall returneras ett fel. Funktionen tillåts inte i andra delar av mallen eftersom den returnerar ett annat värde varje gång den anropas. Att distribuera samma mall med samma parametrar skulle inte tillförlitligt producera samma resultat.

Om du använder [alternativet för att distribuera om en tidigare lyckad distribution](rollback-on-error.md), och den tidigare distributionen innehåller en parameter som använder utcNow, utvärderas inte parametern. I stället återanvänds parametervärdet från den tidigare distributionen automatiskt i återställnings distributionen.

Var noga med att distribuera om en mall som förlitar sig på utcNow-funktionen för ett standardvärde. När du distribuerar om och inte anger något värde för parametern utvärderas funktionen om. Om du vill uppdatera en befintlig resurs i stället för att skapa en ny, måste du skicka värdet i parametervärdet från den tidigare distributionen.

### <a name="return-value"></a>Returvärde

Aktuellt UTC-slutdatum värde.

### <a name="examples"></a>Exempel

I följande exempel mall visas olika format för datetime-värdet.

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

Resultatet från föregående exempel varierar för varje distribution, men ser ut ungefär så här:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| utcOutput | sträng | 20190305T175318Z |
| utcShortOutput | sträng | 03/05/2019 |
| utcCustomOutput | sträng | 3 5 |

I nästa exempel visas hur du använder ett värde från funktionen när du anger ett tagg värde.

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
* En beskrivning av avsnitten i en Azure Resource Manager mall finns i [redigera Azure Resource Manager mallar](template-syntax.md).
* Information om hur du sammanfogar flera mallar finns i [använda länkade mallar med Azure Resource Manager](linked-templates.md).
* Om du vill iterera ett visst antal gånger när du skapar en typ av resurs, se [skapa flera instanser av resurser i Azure Resource Manager](copy-resources.md).
* Information om hur du distribuerar mallen som du har skapat finns i [distribuera ett program med Azure Resource Manager mall](deploy-powershell.md).

