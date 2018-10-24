---
title: 'Så här felsöker du UDF: er i Azure Digital Twins | Microsoft Docs'
description: 'Riktlinjer om hur du felsöker UDF: er i Azure Digital Twins'
author: stefanmsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: stefanmsft
ms.openlocfilehash: 852b2d35ae605f5529d162d52655fd258ca07c5a
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946104"
---
# <a name="how-to-debug-issues-with-user-defined-functions-in-azure-digital-twins"></a>Så här felsöker du problem med användardefinierade funktioner i Azure Digital Twins

Den här artikeln sammanfattar hur du diagnostiserar användardefinierade funktioner. Sedan visas några av de vanligaste scenarierna som påträffades när du arbetar med dem.

## <a name="debug-issues"></a>Felsökning av problem

Att veta hur du diagnostiserar problem som kan uppstå i din instans av Azure Digital Twins hjälper dig att effektivt identifiera problemet och orsaken till problemet och en lösning.

### <a name="enable-log-analytics-for-your-instance"></a>Aktivera logganalys för din instans

Loggar och mått för din instans av Azure Digital Twins blir tillgängliga via Azure Monitor. Följande dokumentation förutsätter att du har skapat en [Azure Log Analytics](../log-analytics/log-analytics-queries.md) arbetsytan via den [Azure-portalen](../log-analytics/log-analytics-quick-create-workspace.md), via [Azure CLI](../log-analytics/log-analytics-quick-create-workspace-cli.md), eller via [ PowerShell](../log-analytics/log-analytics-quick-create-workspace-posh.md).

> [!NOTE]
> Det uppstår en fördröjning i 5 minuter när du skickar händelser till **Log Analytics** för första gången.

Läs artikeln [”samla in och använda loggdata från resurserna i Azure”](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) att aktivera diagnostikinställningar för din Azure Digital Twins instans via portalen, Azure CLI eller PowerShell. Se till att välja alla loggkategorier, mått och Azure Log Analytics-arbetsytan.

### <a name="trace-sensor-telemetry"></a>Spårningstelemetri för sensor

Kontrollera att diagnostikinställningar är aktiverat på din digitala Twins för Azure-instans, alla loggkategorier är markerade och att loggarna skickas till Azure Log Analytics.

Du kan ange ett Korrelations-ID på av data som skickas för att matcha en sensor telemetrimeddelanden till dess respektive loggar. Du gör detta genom att ange den `x-ms-client-request-id` egenskapen till ett GUID.

När du har skickat telemetri, öppnar Azure Log Analytics att fråga efter loggar med uppsättningen Korrelations-ID:

```Kusto
AzureDiagnostics
| where CorrelationId = 'yourCorrelationIdentifier'
```

| Anpassade attributets namn | Ersätt med |
| --- | --- |
| *yourCorrelationIdentifier* | Korrelations-ID som angavs på händelsedata |

Om du loggar den användardefinierade funktionen loggarna visas i din Azure Log Analytics-instans i kategorin `UserDefinedFunction`. Ange följande fråga villkor i Azure Log Analytics för att hämta dem:

```Kusto
AzureDiagnostics
| where Category = 'UserDefinedFunction'
```

Läs mer om kraftfulla frågeåtgärder [komma igång med frågor](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries).

## <a name="identify-common-issues"></a>Identifiera vanliga problem

Både diagnostisera och identifiera vanliga problem är viktigt när du felsöker din lösning. Flera vanliga problem som uppstår vid utveckla användardefinierade funktioner sammanfattas nedan.

### <a name="ensure-a-role-assignment-was-created"></a>Se till att en rolltilldelning har skapats

Den användardefinierade funktionen har inte åtkomst att utföra alla åtgärder som att skicka meddelanden, hämta metadata, utan en rolltilldelning som skapats i API Management, och inställningen beräknade värden i topologin.

Kontrollera om det finns en rolltilldelning för ditt användardefinierad funktion via Management-API:

```plaintext
GET https://yourManagementApiUrl/api/v1.0/roleassignments?path=/&traverse=Down&objectId=yourUserDefinedFunctionId
```

| Anpassade attributets namn | Ersätt med |
| --- | --- |
| *yourManagementApiUrl* | Den fullständiga URL-sökvägen för API Management  |
| *yourUserDefinedFunctionId* | ID för den användardefinierade funktionen ska hämtas rolltilldelningar för|

