---
title: Mått, aviseringar och diagnostiska loggar
description: Registrera och analysera diagnostiska logg händelser för Azure Batch konto resurser som pooler och uppgifter.
ms.topic: article
ms.date: 12/05/2018
ms.custom: seodec18
ms.openlocfilehash: 7f75a8302c8ba368138e6c8edee6c6069c5031d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82117309"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batch-mått, aviseringar och loggar för diagnostisk utvärdering och övervakning

 
Den här artikeln förklarar hur du övervakar ett batch-konto med hjälp av funktioner i [Azure Monitor](../azure-monitor/overview.md). Azure Monitor samlar in [Mät värden](../azure-monitor/platform/data-platform-metrics.md) och [diagnostikloggar](../azure-monitor/platform/platform-logs-overview.md) för resurser i batch-kontot. Samla in och Använd dessa data på flera olika sätt för att övervaka ditt batch-konto och diagnostisera problem. Du kan också konfigurera [mått varningar](../azure-monitor/platform/alerts-overview.md) så att du får meddelanden när ett mått når ett angivet värde. 

## <a name="batch-metrics"></a>Batch-mått

Mått är Azure-telemetridata (kallas även prestanda räknare) som genereras av dina Azure-resurser som används av tjänsten Azure Monitor. Exempel på mått i ett batch-konto är: pool skapa händelser, antal noder med låg prioritet och aktivitet slutförda händelser. 

