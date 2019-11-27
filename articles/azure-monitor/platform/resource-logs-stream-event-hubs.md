---
title: Strömma Azure-resurs loggar till en Event Hub
description: Lär dig att strömma Azure-resurshanteraren till en Event Hub för att skicka data till externa system, till exempel Siem från tredje part och andra Log Analytics-lösningar.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 680570c5102f656b2b2d2e05f9e08f51fe892f44
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74304940"
---
# <a name="stream-azure-resource-logs-to-azure-event-hubs"></a>Strömma Azures resurs loggar till Azure Event Hubs
[Resurs loggar](resource-logs-overview.md) i Azure ger omfattande, frekventa data om den interna driften av en Azure-resurs. I den här artikeln beskrivs strömmande resurs loggar till händelse hubbar för att skicka data till externa system, till exempel Siem från tredje part och andra Log Analytics-lösningar.


## <a name="what-you-can-do-with-resource-logs-sent-to-an-event-hub"></a>Vad du kan göra med resurs loggar som skickas till en Event Hub
Strömma resurs loggar i Azure till Event Hubs för att tillhandahålla följande funktioner:

* **Strömma loggar till loggnings-och telemetri system från tredje part** – strömma alla dina resurs loggar till en enda händelsehubben för att skicka loggdata till ett Siem-eller Log Analytics-verktyg från tredje part.
* **Bygg en anpassad telemetri-och loggnings plattform** – den mycket skalbara publicerings prenumerations typen för Event Hub gör att du kan mata in resurs loggar på ett flexibelt sätt i en anpassad teletry-plattform. Mer information finns i [utforma och ändra storlek på en plattform för global skalnings telemetri på Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .

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

## <a name="prerequisites"></a>Förutsättningar
Du måste [skapa en Event Hub](../../event-hubs/event-hubs-create.md) om du inte redan har en. Om du tidigare har strömmat resurs loggar till det här Event Hubs namn området, kommer den händelsehubben att återanvändas.

Principen för delad åtkomst för namn området definierar de behörigheter som den strömmande mekanismen har. Strömning till Event Hubs kräver behörigheterna hantera, skicka och lyssna. Du kan skapa eller ändra principer för delad åtkomst i Azure Portal under fliken Konfigurera för ditt Event Hubs-namnområde.

Om du vill uppdatera den diagnostiska inställningen för att inkludera strömning måste du ha ListKey-behörighet för den Event Hubs auktoriseringsregeln. Event Hubs namn området behöver inte finnas i samma prenumeration som den prenumeration som avger loggar, så länge den användare som konfigurerar inställningen har lämplig RBAC-åtkomst till båda prenumerationerna och båda prenumerationerna finns i samma AAD-klient.

## <a name="create-a-diagnostic-setting"></a>Skapa en diagnostisk inställning
Resurs loggar samlas inte in som standard. Skicka dem till en händelsehubben och andra mål genom att skapa en diagnostisk inställning för en Azure-resurs. Mer information finns i [skapa diagnostisk inställning för insamling av loggar och mått i Azure](diagnostic-settings.md) .

## <a name="stream-data-from-compute-resources"></a>Strömma data från beräknings resurser
Processen i den här artikeln är för icke-Compute-resurser enligt beskrivningen i [Översikt över Azures resurs loggar](diagnostic-settings.md).
Strömma resurs loggar från Azure Compute-resurser med hjälp av Windows Azure-diagnostik-agenten. Mer information finns i [strömmande Azure-diagnostik data i den aktiva sökvägen med hjälp av Event Hubs](diagnostics-extension-stream-event-hubs.md) .


## <a name="consuming-log-data-from-event-hubs"></a>Använda loggdata från Event Hub
När du använder resurs loggar från Event Hub är det JSON-format med elementen i följande tabell.

| Element namn | Beskrivning |
| --- | --- |
| resurspost |En matris med alla logg händelser i denna nytto Last. |
| time |Tiden då händelsen inträffade. |
| category |Logg kategori för den här händelsen. |
| resourceId |Resurs-ID för den resurs som skapade den här händelsen. |
| operationName |Åtgärdens namn. |
| nivå |Valfritt. Anger händelse nivån för loggen. |
| Egenskaper |Händelsens egenskaper. Dessa kan variera för varje Azure-tjänst enligt beskrivningen [ ]()i. |


Följande är exempel på utdata från Event Hubs:

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

* [Strömma Azure Active Directory loggar med Azure Monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Läs mer om Azures resurs loggar](resource-logs-overview.md).
* [Kom igång med Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

