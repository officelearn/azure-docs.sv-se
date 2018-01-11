---
title: "Strömma Azure diagnostiska loggar till en händelsehubb | Microsoft Docs"
description: "Lär dig mer om att strömma Azure diagnostiska loggar till en händelsehubb."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 42bc4845-c564-4568-b72d-0614591ebd80
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2017
ms.author: johnkem
ms.openlocfilehash: bcb9fcb2371217e7082d96ddbba4a095e6d9a00f
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2017
---
# <a name="stream-azure-diagnostic-logs-to-an-event-hub"></a>Azure strömmen diagnostiska loggar till en händelsehubb
**[Azure diagnostikloggar](monitoring-overview-of-diagnostic-logs.md)**  kan strömmas i nära realtid för alla program med alternativet inbyggda ”exportera till Händelsehubbar” i portalen eller genom att aktivera Event Hub auktorisering regel-ID i diagnostikinställningen via Azure PowerShell-Cmdlets eller Azure CLI.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Vad du kan göra med diagnostik loggar och Händelsehubbar
Här följer några olika sätt som du kan använda den strömmande kapaciteten för diagnostikloggar:

* **Strömma loggar till 3 loggning och telemetri part** – kan du strömma alla dina diagnostikloggar till en enda event hub pipe logga data till en SIEM- eller log analytics tredjepartsverktyg.
* **Visa tjänstens hälsa med streaming ”varm path” data till PowerBI** – med Händelsehubbar, Stream Analytics och PowerBI, kan du enkelt transformera diagnostikdata i till nära realtidsinsikter på Azure-tjänster. [Den här dokumentationen artikeln ger en bra överblick över hur du ställer in Händelsehubbar, bearbeta data med Stream Analytics och använder PowerBI som utdata](../stream-analytics/stream-analytics-power-bi-dashboard.md). Här följer några tips om att hämta inställd med diagnostikloggar:
  
  * En händelsehubb för diagnostikloggar skapas automatiskt när du markerar alternativet i portalen eller aktivera via PowerShell, så att du vill välja händelsehubben i namnområdet med namn som börjar med **insights -**.
  * Följande SQL-kod är en Stream Analytics-fråga som du kan använda för att analysera alla loggdata i en PowerBI-tabellen:

    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the PowerBI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

