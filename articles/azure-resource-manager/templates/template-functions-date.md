---
title: Mallfunktioner - datum
description: Beskriver de funktioner som ska användas i en Azure Resource Manager-mall för att arbeta med datum.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 364b41e9e92cb248a7bd2fac5a41eb535adbf440
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084795"
---
# <a name="date-functions-for-arm-templates"></a>Datumfunktioner för ARM-mallar

Resource Manager innehåller följande funktioner för att arbeta med datum i dina ARM-mallar (Azure Resource Manager):

* [dateTimeAdd](#datetimeadd)
* [utcNow (olikartade)](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Lägger till en tidslängd till ett basvärde. ISO 8601-format förväntas.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| base | Ja | sträng | Startdatumtidsvärdet för tillägget. Använd [ISO 8601 tidsstämpelformat](https://en.wikipedia.org/wiki/ISO_8601). |
| varaktighet | Ja | sträng | Tidsvärdet som ska läggas till basen. Det kan vara ett negativt värde. Använd [iso 8601 varaktighet format](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| format | Inga | sträng | Utdataformatet för datumtidsresultatet. Om det inte anges används basvärdets format. Använd antingen [standardformatsträngar](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [anpassade formatsträngar](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Returvärde

Det datetime-värde som uppstår genom att lägga till varaktighetsvärdet i basvärdet.

### <a name="examples"></a>Exempel

I följande exempelmall visas olika sätt att lägga till tidsvärden.

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

När föregående mall distribueras med en `2020-04-07 14:53:14Z`bastid är utdata:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| add3Years | Sträng | 2023-07-07 14:53:14 |
| subtrahera9Dagar | Sträng | 2020-03-29 14:53:14 |
| add1Hour | Sträng | 2020-07-07 15:53:14 |

Nästa exempelmall visar hur du ställer in starttiden för ett automationsschema.

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

## <a name="utcnow"></a>utcNow (olikartade)

`utcNow(format)`

Returnerar det aktuella datetime-värdet (UTC) i det angivna formatet. Om inget format tillhandahålls används ISO 8601-formatet (yyyyMMddTHHmmssZ). **Den här funktionen kan bara användas i standardvärdet för en parameter.**

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| format |Inga |sträng |Uri-kodat värde som ska konverteras till en sträng. Använd antingen [standardformatsträngar](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [anpassade formatsträngar](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Anmärkningar

Du kan bara använda den här funktionen i ett uttryck för standardvärdet för en parameter. Om du använder den här funktionen någon annanstans i en mall returneras ett fel. Funktionen är inte tillåten i andra delar av mallen eftersom den returnerar ett annat värde varje gång den anropas. Om du distribuerar samma mall med samma parametrar skulle inte samma resultat ge samma resultat på ett tillförlitligt sätt.

Om du använder [alternativet för att distribuera om en tidigare lyckad distribution](rollback-on-error.md)och den tidigare distributionen innehåller en parameter som använder utcNow, omvärderas inte parametern. I stället återanvänds parametervärdet från den tidigare distributionen automatiskt i återställningsdistributionen.

Var försiktig med att distribuera om en mall som är beroende av funktionen utcNow för ett standardvärde. När du distribuerar om och inte anger något värde för parametern omvärderas funktionen. Om du vill uppdatera en befintlig resurs i stället för att skapa en ny, skicka in parametervärdet från den tidigare distributionen.

### <a name="return-value"></a>Returvärde

Det aktuella UTC-datetime-värdet.

### <a name="examples"></a>Exempel

I följande exempelmall visas olika format för datetime-värdet.

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

Utdata från föregående exempel varierar för varje distribution, men liknar:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| utcOutput (utcOutput) | sträng | 20190305T175318Z |
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