Följ den här artikeln på om ingen rolltilldelning hämtas [så här skapar du en rolltilldelning för användardefinierade funktionen](./how-to-user-defined-functions.md).

### <a name="check-if-the-matcher-will-work-for-a-sensors-telemetry"></a>Kontrollera om matcher fungerar för en sensor telemetri

Med följande anrop mot ditt Azure Digital Twins instanser Management API: et kommer du att kunna avgöra om en viss matcher gäller för den angivna sensorn.

```plaintext
GET https://yourManagementApiUrl/api/v1.0/matchers/yourMatcherIdentifier/evaluate/yourSensorIdentifier?enableLogging=true
```

| Anpassade attributets namn | Ersätt med |
| --- | --- |
| *yourManagementApiUrl* | Den fullständiga URL-sökvägen för API Management  |
| *yourMatcherIdentifier* | ID för matcher som du vill utvärdera |
| *yourSensorIdentifier* | ID för sensorn som du vill utvärdera |

Svar:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-will-trigger"></a>Kontrollera vad som ska utlösa en sensor

Du kommer att kunna fastställa identifierare av dina egna funktioner som utlöses av den angivna sensorn inkommande telemetri med följande anrop mot ditt Azure Digital Twins instanser Management-API:

```plaintext
GET https://yourManagementApiUrl/api/v1.0/sensors/yourSensorIdentifier/matchers?includes=UserDefinedFunctions
```

| Anpassade attributets namn | Ersätt med |
| --- | --- |
| *yourManagementApiUrl* | Den fullständiga URL-sökvägen för API Management  |
| *yourSensorIdentifier* | ID för den sensor som kommer att skicka telemetri |

Svar:

```JavaScript
[
    {
        "id": "48a64768-797e-4832-86dd-de625f5f3fd9",
        "name": "MotionMatcher",
        "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
        "conditions": [
            {
                "id": "024a067a-414f-415b-8424-7df61392541e",
                "target": "Sensor",
                "path": "$.dataType",
                "value": "\"Motion\"",
                "comparison": "Equals"
            }
        ],
        "userDefinedFunctions": [
            {
                "id": "373d03c5-d567-4e24-a7dc-f993460120fc",
                "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
                "name": "Motion User-Defined Function",
                "disabled": false
            }
        ]
    }
]
```

### <a name="issue-with-receiving-notifications"></a>Problem med att ta emot meddelanden

När du inte ta emot meddelanden från utlösta användardefinierad funktion kontrollerar du att matchar din topologi objektet typparametern typ av identifierare som används.

**Felaktig** exempel:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Det här scenariot uppstår eftersom används identifieraren som refererar till en sensor topologi objekttyp som angetts är ”utrymme”.

**Rätt** exempel:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

Det enklaste sättet att inte stöter på det här problemet är att använda den `Notify` metoden i metadata-objektet.

Exempel:

```JavaScript
function process(telemetry, executionContext) {
    var sensorMetadata = getSensorMetadata(telemetry.SensorId);

    var customNotification = {
        Message: 'Custom notification'
    };

    // Short-hand for above methods where object type is known from metadata.
    sensorMetadata.Notify(JSON.stringify(customNotification));
}
```

## <a name="common-diagnostic-exceptions"></a>Vanliga diagnostiska undantag

Om du aktiverar diagnostikinställningar kan du stöta på dessa vanliga undantag:

1. **Begränsning**: om den användardefinierade funktionen överskrider Körningsfrekvens gränser som beskrivs i den [tjänstbegränsningar](./concepts-service-limits.md) artikel, kommer att begränsas. Begränsning innebär inga ytterligare åtgärder har köras tills gränserna upphör att gälla.

1. **Det gick inte att hitta data**: om den användardefinierade funktionen försöker få åtkomst till metadata som inte finns, misslyckas åtgärden.

1. **Inte auktoriserad**: om den användardefinierade funktionen inte har en rolltilldelning ange eller saknar tillräckligt med behörighet att komma åt vissa metadata från topologin, misslyckas åtgärden.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du aktiverar [övervakning och loggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) i Azure Digital Twins.
