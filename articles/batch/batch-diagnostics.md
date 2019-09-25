---
title: Mått, varningar och diagnostikloggar – Azure Batch | Microsoft Docs
description: Registrera och analysera diagnostiklogg för resurser som pooler och uppgifter i Azure Batch-kontot.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 12/05/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: aa86d6cf22562fa1fac7d45de20b28aa0eec33aa
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261663"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batch-mått, aviseringar och loggar för diagnostisk utvärdering och övervakning

 
Den här artikeln förklarar hur du övervakar ett Batch-konto med hjälp av funktionerna i [Azure Monitor](../azure-monitor/overview.md). Azure Monitor samlar in [mått](../azure-monitor/platform/data-platform-metrics.md) och [diagnostikloggar](../azure-monitor/platform/resource-logs-overview.md) för resurser i Batch-kontot. Samla in och använda dessa data i en mängd olika sätt att övervaka ditt Batch-konto och diagnostisera problem. Du kan också konfigurera [måttaviseringar](../azure-monitor/platform/alerts-overview.md) så att du att få meddelanden när ett mått överskrider ett angivet värde. 

## <a name="batch-metrics"></a>Batch-mått

Mått är Azure telemetridata (kallas även prestandaräknare) skickas från dina Azure-resurser som förbrukas av Azure Monitor-tjänsten. Exempel på mått i ett batch-konto är: Pool, skapa händelser, antal noder med låg prioritet och aktivitetens kompletta händelser. 

