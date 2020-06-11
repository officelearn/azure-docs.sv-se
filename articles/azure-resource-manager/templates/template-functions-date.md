---
title: Mallens funktioner – datum
description: Beskriver de funktioner som används i en Azure Resource Manager mall för att arbeta med datum.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 22bb3c80acb504649e4f5485c4d78dcd04277ec0
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84678009"
---
# <a name="date-functions-for-arm-templates"></a>Datum funktioner för ARM-mallar

Resource Manager innehåller följande funktioner för att arbeta med datum i din Azure Resource Manager-mall (ARM):

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Lägger till en tids period till ett bas värde. ISO 8601-format förväntas.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Description |
|:--- |:--- |:--- |:--- |
| base | Yes | sträng | Start-datetime-värdet för additionen. Använd [formatet ISO 8601-tidsstämpel](https://en.wikipedia.org/wiki/ISO_8601). |
| varaktighet | Yes | sträng | Det tids värde som ska läggas till i basen. Det kan vara ett negativt värde. Använd [varaktighets formatet ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| format | No | sträng | Utdataformatet för datum/tid-resultatet. Om inget värde anges används formatet för bas värdet. Använd antingen [standard format strängar](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [anpassade format strängar](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Returvärde

Det datetime-värde som är resultatet av att lägga till duration-värdet i bas värdet.

### <a name="examples"></a>Exempel

Följande exempel-mall visar olika sätt att lägga till tids värden.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]"
        }
    },
    "variables": {
        "add3Years": "[dateTimeAdd(parameters('baseTime'), 'P3Y')]",
        "subtract9Days": "[dateTimeAdd(parameters('baseTime'), '-P9D')]",
        "add1Hour": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [],
    "outputs": {
        "add3Years": {
            "value": "[variables('add3Years')]",
            "type": "string"
        },
        "subtract9Days": {
            "value": "[variables('subtract9Days')]",
            "type": "string"
        },
        "add1Hour": {
            "value": "[variables('add1Hour')]",
            "type": "string"
        },
    }
}
```

När föregående mall distribueras med en bas tid av `2020-04-07 14:53:14Z` , är utdata:

| Name | Typ | Värde |
| ---- | ---- | ----- |
| add3Years | Sträng | 4/7/2023 2:53:14 PM |
| subtract9Days | Sträng | 3/29/2020 2:53:14 PM |
| add1Hour | Sträng | 4/7/2020 3:53:14 PM |

I nästa exempel-mall visas hur du ställer in start tiden för ett Automation-schema.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "omsAutomationAccountName": {
            "type": "string",
            "defaultValue": "demoAutomation",
            "metadata": {
                "description": "Use an existing Automation account."
            }
        },
        "scheduleName": {
            "type": "string",
            "defaultValue": "demoSchedule1",
            "metadata": {
                "description": "Name of the new schedule."
            }
        },
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]",
            "metadata": {
                "description": "Schedule will start one hour from this time."
            }
        }
    },
    "variables": {
        "startTime": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [
        {
            "name": "[concat(parameters('omsAutomationAccountName'), '/', parameters('scheduleName'))]",
            "type": "microsoft.automation/automationAccounts/schedules",
            "apiVersion": "2015-10-31",
            "location": "eastus2",
            "tags": {
            },
            "properties": {
                "description": "Demo Scheduler",
                "startTime": "[variables('startTime')]",
                "isEnabled": "true",
                "interval": 1,
                "frequency": "hour"
            }
        }
    ],
    "outputs": {
    }
}
```

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Returnerar det aktuella (UTC) datetime-värdet i det angivna formatet. Om inget format anges används formatet ISO 8601 (yyyyMMddTHHmmssZ). **Den här funktionen kan endast användas i standardvärdet för en parameter.**

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Description |
|:--- |:--- |:--- |:--- |
| format |No |sträng |Det URI-kodade värdet som ska konverteras till en sträng. Använd antingen [standard format strängar](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [anpassade format strängar](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Kommentarer

Du kan bara använda den här funktionen i ett uttryck för standardvärdet för en parameter. Om du använder den här funktionen någon annan stans i en mall returneras ett fel. Funktionen tillåts inte i andra delar av mallen eftersom den returnerar ett annat värde varje gång den anropas. Att distribuera samma mall med samma parametrar skulle inte tillförlitligt producera samma resultat.

Om du använder [alternativet för att distribuera om en tidigare lyckad distribution](rollback-on-error.md), och den tidigare distributionen innehåller en parameter som använder utcNow, utvärderas inte parametern. I stället återanvänds parametervärdet från den tidigare distributionen automatiskt i återställnings distributionen.

Var noga med att distribuera om en mall som förlitar sig på utcNow-funktionen för ett standardvärde. När du distribuerar om och inte anger något värde för parametern utvärderas funktionen om. Om du vill uppdatera en befintlig resurs i stället för att skapa en ny, måste du skicka värdet i parametervärdet från den tidigare distributionen.

### <a name="return-value"></a>Returvärde

Aktuellt UTC-slutdatum värde.

### <a name="examples"></a>Exempel

I följande exempel mall visas olika format för datetime-värdet.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Name | Typ | Värde |
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

* En beskrivning av avsnitten i en Azure Resource Manager mall finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
