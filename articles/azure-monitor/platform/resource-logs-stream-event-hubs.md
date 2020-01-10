---
title: Strömma Azure-plattformar loggar till en händelsehubben
description: Lär dig att strömma Azure-resurshanteraren till en Event Hub för att skicka data till externa system, till exempel Siem från tredje part och andra Log Analytics-lösningar.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 00dcc1c1a1d823ab0f2497e47641916d391ee37b
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750351"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>Strömma Azure-plattforms loggar till Azure Event Hubs
[Plattforms loggar](platform-logs-overview.md) i Azure, inklusive Azure aktivitets logg och resurs loggar, ger detaljerad diagnostik och gransknings information för Azure-resurser och Azure-plattformen som de är beroende av.  I den här artikeln beskrivs strömmande plattforms loggar till Event Hub för att skicka data till externa system, till exempel Siem från tredje part och andra Log Analytics-lösningar.


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>Vad du kan göra med plattforms loggar som skickas till en Event Hub
Strömma plattforms loggar i Azure till Event Hubs för att tillhandahålla följande funktioner:

* **Strömma loggar till loggnings-och telemetri system från tredje part** – strömma alla dina plattforms loggar till en enda händelsehubben för att skicka loggdata till ett Siem-eller Log Analytics-verktyg från tredje part.
  
* **Bygg en anpassad telemetri-och loggnings plattform** – den mycket skalbara publicerings prenumerations typen för Event Hub gör att du kan sätta samman plattforms loggar på en anpassad teletry-plattform. Mer information finns i [utforma och ändra storlek på en plattform för global skalnings telemetri på Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .

* **Visa tjänstens hälsa genom att strömma data till Power BI** – Använd Event Hubs, Stream Analytics och Power BI för att omvandla dina diagnostikdata till nära real tids insikter om dina Azure-tjänster. Mer information om den här lösningen finns i [Stream Analytics och Power BI: en analys instrument panel i real tid för att strömma data](../../stream-analytics/stream-analytics-power-bi-dashboard.md) .

    Följande SQL-kod är ett exempel på en Stream Analytics fråga som du kan använda för att parsa alla loggdata i en Power BI tabell:
    
    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

## <a name="prerequisites"></a>Krav
Du måste [skapa en Event Hub](../../event-hubs/event-hubs-create.md) om du inte redan har en. Om du redan har en diagnostisk inställning som använder den här Event Hubs namn rymden kommer den händelsehubben att återanvändas.

Principen för delad åtkomst för namn området definierar de behörigheter som den strömmande mekanismen har. Strömning till Event Hubs kräver behörigheterna hantera, skicka och lyssna. Du kan skapa eller ändra principer för delad åtkomst i Azure Portal under fliken Konfigurera för ditt Event Hubs-namnområde.

Om du vill uppdatera den diagnostiska inställningen för att inkludera strömning måste du ha ListKey-behörighet för den Event Hubs auktoriseringsregeln. Event Hubs namn området behöver inte finnas i samma prenumeration som den prenumeration som avger loggar, så länge den användare som konfigurerar inställningen har lämplig RBAC-åtkomst till båda prenumerationerna och båda prenumerationerna finns i samma AAD-klient.

## <a name="create-a-diagnostic-setting"></a>Skapa en diagnostisk inställning
Skicka plattforms loggar till en Event Hub och andra mål genom att skapa en diagnostisk inställning för en Azure-resurs. Mer information finns i [skapa diagnostisk inställning för insamling av loggar och mått i Azure](diagnostic-settings.md) .

## <a name="collect-data-from-compute-resources"></a>Samla in data från beräknings resurser
Diagnostikinställningar samlar in resurs loggar för Azure Compute-resurser som vilken annan resurs som helst, men inte deras gäst operativ system eller arbets belastningar. Om du vill samla in dessa data installerar du [Log Analytics agenten](log-analytics-agent.md). 


## <a name="consuming-log-data-from-event-hubs"></a>Använda loggdata från Event Hub
Plattforms loggar från Event Hub förbrukas i JSON-format med elementen i följande tabell.

| Elementnamn | Beskrivning |
| --- | --- |
| resurspost |En matris med alla logg händelser i denna nytto Last. |
| time |Tiden då händelsen inträffade. |
| category |Logg kategori för den här händelsen. |
| resourceId |Resurs-ID för den resurs som skapade den här händelsen. |
| operationName |Åtgärdens namn. |
| level |Valfri. Anger händelse nivån för loggen. |
| properties |Händelsens egenskaper. Dessa kan variera för varje Azure-tjänst enligt beskrivningen [ ]()i. |


Följande är exempel på utdata från Event Hubs för en resurs logg:

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```



## <a name="next-steps"></a>Nästa steg

* [Läs mer om resurs loggar](platform-logs-overview.md).
* [Skapa en diagnostisk inställning för insamling av loggar och mått i Azure](diagnostic-settings.md).
* [Strömma Azure Active Directory loggar med Azure Monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Kom igång med Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