* **Skapa en anpassad telemetri och loggning plattform** – om du redan har ett specialbyggt telemetri plattform eller är bara om du tänker skapa en mycket skalbar natur att publicera och prenumerera i Händelsehubbar kan du flexibelt kan mata in diagnostikloggar. [Finns Dan Rosanova guide med Händelsehubbar i en global skala telemetri platform här](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Strömning av diagnostiska loggar
Du kan aktivera strömning av diagnostikloggar programmässigt via portalen eller med hjälp av den [Azure övervakaren REST API: er](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings). Oavsett hur du skapar en diagnostikinställningen där du anger ett namnområde för Händelsehubbar och logg kategorier och mått som du vill skicka namnområdet. En händelsehubb har skapats i namnområdet för varje logg kategori som du aktiverar. En diagnostik **loggen kategori** är en typ av logg som en resurs kan samla in.

> [!WARNING]
> Aktivera och strömning diagnostikloggar från beräkningsresurser (till exempel virtuella datorer eller Service Fabric) [kräver en annan uppsättning steg](../event-hubs/event-hubs-streaming-azure-diags-data.md).
> 
> 

Namnområdet Händelsehubbar behöver inte finnas i samma prenumeration som resursen avger loggar så länge som den användare som konfigurerar inställningen har lämplig RBAC åtkomst till båda prenumerationer.

## <a name="stream-diagnostic-logs-using-the-portal"></a>Dataströmmen diagnostikloggar med hjälp av portalen
1. Gå till Azure-Monitor i portalen och klicka på **diagnostikinställningar**

    ![Avsnittet av Azure-Monitor övervakning](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-blade.png)

2. Om du vill filtrera listan efter resursgrupp eller resurstyp, och klicka sedan på resursen som du vill ange en diagnostikinställningen.

3. Om det finns inga inställningar på resursen har du valt, uppmanas du för att skapa en inställning. Klicka på ”Aktivera diagnostik”.

   ![Lägg till diagnostikinställningen - inga befintliga inställningar](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-none.png)

   Om det finns befintliga inställningarna på resursen, visas en lista över inställningar som redan har konfigurerats på den här resursen. Klicka på ”Lägg till diagnostikinställningen”.

   ![Lägg till diagnostikinställningen - befintliga inställningar](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-multiple.png)

3. Ge din ange ett namn och markera kryssrutan för **dataströmmen till en händelsehubb**, välj sedan ett namnområde för Händelsehubbar.
   
   ![Lägg till diagnostikinställningen - befintliga inställningar](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-configure.png)
    
   Det namnområde som valts kommer att där händelsehubben skapas (om det här är din första gången strömmande diagnostikloggar) eller strömmas till (om det finns redan resurser som direktuppspelas loggen kategorin för att det här namnområdet), och principen definierar vilka behörigheter som har mekanismen för strömning. Idag kräver strömning till en händelsehubb behörighet att hantera, skicka och lyssna. Du kan skapa eller ändra principer för Händelsehubbar namnområde delad åtkomst i portalen under fliken Konfigurera för namnområdet. Om du vill uppdatera en av dessa diagnostikinställningar måste klienten ha behörigheten ListKey på Händelsehubbar auktoriseringsregeln. Du kan också ange en händelsehubbens namn. Om du anger ett händelsehubbens namn, dirigeras loggar till den event hub i stället för en nyligen skapade händelsehubb per loggen kategori.

4. Klicka på **Spara**.

Den nya inställningen visas i din lista över inställningar för den här resursen efter en liten stund och diagnostiska loggar strömmas till den event hub så snart nya händelsedata genereras.

### <a name="via-powershell-cmdlets"></a>Via PowerShell-Cmdlets
Strömning den [Azure PowerShell-Cmdlets](insights-powershell-samples.md), du kan använda den `Set-AzureRmDiagnosticSetting` med följande parametrar:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -ServiceBusRuleId [your Service Bus rule ID] -Enabled $true
```

Regel-ID för Service Bus är en sträng med formatet: `{Service Bus resource ID}/authorizationrules/{key name}`, till exempel `/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{Service Bus namespace}/authorizationrules/RootManageSharedAccessKey`. Du välja för närvarande inte en viss händelse hubbnamn med PowerShell.

### <a name="via-azure-cli"></a>Via Azure CLI
Strömning den [Azure CLI](insights-cli-samples.md), du kan använda den `insights diagnostic set` kommandot så här:

```azurecli
azure insights diagnostic set --resourceId <resourceID> --serviceBusRuleId <serviceBusRuleID> --enabled true
```

Använd samma format för Service Bus regel-ID som förklaras för PowerShell-cmdleten. För närvarande kan du välja en viss händelse hubbnamn med Azure CLI.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Hur jag för att använda loggdata från Event Hubs?
Här är exempel utdata från Händelsehubbar:

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

| Elementnamn | Beskrivning |
| --- | --- |
| poster |En matris med alla händelser i den här nyttolasten. |
| time |Tid då händelsen inträffade. |
| category |Loggen kategorin för den här händelsen. |
| resourceId |Resurs-ID för den resurs som genereras av den här händelsen. |
| operationName |Namnet på åtgärden. |
| nivå |Valfri. Anger loggningsnivån för händelsen. |
| properties |Egenskaper för händelsen. |

Du kan visa en lista över alla resursproviders som har stöd för strömning till Händelsehubbar [här](monitoring-overview-of-diagnostic-logs.md).

## <a name="stream-data-from-compute-resources"></a>Dataströmmen data från beräkningsresurser
Du kan också strömma diagnostikloggar från beräkningsresurser med Windows Azure-diagnostik-agenten. [Finns den här artikeln](../event-hubs/event-hubs-streaming-azure-diags-data.md) för hur du konfigurerar som.

## <a name="next-steps"></a>Nästa steg
* [Läs mer om Azure diagnostikloggar](monitoring-overview-of-diagnostic-logs.md)
* [Kom igång med Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

