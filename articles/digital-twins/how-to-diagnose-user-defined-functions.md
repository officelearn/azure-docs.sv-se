---
title: 'Så här felsöker du UDF: er i Azure Digitals flätas | Microsoft Docs'
description: 'Rikt linjer för hur du felsöker UDF: er i Azure Digitals dubbla.'
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/01/2019
ms.custom: seodec18
ms.openlocfilehash: 7b122df279ecde8ed9ed49b5a89251073f3feda7
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949890"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Så här felsöker du användardefinierade funktioner i Azure Digitals dubbla

I den här artikeln sammanfattas hur du diagnostiserar och felsöker användardefinierade funktioner i Azure Digitals dubbla. Sedan identifierar den några av de vanligaste scenarierna som påträffades vid fel sökning.

>[!TIP]
> Läs [hur du konfigurerar övervakning och loggning](./how-to-configure-monitoring.md) för att lära dig mer om hur du konfigurerar fel söknings verktyg i Azure Digitals med aktivitets loggar, diagnostikloggar och Azure Monitor.

## <a name="debug-issues"></a>Felsöka problem

Genom att känna till hur du diagnostiserar problem i Azure Digitals dubbla, kan du effektivt analysera problem, identifiera orsakerna till problemen och tillhandahålla lämpliga lösningar för dem.

En rad olika loggnings-, analys-och diagnostikverktygs verktyg tillhandahålls för detta ändamål.

### <a name="enable-logging-for-your-instance"></a>Aktivera loggning för din instans

Azure Digitals dubbla har stöd för robust loggning, övervakning och analys. Lösningar utvecklare kan använda Azure Monitor loggar, diagnostikloggar, aktivitets loggar och andra tjänster för att stödja de komplexa övervaknings behoven i en IoT-app. Loggnings alternativ kan kombineras för att fråga eller Visa poster över flera tjänster och för att tillhandahålla detaljerad loggning för många tjänster.

* Information om hur du loggar konfiguration som är unik för Azure Digitals dubbla finns [i Konfigurera övervakning och loggning](./how-to-configure-monitoring.md).
* Läs [Azure Monitor](../azure-monitor/overview.md) översikt och lär dig om kraftfulla logg inställningar som Aktiver ats via Azure Monitor.
* Läs igenom artikeln [samla in och Använd loggdata från dina Azure-resurser](../azure-monitor/platform/resource-logs-overview.md) för att konfigurera diagnostikloggar i Azure Digitals dubbla steg via Azure Portal, Azure CLI eller PowerShell.

När du har konfigurerat kan du välja alla logg kategorier, mått och använda kraftfulla Azure Monitor Log Analytics-arbetsytor som stöd för dina fel söknings åtgärder.

### <a name="trace-sensor-telemetry"></a>Spåra telemetri för spårning

Om du vill spåra sensor telemetri kontrollerar du att diagnostikinställningar har Aktiver ATS för din Azure Digital-instansen. Kontrol lera sedan att alla önskade logg kategorier är markerade. Till sist bekräftar du att önskade loggar skickas till Azure Monitor loggar.

Om du vill matcha ett sensor telemetri till respektive loggar kan du ange ett korrelations-ID för de händelse data som skickas. Det gör du genom att ange egenskapen `x-ms-client-request-id` till ett GUID.

När du har skickat telemetri öppnar du Azure Monitor Log Analytics för att fråga efter loggar med hjälp av ange korrelations-ID:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Värde för fråga | Ersätt med |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | Korrelations-ID som angavs för händelse data |

För att se frågan om alla senaste telemetri loggar:

```Kusto
AzureDiagnostics
| order by CorrelationId desc
```

Om du aktiverar loggning för den användardefinierade funktionen visas dessa loggar i Log Analytics-instansen med kategorin `UserDefinedFunction`. Hämta dem genom att ange följande frågevillkor i Log Analytics:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Läs [komma igång med frågor](../azure-monitor/log-query/get-started-queries.md)om du vill ha mer information om kraftfulla fråge åtgärder.

## <a name="identify-common-issues"></a>Identifiera vanliga problem

Det är viktigt att både diagnostisera och identifiera vanliga problem vid fel sökning av din lösning. Flera problem som ofta uppstår när du utvecklar användardefinierade funktioner sammanfattas i följande underavsnitt.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Kontrol lera om en roll tilldelning har skapats

Utan en roll tilldelning som skapats inom hanterings-API: t har den användardefinierade funktionen inte åtkomst att utföra åtgärder som att skicka meddelanden, hämta metadata och ange beräknade värden i topologin.

Kontrol lera om det finns en roll tilldelning för din användardefinierade funktion via ditt hanterings-API:

```plaintext
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Parametervärde | Ersätt med |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | ID: t för den användardefinierade funktionen för att hämta roll tilldelningar för|

Lär dig [hur du skapar en roll tilldelning för din användardefinierade funktion](./how-to-user-defined-functions.md), om det inte finns några roll tilldelningar.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Kontrol lera om matchnings funktionen fungerar för en sensors telemetri

Med följande anrop mot Azure Digitals dubbla instansers hanterings-API kan du fastställa om en specifik matchning gäller för den aktuella sensorn.

```plaintext
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | ID för den matchning som du vill utvärdera |
| *YOUR_SENSOR_IDENTIFIER* | ID för den sensor som du vill utvärdera |

Svar:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>Kontrol lera vad en sensor utlöser

Med följande anrop till API: erna för digital multiadministration i Azure kan du fastställa identifierarna för dina användardefinierade funktioner som utlöses av den angivna sensorns inkommande telemetri:

```plaintext
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | ID för sensorn för att skicka telemetri |

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

När du inte får meddelanden från den utlöst användardefinierade funktionen, kontrollerar du att parametern topologi objekt typ matchar den typ av identifierare som används.

**Felaktig** Exempel

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Det här scenariot beror på att den använda identifieraren refererar till en sensor medan den angivna topologins objekt typ är `Space`.

**Korrigera** Exempel

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

Det enklaste sättet att inte köra det här problemet är att använda metoden `Notify` på objektet metadata.

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

1. **Begränsning**: om din användardefinierade funktion överskrider gränserna för körnings frekvensen som beskrivs i artikeln om [tjänst begränsningar](./concepts-service-limits.md) kommer den att begränsas. Inga ytterligare åtgärder utfördes förrän begränsnings gränserna har löpt ut.

1. Det gick **inte att hitta data**: om den användardefinierade funktionen försöker komma åt metadata som inte finns, Miss lyckas åtgärden.

1. **Inte auktoriserad**: om den användardefinierade funktionen inte har någon roll tilldelning inställd eller saknar tillräcklig behörighet för att komma åt vissa metadata från topologin, Miss lyckas åtgärden.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du aktiverar [övervakning och loggar](./how-to-configure-monitoring.md) i Azure Digitals dubbla.

- Läs [översikten över Azures aktivitets logg](../azure-monitor/platform/activity-logs-overview.md) artikel om du vill ha fler alternativ för Azure-loggning.
