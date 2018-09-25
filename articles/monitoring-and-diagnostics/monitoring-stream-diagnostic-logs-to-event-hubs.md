---
title: Stream, Azure-diagnostikloggar till en händelsehubb
description: Lär dig mer om att strömma Azure diagnostikloggar till en händelsehubb.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 19f066bea9de580cf1245aec74fbe563bf8ba449
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996561"
---
# <a name="stream-azure-diagnostic-logs-to-an-event-hub"></a>Stream, Azure-diagnostikloggar till en händelsehubb
**[Azure diagnostikloggar](monitoring-overview-of-diagnostic-logs.md)**  kan strömmas i nära realtid för program med hjälp av alternativet inbyggda ”exportera till Event Hubs” i portalen eller genom att aktivera Event Hub auktorisering regel-ID i en diagnostikinställning via Azure PowerShell-cmdletar eller Azure CLI.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Vad du kan göra med diagnostikloggar och Event Hubs
Här är några sätt som du kan använda den strömmande kapaciteten för diagnostikloggar:

* **Stream loggar till 3 part loggning och telemetri system** – du kan strömma alla dina diagnostikloggar till en enda händelsehubb till pipe loggdata till en tredje parts SIEM- eller log analytics-verktyget.
* **Visa tjänstehälsa med strömmande ”heta sökvägen” data till Power BI** – med hjälp av Event Hubs, Stream Analytics och Power BI, kan du enkelt omvandla diagnostics-data i att nästan i realtid insikter om dina Azure-tjänster. [Den här dokumentationen artikeln ger en bra översikt över hur du ställer in Event Hubs, bearbeta data med Stream Analytics och använda Power BI som utdata](../stream-analytics/stream-analytics-power-bi-dashboard.md). Här följer några tips för att komma som konfigurerats med diagnostikloggar:

  * En händelsehubb för en viss kategori av diagnostikloggar skapas automatiskt när du markerar alternativet i portalen eller aktivera via PowerShell, så att du vill välja händelsehubben i namnområdet med namn som börjar med **insights -**.
  * Följande SQL-kod är en exempelfråga för Stream Analytics som du kan använda för att parsa alla loggdata i till en Power BI-tabell:

    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