Se [listan över de batch-mått som stöds](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Mått är:

* Aktive rad som standard i varje batch-konto utan ytterligare konfiguration
* Genereras var 1 minut
* Sparas inte automatiskt, men har en rullande historik på 30 dagar. Du kan spara aktivitets mått som en del av diagnostisk loggning.

### <a name="view-metrics"></a>Visa mått

Visa mått för batch-kontot i Azure Portal. På sidan **Översikt** för kontot visas som standard nyckel nod, kärna och aktivitets mått. 

Visa alla batch-konto mått: 

1. I portalen klickar du på **alla tjänster** > **batch-konton**och sedan på namnet på batch-kontot.
2. Klicka på **mått**under **övervakning**.
3. Välj en eller flera av måtten. Om du vill kan du välja ytterligare resurs mått med hjälp av list rutorna **prenumerationer**, **resurs grupp**, **resurs typ**och **resurs** .
    * Använd "genomsnittlig" agg regering för beräknings mått (t. ex. "dedikerat antal" eller "antal med låg prioritet"). Använd agg regeringen "count" för händelsebaserade mått (t. ex. att ändra storlek på slutförda händelser för pool).

> [!WARNING]
> Använd inte agg regeringen "sum", som lägger till värdena för alla data punkter som tas emot under perioden i diagrammet
> 
> 

    ![Batch metrics](media/batch-diagnostics/metrics-portal.png)

Använd Azure Monitor API: er för att hämta mått program mässigt. Se till exempel [hämta Azure Monitor mått med .net](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-reliability"></a>Batch-måttets tillförlitlighet

Mått är avsedda att användas för trend-och data analys. Mått leveransen är inte garanterad och omfattas inte av leverans i turordning, data förlust och/eller duplicering. Det rekommenderas inte att använda enskilda händelser för att varna eller utlösa funktioner. Mer information om hur du anger tröskelvärden för aviseringar finns i avsnittet [batch-mått aviseringar](#batch-metric-alerts) .

De värden som har spridits under de senaste 3 minuterna kan fortfarande aggregeras. Under den här tids ramen kan värdena för värden underhållas.

## <a name="batch-metric-alerts"></a>Batch Metric-aviseringar

Alternativt kan du konfigurera mått för nära real tids *aviseringar* som utlöses när värdet för ett angivet mått korsar ett tröskelvärde som du tilldelar. Aviseringen genererar ett [meddelande](../monitoring-and-diagnostics/insights-alerts-portal.md) som du väljer när aviseringen är "aktive rad" (när tröskelvärdet överskrids och varnings villkoret är uppfyllt) samt när det är "löst" (när tröskelvärdet överskrids igen och villkoret inte längre uppfylls). Aviseringar som baseras på enskilda data punkter rekommenderas inte eftersom måtten är beroende av leverans, data förlust och/eller duplicering. Aviseringar bör använda tröskelvärden för att ta hänsyn till dessa inkonsekvenser.

Du kanske t. ex. vill konfigurera en måtta avisering när antalet låg prioritets kärnor är en viss nivå, så att du kan justera sammansättningarna för dina pooler. Vi rekommenderar att du anger en period på 10 eller mer minuter där varningar utlöses om det genomsnittliga antalet låg prioritets kärnor sjunker under tröskelvärdet för hela perioden. Vi rekommenderar inte att du meddelar en 1-5-minuters period eftersom måtten fortfarande kan aggregeras.

Så här konfigurerar du en mått avisering i portalen:

1. Klicka på **alla tjänster** > **batch-konton**och sedan på namnet på batch-kontot.
2. Klicka på **aviserings regler** > **Lägg till mått avisering**under **övervakning**.
3. Välj ett mått, ett varnings villkor (till exempel när ett mått överskrider ett visst värde under en period) och en eller flera aviseringar.

Du kan också konfigurera en nästan real tids avisering med hjälp av [REST API](https://docs.microsoft.com/rest/api/monitor/). Mer information finns i [Översikt över aviseringar](../azure-monitor/platform/alerts-overview.md). Om du vill inkludera jobb, uppgift eller leverantörsspecifik information i dina aviseringar kan du läsa informationen om Sök frågor i [svara på händelser med Azure Monitor aviseringar](../azure-monitor/learn/tutorial-response.md)

## <a name="batch-diagnostics"></a>Batch-diagnostik

Diagnostikloggar innehåller information som släpps av Azure-resurser som beskriver driften av varje resurs. För batch kan du samla in följande loggar:

* **Tjänst loggar** händelser som genereras av tjänsten Azure Batch under en enskild batch-resurss livs längd, till exempel en pool eller uppgift. 

* **Mått** loggar på konto nivå. 

Inställningar för att aktivera insamling av diagnostikloggar är inte aktiverade som standard. Aktivera diagnostiska inställningar uttryckligen för varje batch-konto som du vill övervaka.

### <a name="log-destinations"></a>Logg destinationer

Ett vanligt scenario är att välja ett Azure Storage konto som mål för loggen. Om du vill lagra loggar i Azure Storage skapar du kontot innan du aktiverar samling av loggar. Om du har kopplat ett lagrings konto till ditt batch-konto kan du välja det kontot som mål för loggen. 

Andra valfria destinationer för diagnostikloggar:

* Strömma logg händelser för batch-diagnostikloggar till en [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Event Hubs kan mata in miljon tals händelser per sekund, som du sedan kan omvandla och lagra med valfri analys-Provider i real tid. 

* Skicka diagnostikloggar till [Azure Monitor loggar](../log-analytics/log-analytics-overview.md)där du kan analysera dem eller exportera dem för analys i Power BI eller Excel.

> [!NOTE]
> Du kan debiteras ytterligare kostnader för att lagra eller bearbeta diagnostikdata med Azure-tjänster. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Aktivera insamling av batch-diagnostikloggar

1. I portalen klickar du på **alla tjänster** > **batch-konton**och sedan på namnet på batch-kontot.
2. Under **övervakning**klickar du på **diagnostikloggar** > **Aktivera diagnostik**.
3. I **diagnostikinställningar**anger du ett namn för inställningen och väljer ett mål för loggen (befintligt lagrings konto, Event Hub eller Azure Monitor loggar). Välj antingen eller både **ServiceLog** och **AllMetrics**.

    När du väljer ett lagrings konto kan du ange en bevarande princip. Om du inte anger ett antal dagar för kvarhållning behålls data under lagrings kontots livs längd.

4. Klicka på **Spara**.

    ![Batch-diagnostik](media/batch-diagnostics/diagnostics-portal.png)

Andra alternativ för att aktivera logg insamling är: Använd Azure Monitor i portalen för att konfigurera diagnostikinställningar, använda en [Resource Manager-mall](../azure-monitor/platform/diagnostic-settings-template.md)eller använda Azure PowerShell eller Azure CLI. Se [samla in och använda loggdata från dina Azure-resurser](../azure-monitor/platform/platform-logs-overview.md).


### <a name="access-diagnostics-logs-in-storage"></a>Åtkomst till diagnostikloggar i Storage

Om du arkiverar batch-diagnostikloggar i ett lagrings konto skapas en lagrings behållare i lagrings kontot så snart en relaterad händelse inträffar. Blobbar skapas enligt följande namn mönster:

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
Varje `PT1H.json` BLOB-fil innehåller JSON-formaterade händelser som inträffat inom den timme som anges i BLOB- `h=12`URL: en (till exempel). Under den aktuella timmen läggs händelser till i `PT1H.json` filen när de inträffar. Minut värdet (`m=00`) är alltid `00`, eftersom diagnostiska logg händelser bryts till enskilda blobbar per timme. (Alla tider är i UTC-tid.)

Nedan visas ett exempel på en `PoolResizeCompleteEvent` post i en `PT1H.json` loggfil. Den innehåller information om aktuella och mål för dedikerade och låg prioritets noder, samt start-och slut tid för åtgärden:

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Mer information om schemat för diagnostikloggar i lagrings kontot finns i [arkivera Azure-diagnostikloggar](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-platform-logs-in-storage-account). Använd Storage-API: er för att komma åt loggarna i ditt lagrings konto program mässigt. 

### <a name="service-log-events"></a>Tjänst logg händelser
Azure Batch tjänst loggar, om de samlas in, innehåller händelser som avsänts av tjänsten Azure Batch under en enskild batch-resurs som en pool eller aktivitet. Varje händelse som släpps av batch är inloggad i JSON-format. Detta är till exempel bröd texten i en exempel händelse för att **skapa en pool**:

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

Batch-tjänsten genererar för närvarande följande tjänst logg händelser. Den här listan kanske inte är fullständig eftersom ytterligare händelser kan ha lagts till sedan den här artikeln senast uppdaterades.

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
