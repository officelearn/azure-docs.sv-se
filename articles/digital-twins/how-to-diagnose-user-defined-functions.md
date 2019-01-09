---
title: 'Så här felsöker du UDF: er i Azure Digital Twins | Microsoft Docs'
description: 'Riktlinjer om hur du felsöker UDF: er i Azure Digital Twins.'
author: stefanmsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: stefanmsft
ms.custom: seodec18
ms.openlocfilehash: ebeed6d2a52937a6e80dfe28574ad854643fa7f2
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119239"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Så här felsöker du användardefinierade funktioner i Azure Digital Twins

Den här artikeln sammanfattar hur du diagnostiserar och felsöka användardefinierade funktioner. Sedan visas några av de vanligaste scenarierna som hittades när du felsöker dem.

>[!TIP]
> Läs [så här konfigurerar du övervakning och loggning](./how-to-configure-monitoring.md) mer information om hur du konfigurerar felsökningsverktyg i Azure Digital Twins med aktivitetsloggar, diagnostikloggar och Azure Monitor.

## <a name="debug-issues"></a>Felsökning av problem

Att veta hur du diagnostiserar problem som kan uppstå i din instans av Azure Digital Twins hjälper dig att effektivt identifiera problemet, orsaken till problemet och en lösning.

### <a name="enable-log-analytics-for-your-instance"></a>Aktivera logganalys för din instans

Loggar och mått för din Azure Digital Twins instans visas i Azure Monitor. Den här dokumentationen förutsätter att du har skapat en [Azure Log Analytics](../azure-monitor/log-query/log-query-overview.md) arbetsytan via den [Azure-portalen](../azure-monitor/learn/quick-create-workspace.md), via [Azure CLI](../azure-monitor/learn/quick-create-workspace-cli.md), eller via [ PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

> [!NOTE]
> Det uppstå en fördröjning på 5 minuter när du skickar händelser till Azure Log Analytics för första gången.

Om du vill konfigurera övervakning och loggning för Azure Digital Twins resurser, läsa [så här konfigurerar du övervakning och loggning](./how-to-configure-monitoring.md).

Läs artikeln [samla in och använda loggdata från resurserna i Azure](../azure-monitor/platform/diagnostic-logs-overview.md) för att konfigurera diagnostiklogginställningar i Azure Digital Twins via Azure Portal, Azure CLI eller PowerShell.

>[!IMPORTANT]
> Se till att välja alla loggkategorier, mått och Azure Log Analytics-arbetsytan.

### <a name="trace-sensor-telemetry"></a>Spårningstelemetri för sensor

Att spåra sensor telemetri, kontrollera att diagnostikinställningar har aktiverats för din Azure Digital Twins-instans. Kontrollera sedan att alla önskade loggkategorier har valts. Kontrollera slutligen att loggarna som skickas till Azure Log Analytics.

Du kan ange ett Korrelations-ID på av data som skickas för att matcha en sensor telemetrimeddelanden till dess respektive loggar. Du gör detta genom att ange den `x-ms-client-request-id` egenskapen till ett GUID.

När du har skickat telemetri, öppnar Azure Log Analytics att fråga efter loggar med uppsättningen Korrelations-ID:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Läsa värde | Ersätt med |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | Korrelations-ID som angavs på händelsedata |

Om du aktiverar loggning för den användardefinierade funktionen loggarna visas i din Azure Log Analytics-instans i kategorin `UserDefinedFunction`. Ange följande fråga villkor i Azure Log Analytics för att hämta dem:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Mer information om kraftfulla frågeåtgärder [komma igång med frågor](../azure-monitor/log-query/get-started-queries.md).

## <a name="identify-common-issues"></a>Identifiera vanliga problem

Både diagnostisera och identifiera vanliga problem är viktigt när du felsöker din lösning. Flera problem som ofta uppstår när du utvecklar användardefinierade funktioner sammanfattas i följande underavsnitt.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Kontrollera om en rolltilldelning har skapats

Den användardefinierade funktionen har inte åtkomst att utföra alla åtgärder som att skicka meddelanden, hämta metadata, utan en rolltilldelning som skapats i Management-API, och inställningen beräknade värden i topologin.

Kontrollera om det finns en rolltilldelning för ditt användardefinierad funktion via Management-API:

```plaintext
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Parametervärde | Ersätt med |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | ID för den användardefinierade funktionen ska hämtas rolltilldelningar för|

Lär dig [så här skapar du en rolltilldelning för användardefinierade funktionen](./how-to-user-defined-functions.md), om det finns inga rolltilldelningar.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Kontrollera om matcher fungerar för en sensor telemetri

Följande anrop mot ditt Azure Digital Twins instanser Management API: et är du kan avgöra om en viss matcher gäller för den angivna sensorn.

```plaintext
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | ID för matcher som du vill utvärdera |
| *YOUR_SENSOR_IDENTIFIER* | ID för sensorn som du vill utvärdera |

Svar:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>Kontrollera vad som utlöser en sensor

Med följande anrop mot Azure Digital Twins Management API: er är du kunna avgöra identifierare av dina egna funktioner som utlöses av den angivna sensorn inkommande telemetri:

```plaintext
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | ID för sensorn att skicka telemetri |

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

När du inte har fått meddelanden från utlösta användardefinierad funktion, bekräftar du att din topologi objektet typparametern matchar typ av identifierare som används.

**Felaktig** exempel:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Det här scenariot uppstår eftersom den använda identifieraren refererar till en sensor medan objekttypen topologi angivna är `Space`.

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

1. **Begränsning**: om den användardefinierade funktionen överskrider Körningsfrekvens gränser som beskrivs i den [tjänstbegränsningar](./concepts-service-limits.md) artikel, kommer att begränsas. Inga vidare åtgärder kört tills begränsningar gränser upphör att gälla.

1. **Det gick inte att hitta data**: om den användardefinierade funktionen försöker få åtkomst till metadata som inte finns åtgärden misslyckas.

1. **Inte auktoriserad**: om den användardefinierade funktionen inte har en rolltilldelning ange eller saknar tillräckligt med behörighet att komma åt vissa metadata från topologin, åtgärden misslyckas.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du aktiverar [övervakning och loggar](../azure-monitor/platform/activity-logs-overview.md) i Azure Digital Twins.