* **Skapa en anpassad telemetri och loggning** – om du redan har en specialbyggda telemetri plattform eller precis har du funderingar kring att bygga en mycket skalbar karaktär för Publicera / prenumerera i Händelsehubbar kan du flexibelt kan mata in diagnostikloggar. [Se Dan Rosanova för att använda Event Hubs i en global skala telemetri platform här](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Kunna strömma diagnostikloggar

Du kan aktivera strömning av diagnostikloggar programmässigt, via portalen, eller med hjälp av den [Azure Monitor REST API: er](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). Oavsett hur du skapar en diagnostikinställning där du anger ett namnområde för Event Hubs och loggkategorier och mått som du vill skicka namnområdet. En händelsehubb skapas i namnområdet för varje loggkategori som du aktiverar. En diagnostik **loggkategori** är en typ av logg som en resurs kan samla in.

> [!WARNING]
> Aktivera och strömma diagnostikloggar från beräkningsresurser (till exempel virtuella datorer eller Service Fabric) [kräver en annan uppsättning steg](../event-hubs/event-hubs-streaming-azure-diags-data.md).

Event Hubs namnområde inte har finnas i samma prenumeration som resursen loggarna så länge som den användare som konfigurerar inställningen har lämplig RBAC-åtkomst till både prenumerationer och båda prenumerationerna är en del av samma AAD-klient.

> [!NOTE]
> Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
>
> *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och läggas till på per-kö-nivå. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Stream diagnostikloggar med hjälp av portalen

1. I portalen, gå till Azure Monitor och klicka på **diagnostikinställningar**

    ![Avsnittet av Azure Monitor övervakning](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-blade.png)

2. Du kan också filtrera listan efter resursgruppen eller resursen. Klicka sedan på den resurs som du vill ange en diagnostikinställning.

3. Om inga inställningar finns på resursen har du valt, uppmanas du för att skapa en inställning. Klicka på ”Slå på diagnostik”.

   ![Lägg till diagnostikinställning - inga befintliga inställningar](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-none.png)

   Om det finns befintliga inställningarna på resursen, visas en lista över inställningar som redan har konfigurerats på den här resursen. Klicka på ”Lägg till diagnostikinställning”.

   ![Lägg till diagnostikinställning - befintliga inställningar](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-multiple.png)

3. Ge din ställa in ett namn och markera kryssrutan för **Stream till en händelsehubb**, Välj ett namnområde för Event Hubs.

   ![Lägg till diagnostikinställning - befintliga inställningar](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-configure.png)

   Det namnområde som valts kommer att där händelsehubben skapas (om det här är din första gången strömmande diagnostikloggar) eller strömma till (om det finns redan resurser som direktuppspelas log kategorin för att det här namnområdet), och principen som definierar behörigheterna som den mekanism för direktuppspelning har. Idag, kräver strömma till en event hub behörighet att hantera, skicka och lyssna. Du kan skapa eller ändra åtkomstprinciper för Event Hubs-namnområde som delas i portalen under fliken Konfigurera för ditt namnområde. Om du vill uppdatera en av dessa diagnostikinställningar måste klienten ha behörigheten ListKey på auktoriseringsregel för Event Hubs. Du kan även ange en event hub-namn. Om du anger en event hub-namn, dirigeras loggar till den händelsehubben i stället för en nyligen skapade händelsehubben per loggkategori.

4. Klicka på **Spara**.

Den nya inställningen visas i din lista över inställningar för den här resursen efter en liten stund och diagnostiska loggar strömmas till den händelsehubben när nya händelsedata genereras.

### <a name="via-powershell-cmdlets"></a>Via PowerShell-cmdletar

Strömning den [Azure PowerShell-Cmdlets](insights-powershell-samples.md), du kan använda den `Set-AzureRmDiagnosticSetting` cmdlet med följande parametrar:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -EventHubAuthorizationRuleId [your Event Hub namespace auth rule ID] -Enabled $true
```

Event Hub auktorisering regel-ID är en sträng med det här formatet: `{Event Hub namespace resource ID}/authorizationrules/{key name}`, till exempel `/subscriptions/{subscription ID}/resourceGroups/{resource group}/providers/Microsoft.EventHub/namespaces/{Event Hub namespace}/authorizationrules/RootManageSharedAccessKey`. Du kan inte för närvarande välja en viss event hub-namn med PowerShell.

### <a name="via-azure-cli"></a>Via Azure CLI

Strömning den [Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest), du kan använda den [az monitor diagnostic-settings skapa](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) kommando.

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Du kan lägga till ytterligare kategorier diagnostikloggen genom att lägga till ordlistor JSON-matris som skickas som den `--logs` parametern.

Den `--event-hub-rule` argumentet använder samma format som Event Hub auktorisering regel-ID enligt beskrivningen för PowerShell-cmdleten.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Hur jag för att använda loggdata från Event Hubs?

Här är exempel på utdata data från Event Hubs:

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
| category |Loggkategori för den här händelsen. |
| resourceId |Resurs-ID för den resurs som genereras av den här händelsen. |
| operationName |Åtgärdens namn. |
| nivå |Valfri. Anger loggningsnivån för händelsen. |
| properties |Egenskaper för händelsen. |

Du kan visa en lista över alla resursprovidrar som har stöd för direktuppspelning till Event Hubs [här](monitoring-overview-of-diagnostic-logs.md).

## <a name="stream-data-from-compute-resources"></a>Stream-data från beräkningsresurser

Du kan också strömma diagnostikloggar från Compute-resurser med hjälp av Windows Azure Diagnostics-agenten. [Se den här artikeln](../event-hubs/event-hubs-streaming-azure-diags-data.md) för hur du konfigurerar som.

## <a name="next-steps"></a>Nästa steg

* [Stream Azure Active Directory-loggar med Azure Monitor](../active-directory/reports-monitoring/quickstart-azure-monitor-stream-logs-to-event-hub.md)
* [Läs mer om Azure-diagnostikloggar](monitoring-overview-of-diagnostic-logs.md)
* [Kom igång med Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
