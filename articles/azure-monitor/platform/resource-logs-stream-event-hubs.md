---
title: Strömma Azure-plattformsloggar till en händelsehubb
description: Lär dig hur du streamar Azure-resursloggar till en händelsehubb för att skicka data till externa system som SIEM-tillverkare från tredje part och andra logganalyslösningar.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 72341b6da0068ba4b7e3f53b08e6015cafb70f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658922"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>Strömma Azure-plattformsloggar till Azure Event Hubs
[Plattformsloggar](platform-logs-overview.md) i Azure, inklusive Azure Activity-logg och resursloggar, tillhandahåller detaljerad diagnostik- och granskningsinformation för Azure-resurser och den Azure-plattform de är beroende av.  I den här artikeln beskrivs loggböcker för direktuppspelningsplattformar till händelsehubbar för att skicka data till externa system som SIEM-tillverkare från tredje part och andra logganalyslösningar.


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>Vad du kan göra med plattformsloggar som skickas till en händelsehubb
Stream plattform loggar i Azure till händelse nav för att ge följande funktioner:

* **Strömma loggar till loggnings- och telemetrisystem** från tredje part – Strömma alla dina plattformsloggar till en enda händelsenav för att leda loggdata till ett SIEM- eller logganalysverktyg från tredje part.
  
* **Skapa en anpassad telemetri och loggningsplattform** – Den mycket skalbara publicerings-prenumereran av händelsehubbar gör att du flexibelt kanta plattformsloggar till en anpassad teletryplattform. Mer information finns i [Designa och dimensionera en telemetriplattform för global skala på Azure Event Hubs.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

* **Visa tjänstens hälsotillstånd genom att strömma data till Power BI** – Använd eventhubbar, Stream Analytics och Power BI för att omvandla diagnostikdata till nära realtidsinsikter om dina Azure-tjänster. Se [Stream Analytics och Power BI: En instrumentpanel för analys i realtid för direktuppspelning av data](../../stream-analytics/stream-analytics-power-bi-dashboard.md) för mer information om den här lösningen.

    Följande SQL-kod är ett exempel på Stream Analytics-fråga som du kan använda för att tolka alla loggdata i en Power BI-tabell:
    
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
Du måste [skapa en händelsehubb](../../event-hubs/event-hubs-create.md) om du inte redan har en. Om du redan har en diagnostikinställning med det här namnområdet För händelsehubbar kommer händelsehubben att återanvändas.

Principen för delad åtkomst för namnområdet definierar de behörigheter som direktuppspelningsmekanismen har. För att strömma till händelsehubbar krävs behörigheter för hantera, skicka och lyssna. Du kan skapa eller ändra principer för delad åtkomst i Azure-portalen under fliken Konfigurera för namnområdet Event Hubs.

Om du vill uppdatera diagnostikinställningen för att inkludera direktuppspelning måste du ha listnyckelbehörigheten för den behörighetsregeln För eventhubbar. Händelsehubbarnamnområdet behöver inte vara i samma prenumeration som prenumerationen som avger loggar, så länge som användaren som konfigurerar inställningen har lämplig RBAC-åtkomst till både prenumerationer och båda prenumerationerna finns i samma AAD-klient.

## <a name="create-a-diagnostic-setting"></a>Skapa en diagnostikinställning
Skicka plattformsloggar till ett händelsenav och andra mål genom att skapa en diagnostikinställning för en Azure-resurs. Mer information finns [i Skapa diagnostikinställning för att samla in loggar och mått i Azure.](diagnostic-settings.md)

## <a name="collect-data-from-compute-resources"></a>Samla in data från beräkningsresurser
Diagnostikinställningar samlar in resursloggar för Azure-beräkningsresurser som alla andra resurser, men inte deras gästoperativsystem eller arbetsbelastningar. Om du vill samla in dessa data installerar du [Log Analytics-agenten](log-analytics-agent.md). 


## <a name="consuming-log-data-from-event-hubs"></a>Använda loggdata från händelsehubbar
Plattformsloggar från händelsehubbar förbrukas i JSON-format med elementen i följande tabell.

| Elementnamn | Beskrivning |
| --- | --- |
| Poster |En matris med alla logghändelser i den här nyttolasten. |
| time |Tidpunkt då händelsen inträffade. |
| category |Loggkategori för den här händelsen. |
| resourceId |Resurs-ID för den resurs som genererade den här händelsen. |
| operationName |Operationens namn. |
| nivå |Valfri. Anger logghändelsenivån. |
| properties |Egenskaper för händelsen. Dessa varierar för varje Azure-tjänst enligt beskrivningen i [ ](). |


Följande är exempel på utdata från eventhubbar för en resurslogg:

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

* [Läs mer om resursloggar](platform-logs-overview.md).
* [Skapa diagnostikinställning för att samla in loggar och mått i Azure](diagnostic-settings.md).
* [Strömma Azure Active Directory-loggar med Azure Monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Kom igång med eventhubbar](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