Se den [lista över mått som stöds Batch](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Mått är:

* Aktiverat som standard i varje Batch-konto utan ytterligare konfiguration
* Genereras varje minut
* Beständiga inte automatiskt, men har en rullande 30-dagars historik. Du kan spara aktivitets mått som en del av diagnostisk loggning.

### <a name="view-metrics"></a>Visa mått

Visa mått för ditt Batch-konto i Azure-portalen. Den **översikt** sidan för kontot som standard visar viktiga nod, core och statistik för uppgiften. 

Visa alla mått för Batch-konto: 

1. I portalen klickar du på **alla tjänster** > **Batch-konton**, och klicka sedan på namnet på ditt Batch-konto.
2. Under **övervakning**, klickar du på **mått**.
3. Välj en eller flera av mått. Om du vill välja ytterligare Resursmått med hjälp av den **prenumerationer**, **resursgrupp**, **resurstyp**, och **Resource** listrutor.
    * Använd "genomsnittlig" agg regering för beräknings mått (t. ex. "dedikerat antal" eller "antal med låg prioritet"). Använd agg regeringen "count" för händelsebaserade mått (t. ex. att ändra storlek på slutförda händelser för pool).

    ![Batch-mått](media/batch-diagnostics/metrics-portal.png)

Hämta mätvärden via programmering genom att använda API: er för Azure Monitor. Se exempelvis [hämta Azure Monitor-mått med .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-reliability"></a>Batch mått tillförlitlighet

Mått är avsedda att användas för trender och dataanalys. Metrisk leverans garanteras inte och kan komma ut ordning, förlust av data och/eller duplicering. Du bör inte använda enkel händelser till varning eller utlösare. Se den [Batch måttaviseringar](#batch-metric-alerts) mer information om hur du anger tröskelvärden för aviseringar.

Fortfarande kan sammanställning av mått som genererats under de senaste 3 minuterna. Under det här tidsintervallet kan du underreported mått värdena.

## <a name="batch-metric-alerts"></a>Måttaviseringar för batch

Alternativt kan du konfigurera nära realtid *måttaviseringar* som utlöser när värdet för ett visst mått överskrider ett tröskelvärde som du tilldelar. Aviseringen genererar en [meddelande](../monitoring-and-diagnostics/insights-alerts-portal.md) du väljer när aviseringen är ”aktiverad” (när tröskelvärdet skärs och aviseringstillståndet uppfylls), samt när det är ”löst” (när tröskelvärdet skärs igen och villkoret är ingen längre uppfyllt). Aviseringar baserat på enskild datapunkter rekommenderas inte eftersom mått är föremål för out-ordning, förlust av data och/eller duplicering. Avisering bör se användning av tröskelvärden för att kompensera för de här inkonsekvenserna.

Du kan till exempel vill konfigurera en metrisk varning när med låg prioritet core beräkningen faller på en viss nivå så att du kan justera sammansättning av dina pooler. Det rekommenderas att ställa in en period av minst 10 minuter där aviseringar Utlös om antal för genomsnittlig med låg prioritet kärnor hamnar under tröskelvärdet för hela perioden. Det rekommenderas inte att Avisera om en 1-5-minutersperiod som kan fortfarande sammanställning av mått.

Konfigurera en metrisk varning i portalen:

1. Klicka på **Alla tjänster** > **Batch-konton** och sedan på namnet på Batch-kontot.
2. Under **övervakning**, klickar du på **Aviseringsregler** > **Lägg till metrisk varning**.
3. Välj ett mått, en varningsvillkor (till exempel när ett mått överskrider ett visst värde under en period) och en eller flera meddelanden.

Du kan också konfigurera en nästan i realtid aviseringen med hjälp av den [REST API](https://docs.microsoft.com/rest/api/monitor/). Mer information finns i [översikt över aviseringar](../azure-monitor/platform/alerts-overview.md)

## <a name="batch-diagnostics"></a>Batch-diagnostik

Diagnostikloggar innehåller information som genereras av Azure-resurser som beskrivs åtgärden för varje resurs. För Batch, kan du samla in följande loggar:

* **Tjänstloggar** händelser som genereras av Azure Batch-tjänsten under livslängden för en enskild Batch-resurs som en pool eller uppgift. 

* **Mått** loggar på kontonivå. 

Inställningar för att aktivera insamling av diagnostiska loggar är inte aktiverade som standard. Uttryckligen aktivera diagnostikinställningar för varje Batch-konto som du vill övervaka.

### <a name="log-destinations"></a>Log mål

Ett vanligt scenario är att välja ett Azure Storage-konto som destination log. För att lagra loggar i Azure Storage, skapa kontot innan du aktiverar insamling av loggar. Om du har associerat ett storage-konto med ditt Batch-konto, kan du välja det kontot som log-mål. 

Andra valfritt mål för diagnostikloggar:

* Stream Batch diagnostiklogg händelser till en [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Event Hubs kan mata in miljontals händelser per sekund, vilket du kan omvandla och lagra med hjälp av valfri leverantör av realtidsanalys. 

* Skicka diagnostikloggar till [Azure Monitor loggar](../log-analytics/log-analytics-overview.md)där du kan analysera dem eller exportera dem för analys i Power BI eller Excel.

> [!NOTE]
> Du kan medföra ytterligare kostnader för att lagra eller bearbeta diagnostiklogg data med Azure-tjänster. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Aktivera insamling av Batch-diagnostikloggar

1. I portalen klickar du på **alla tjänster** > **Batch-konton**, och klicka sedan på namnet på ditt Batch-konto.
2. Under **övervakning**, klickar du på **diagnostikloggar** > **slå på diagnostik**.
3. I **diagnostikinställningar**anger du ett namn för inställningen och väljer ett mål för loggen (befintligt lagrings konto, Event Hub eller Azure Monitor loggar). Välj ett eller båda **ServiceLog** och **AllMetrics**.

    När du väljer ett lagringskonto om du vill ange en bevarandeprincip. Om du inte anger ett antal dagar för kvarhållning, bevaras under din arbetsgrens livstid storage-konto.

4. Klicka på **Spara**.

    ![Batch-diagnostik](media/batch-diagnostics/diagnostics-portal.png)

Andra alternativ för att aktivera insamling av supportloggar omfattar: använda Azure Monitor i portalen för att konfigurera diagnostikinställningar ska du använda en [Resource Manager-mall](../azure-monitor/platform/diagnostic-settings-template.md), eller Använd Azure PowerShell eller Azure CLI. Se [samla in och använda loggdata från resurserna i Azure](../azure-monitor/platform/resource-logs-overview.md).


### <a name="access-diagnostics-logs-in-storage"></a>Diagnostisk loggar i storage

Om du arkiverar Batch-diagnostikloggar i ett lagringskonto skapas en lagringsbehållare i lagringskontot när en relaterade händelse inträffar. BLOB-objekt skapas enligt följande namngivningsmönstret:

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
Exempel:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
Varje `PT1H.json` BLOB-fil innehåller JSON-formaterade händelser som inträffat inom den timme som anges i BLOB- `h=12`URL: en (till exempel). Under den aktuella timmen läggs händelser till i `PT1H.json` filen när de inträffar. Minut värdet (`m=00`) är alltid `00`, eftersom diagnostiska logg händelser bryts till enskilda blobbar per timme. (Hela tiden är i UTC.)

Nedan visas ett exempel på en `PoolResizeCompleteEvent` post i en `PT1H.json` loggfil. Den innehåller information om aktuella och mål för dedikerade och låg prioritets noder, samt start-och slut tid för åtgärden:

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Mer information om schemat för diagnostikloggar i storage-konto finns i [Arkivera Azure-diagnostikloggar](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-resource-logs-in-storage-account). Använda Storage-API: er för att komma åt loggarna i ditt storage-konto via programmering. 

### <a name="service-log-events"></a>Tjänsten logghändelser
Loggar om Azure Batch-tjänsten om som samlas in, innehålla händelser som genereras av Azure Batch-tjänsten under livslängden för en enskild Batch-resurs som en pool eller uppgift. Varje händelse som genereras av Batch loggas i JSON-format. Till exempel det här är brödtexten i ett exempel på **händelse för skapande av pool**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Batch-tjänsten genererar för närvarande följande händelser i loggen för tjänsten. Den här listan kanske inte fullständig, eftersom ytterligare händelser kan ha lagts eftersom den här artikeln senast uppdaterades.

| **Tjänsten logghändelser** |
| --- |
| [Skapande av pool](batch-pool-create-event.md) |
| [Start för borttagning av pool](batch-pool-delete-start-event.md) |
| [Pool ta bort klar](batch-pool-delete-complete-event.md) |
| [Start för storleksändring av pool](batch-pool-resize-start-event.md) |
| [Pool resize klar](batch-pool-resize-complete-event.md) |
| [Uppgiften start](batch-task-start-event.md) |
| [Uppgift slutförd](batch-task-complete-event.md) |
| [Aktiviteten misslyckas](batch-task-fail-event.md) |



## <a name="next-steps"></a>Nästa steg

* Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.
* Läs mer om [övervakningslösningar Batch](monitoring-overview.md).
