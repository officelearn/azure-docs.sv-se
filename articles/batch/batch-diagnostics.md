---
title: Mått, varningar och diagnostikloggar – Azure Batch | Microsoft Docs
description: Registrera och analysera diagnostiklogg för resurser som pooler och uppgifter i Azure Batch-kontot.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 12/05/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 68d5976a5a79dbde88b7f80b02b39793ffc86de9
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2020
ms.locfileid: "78254849"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batch-mått, aviseringar och loggar för diagnostisk utvärdering och övervakning

 
Den här artikeln förklarar hur du övervakar ett batch-konto med hjälp av funktioner i [Azure Monitor](../azure-monitor/overview.md). Azure Monitor samlar in [Mät värden](../azure-monitor/platform/data-platform-metrics.md) och [diagnostikloggar](../azure-monitor/platform/platform-logs-overview.md) för resurser i batch-kontot. Samla in och använda dessa data i en mängd olika sätt att övervaka ditt Batch-konto och diagnostisera problem. Du kan också konfigurera [mått varningar](../azure-monitor/platform/alerts-overview.md) så att du får meddelanden när ett mått når ett angivet värde. 

## <a name="batch-metrics"></a>Batch-mått

Mått är Azure telemetridata (kallas även prestandaräknare) skickas från dina Azure-resurser som förbrukas av Azure Monitor-tjänsten. Inkludera exempel mått i ett Batch-konto: Pool skapa händelser, med låg prioritet Nodantal och uppgiften klar händelser. 

