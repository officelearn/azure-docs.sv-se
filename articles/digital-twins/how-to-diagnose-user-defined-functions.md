---
title: Felsöka UDF - Azure Digital Twins | Microsoft-dokument
description: Lär dig mer om rekommenderade metoder för att felsöka användardefinierade funktioner i Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 518383488aa878dab75aec7ad5da664332b62ad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511645"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Felsöka användardefinierade funktioner i Azure Digital Twins

Den här artikeln sammanfattar hur du diagnostiserar och felsöker användardefinierade funktioner i Azure Digital Twins. Sedan identifierar den några av de vanligaste scenarierna som hittades när du felsöker dem.

>[!TIP]
> Läs [så här konfigurerar du övervakning och loggning](./how-to-configure-monitoring.md) om du vill veta mer om hur du konfigurerar felsökningsverktyg i Azure Digital Twins med hjälp av aktivitetsloggar, diagnostikloggar och Azure Monitor.

## <a name="debug-issues"></a>Problem med felsökning

Genom att veta hur du diagnostiserar problem i Azure Digital Twins kan du effektivt analysera problem, identifiera orsakerna till problem och tillhandahålla lämpliga lösningar för dem.

En mängd olika verktyg för loggning, analys och diagnostik tillhandahålls för detta ändamål.

### <a name="enable-logging-for-your-instance"></a>Aktivera loggning för din instans

Azure Digital Twins stöder robust loggning, övervakning och analys. Lösningar utvecklare kan använda Azure Monitor loggar, diagnostiska loggar, aktivitetsloggar och andra tjänster för att stödja komplexa övervakningsbehov av en IoT-app. Loggningsalternativ kan kombineras för att fråga eller visa poster över flera tjänster och för att tillhandahålla detaljerad loggningstäckning för många tjänster.

* För loggningskonfiguration som är specifik för Azure Digital Twins läser [du Så här konfigurerar du övervakning och loggning](./how-to-configure-monitoring.md).
* Läs översikten [över Azure Monitor](../azure-monitor/overview.md) om du vill veta mer om kraftfulla logginställningar som aktiveras via Azure Monitor.
* Granska artikeln [Samla in och använda loggdata från dina Azure-resurser](../azure-monitor/platform/platform-logs-overview.md) för att konfigurera diagnostiklogginställningar i Azure Digital Twins via Azure-portalen, Azure CLI eller PowerShell.

När du har konfigurerat kan du välja alla loggkategorier, mått och använda kraftfulla Azure Monitor-logganalysarbetsytor för att stödja felsökningsinsatserna.

### <a name="trace-sensor-telemetry"></a>Spåra sensortelemetri

Kontrollera att diagnostikinställningar är aktiverade för din Azure Digital Twins-instans för att spåra sensortelemetri. Se sedan till att alla önskade loggkategorier väljs. Bekräfta slutligen att önskade loggar skickas till Azure Monitor-loggar.

Om du vill matcha ett sensortelemetrimeddelande med respektive loggar kan du ange ett korrelations-ID för händelsedata som skickas. Det gör du `x-ms-client-request-id` genom att ange egenskapen till ett GUID.

När du har skickat telemetri öppnar du Azure Monitor-logganalys för att fråga efter loggar med hjälp av uppsättningen korrelations-ID:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Frågevärde | Ersätt med |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | Korrelations-ID som angavs på händelsedata |

Så här läser du alla senaste frågan om telemetriloggar:

```Kusto
AzureDiagnostics
| order by CorrelationId desc
```

Om du aktiverar loggning för din användardefinierade funktion visas dessa loggar `UserDefinedFunction`i logganalysinstansen med kategorin . Om du vill hämta dem anger du följande frågevillkor i logganalys:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Mer information om kraftfulla frågeåtgärder finns i [Komma igång med frågor](../azure-monitor/log-query/get-started-queries.md).

## <a name="identify-common-issues"></a>Identifiera vanliga problem

Både att diagnostisera och identifiera vanliga problem är viktiga vid felsökning av din lösning. Flera problem som ofta uppstår när du utvecklar användardefinierade funktioner sammanfattas i följande underavsnitt.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Kontrollera om en rolltilldelning har skapats

Utan en rolltilldelning som skapats inom hanterings-API:et har den användardefinierade funktionen inte åtkomst till att utföra åtgärder som att skicka meddelanden, hämta metadata och ange beräknade värden i topologin.

Kontrollera om det finns en rolltilldelning för din användardefinierade funktion via ditt hanterings-API:

```URL
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Parametervärde | Ersätt med |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | ID:et för den användardefinierade funktionen för att hämta rolltilldelningar för|

Lär dig hur du [skapar en rolltilldelning för din användardefinierade funktion](./how-to-user-defined-functions.md)om det inte finns några rolltilldelningar.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Kontrollera om matchstickan fungerar för en sensors telemetri

Med följande anrop mot azure Digital Twins-instansernas hanterings-API kan du avgöra om en viss matchning gäller för den angivna sensorn.

```URL
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | ID:et för de matcher som du vill utvärdera |
| *YOUR_SENSOR_IDENTIFIER* | Sensorns ID som du vill utvärdera |

Svar:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>Kontrollera vad en sensor utlöser

Med följande anrop mot Azure Digital Twins Management API:er kan du fastställa identifierarna för dina användardefinierade funktioner som utlöses av den angivna sensorns inkommande telemetri:

```URL
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | Sensorns ID för att skicka telemetri |

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

När du inte får meddelanden från den utlösta användardefinierade funktionen bekräftar du att parametern för topologyobjekttyp matchar den typ av identifierare som används.

**Felaktigt** Exempel:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Det här scenariot uppstår eftersom den använda identifieraren refererar till `Space`en sensor medan den angivna toologiobjekttypen är .

**Korrekt** Exempel:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

Det enklaste sättet att inte stöta `Notify` på det här problemet är att använda metoden på metadataobjektet.

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

Om du aktiverar diagnostikinställningar kan du stöta på följande vanliga undantag:

1. **Begränsning**: Om din användardefinierade funktion överskrider de körningshastighetsgränser som beskrivs i artikeln [Servicegränser](./concepts-service-limits.md) begränsas den. Inga ytterligare åtgärder har utförts förrän begränsningsgränserna upphör att gälla.

1. **Data hittades inte**: om din användardefinierade funktion försöker komma åt metadata som inte finns, misslyckas åtgärden.

1. **Inte auktoriserad**: om din användardefinierade funktion inte har en rolltilldelningsuppsättning eller saknar tillräckligt med behörighet för att komma åt vissa metadata från topologin misslyckas åtgärden.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du aktiverar [övervakning och loggar](./how-to-configure-monitoring.md) i Azure Digital Twins.

- Läs artikeln [Översikt över Azure Activity-loggen](../azure-monitor/platform/platform-logs-overview.md) för fler Azure-loggningsalternativ.