Se [listan över de batch-mått som stöds](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Mått är:

* Aktiverat som standard i varje Batch-konto utan ytterligare konfiguration
* Genereras varje minut
* Beständiga inte automatiskt, men har en rullande 30-dagars historik. Du kan spara aktivitets mått som en del av diagnostisk loggning.

### <a name="view-metrics"></a>Visa mått

Visa mått för ditt Batch-konto i Azure-portalen. På sidan **Översikt** för kontot visas som standard nyckel nod, kärna och aktivitets mått. 

Visa alla mått för Batch-konto: 

1. I portalen klickar du på **alla tjänster** > **batch-konton**och klickar sedan på namnet på batch-kontot.
2. Klicka på **mått**under **övervakning**.
3. Välj en eller flera av mått. Om du vill kan du välja ytterligare resurs mått med hjälp av list rutorna **prenumerationer**, **resurs grupp**, **resurs typ**och **resurs** .
    * Använd "genomsnittlig" agg regering för beräknings mått (t. ex. "dedikerat antal" eller "antal med låg prioritet"). Använd agg regeringen "count" för händelsebaserade mått (t. ex. att ändra storlek på slutförda händelser för pool).

> [!WARNING]
> Använd inte agg regeringen "sum", som lägger till värdena för alla data punkter som tas emot under perioden i diagrammet
> 
> 

    ![Batch metrics](media/batch-diagnostics/metrics-portal.png)

Hämta mätvärden via programmering genom att använda API: er för Azure Monitor. Se till exempel [hämta Azure Monitor mått med .net](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-reliability"></a>Batch mått tillförlitlighet

Mått är avsedda att användas för trender och dataanalys. Metrisk leverans garanteras inte och kan komma ut ordning, förlust av data och/eller duplicering. Du bör inte använda enkel händelser till varning eller utlösare. Mer information om hur du anger tröskelvärden för aviseringar finns i avsnittet [batch-mått aviseringar](#batch-metric-alerts) .

Fortfarande kan sammanställning av mått som genererats under de senaste 3 minuterna. Under det här tidsintervallet kan du underreported mått värdena.

## <a name="batch-metric-alerts"></a>Måttaviseringar för batch

Alternativt kan du konfigurera mått för nära real tids *aviseringar* som utlöses när värdet för ett angivet mått korsar ett tröskelvärde som du tilldelar. Aviseringen genererar ett [meddelande](../monitoring-and-diagnostics/insights-alerts-portal.md) som du väljer när aviseringen är "aktive rad" (när tröskelvärdet överskrids och varnings villkoret är uppfyllt) samt när det är "löst" (när tröskelvärdet överskrids igen och villkoret inte längre uppfylls). Aviseringar baserat på enskild datapunkter rekommenderas inte eftersom mått är föremål för out-ordning, förlust av data och/eller duplicering. Avisering bör se användning av tröskelvärden för att kompensera för de här inkonsekvenserna.

Du kan till exempel vill konfigurera en metrisk varning när med låg prioritet core beräkningen faller på en viss nivå så att du kan justera sammansättning av dina pooler. Det rekommenderas att ställa in en period av minst 10 minuter där aviseringar Utlös om antal för genomsnittlig med låg prioritet kärnor hamnar under tröskelvärdet för hela perioden. Det rekommenderas inte att Avisera om en 1-5-minutersperiod som kan fortfarande sammanställning av mått.

Konfigurera en metrisk varning i portalen:

1. Klicka på **Alla tjänster** > **Batch-konton** och sedan på namnet på Batch-kontot.
2. Under **övervakning**klickar du på **aviserings regler** > **Lägg till mått avisering**.
3. Välj ett mått, en varningsvillkor (till exempel när ett mått överskrider ett visst värde under en period) och en eller flera meddelanden.

Du kan också konfigurera en nästan real tids avisering med hjälp av [REST API](https://docs.microsoft.com/rest/api/monitor/). Mer information finns i [Översikt över aviseringar](../azure-monitor/platform/alerts-overview.md). Om du vill inkludera jobb, uppgift eller leverantörsspecifik information i dina aviseringar kan du läsa informationen om Sök frågor i [svara på händelser med Azure Monitor aviseringar](../azure-monitor/learn/tutorial-response.md)

## <a name="batch-diagnostics"></a>Batch-diagnostik

Diagnostikloggar innehåller information som genereras av Azure-resurser som beskrivs åtgärden för varje resurs. För Batch, kan du samla in följande loggar:

* **Tjänst loggar** händelser som genereras av tjänsten Azure Batch under en enskild batch-resurss livs längd, till exempel en pool eller uppgift. 

* **Mått** loggar på konto nivå. 

Inställningar för att aktivera insamling av diagnostiska loggar är inte aktiverade som standard. Uttryckligen aktivera diagnostikinställningar för varje Batch-konto som du vill övervaka.

### <a name="log-destinations"></a>Log mål

Ett vanligt scenario är att välja ett Azure Storage-konto som destination log. För att lagra loggar i Azure Storage, skapa kontot innan du aktiverar insamling av loggar. Om du har associerat ett storage-konto med ditt Batch-konto, kan du välja det kontot som log-mål. 

Andra valfritt mål för diagnostikloggar:

* Strömma logg händelser för batch-diagnostikloggar till en [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Event Hubs kan mata in miljontals händelser per sekund, vilket du kan omvandla och lagra med hjälp av valfri leverantör av realtidsanalys. 

* Skicka diagnostikloggar till [Azure Monitor loggar](../log-analytics/log-analytics-overview.md)där du kan analysera dem eller exportera dem för analys i Power BI eller Excel.

> [!NOTE]
> Du kan medföra ytterligare kostnader för att lagra eller bearbeta diagnostiklogg data med Azure-tjänster. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Aktivera insamling av Batch-diagnostikloggar

1. I portalen klickar du på **alla tjänster** > **batch-konton**och klickar sedan på namnet på batch-kontot.
2. Under **övervakning**klickar du på **diagnostikloggar** > **aktiverar diagnostik**.
3. I **diagnostikinställningar**anger du ett namn för inställningen och väljer ett mål för loggen (befintligt lagrings konto, Event Hub eller Azure Monitor loggar). Välj antingen eller både **ServiceLog** och **AllMetrics**.

    När du väljer ett lagringskonto om du vill ange en bevarandeprincip. Om du inte anger ett antal dagar för kvarhållning, bevaras under din arbetsgrens livstid storage-konto.

4. Klicka på **Save** (Spara).

    ![Batch-diagnostik](media/batch-diagnostics/diagnostics-portal.png)

Andra alternativ för att aktivera logg insamling är: Använd Azure Monitor i portalen för att konfigurera diagnostikinställningar, använda en [Resource Manager-mall](../azure-monitor/platform/diagnostic-settings-template.md)eller använda Azure PowerShell eller Azure CLI. Se [samla in och använda loggdata från dina Azure-resurser](../azure-monitor/platform/platform-logs-overview.md).


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
Varje `PT1H.json` BLOB-fil innehåller JSON-formaterade händelser som inträffat inom den timme som anges i BLOB-URL: en (till exempel `h=12`). Under den aktuella timmen läggs händelser till i `PT1H.json`-filen när de inträffar. Värdet för minut (`m=00`) är alltid `00`, eftersom diagnostikloggar för incidenter bryts till enskilda blobbar per timme. (Hela tiden är i UTC.)

Nedan visas ett exempel på en `PoolResizeCompleteEvent` post i en `PT1H.json` loggfil. Den innehåller information om aktuella och mål för dedikerade och låg prioritets noder, samt start-och slut tid för åtgärden:

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Mer information om schemat för diagnostikloggar i lagrings kontot finns i [arkivera Azure-diagnostikloggar](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-platform-logs-in-storage-account). Använda Storage-API: er för att komma åt loggarna i ditt storage-konto via programmering. 

### <a name="service-log-events"></a>Tjänsten logghändelser
Loggar om Azure Batch-tjänsten om som samlas in, innehålla händelser som genereras av Azure Batch-tjänsten under livslängden för en enskild Batch-resurs som en pool eller uppgift. Varje händelse som genereras av Batch loggas i JSON-format. Detta är till exempel bröd texten i en exempel händelse för att **skapa en pool**:

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

| **Tjänst logg händelser** |
| --- |
| [Skapa pool](batch-pool-create-event.md) |
| [Start för borttagning av pool](batch-pool-delete-start-event.md) |
| [Borttagning av pool slutförd](batch-pool-delete-complete-event.md) |
| [Start av poolens storleks ändring](batch-pool-resize-start-event.md) |
| [Storleks ändring av pool slutförd](batch-pool-resize-complete-event.md) |
| [Uppgiftens start](batch-task-start-event.md) |
| [Uppgiften slutförd](batch-task-complete-event.md) |
| [Åtgärden kunde inte utföras](batch-task-fail-event.md) |



## <a name="next-steps"></a>Nästa steg

* Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.
* Lär dig mer om att [övervaka batch-lösningar](monitoring-overview.md).
