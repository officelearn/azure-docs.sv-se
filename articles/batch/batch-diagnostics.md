---
title: Mått, aviseringar och diagnostikloggar – Azure Batch | Microsoft-dokument
description: Registrera och analysera diagnostiklogghändelser för Azure Batch-kontoresurser som pooler och uppgifter.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254849"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batchmått, aviseringar och loggar för diagnostikutvärdering och övervakning

 
I den här artikeln beskrivs hur du övervakar ett batchkonto med hjälp av funktioner i [Azure Monitor](../azure-monitor/overview.md). Azure Monitor samlar in [mått](../azure-monitor/platform/data-platform-metrics.md) och [diagnostikloggar](../azure-monitor/platform/platform-logs-overview.md) för resurser i ditt batchkonto. Samla in och använda dessa data på en mängd olika sätt för att övervaka ditt batchkonto och diagnostisera problem. Du kan också konfigurera [måttaviseringar](../azure-monitor/platform/alerts-overview.md) så att du får meddelanden när ett mått når ett angivet värde. 

## <a name="batch-metrics"></a>Batchmått

Mått är Azure telemetridata (kallas även prestandaräknare) som avges av dina Azure-resurser som förbrukas av Azure Monitor-tjänsten. Exempelmått i ett batchkonto är: Pool Skapa händelser, antal nodnoder med låg prioritet och slutförda händelser för aktivitet. 

Se [listan över batchmått som stöds](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Mått är:

* Aktiverad som standard i varje batchkonto utan ytterligare konfiguration
* Genereras var 1 minut
* Inte kvarstod automatiskt, men har en rullande 30-dagars historik. Du kan spara aktivitetsmått som en del av diagnostikloggning.

### <a name="view-metrics"></a>Visa mått

Visa mått för ditt batchkonto i Azure-portalen. Sidan **Översikt** för kontot visar som standard nyckelnod, kärna och uppgiftsmått. 

Så här visar du alla batchkontomått: 

1. Klicka på **Alla tjänster** > **Batch-konton**i portalen och klicka sedan på namnet på ditt batchkonto.
2. Klicka på **Mått**under **Övervakning**.
3. Välj ett eller flera av måtten. Om du vill kan du välja ytterligare resursmått med hjälp av **listrutan Prenumerationer,** **Resurs,** **Resurstyp**och **Resurs.**
    * För räknebaserade mått (som "Dedikerad kärnantal" eller "Antal nod med låg prioritet" använder du aggregeringen "Medel". För händelsebaserade mått (som "Pool Ändra storlek på slutförda händelser" använder du aggregeringen "Antal".

> [!WARNING]
> Använd inte aggregeringen "Summa", som summerar värdena för alla datapunkter som tas emot under diagramperioden
> 
> 

    ![Batch metrics](media/batch-diagnostics/metrics-portal.png)

Om du vill hämta mätvärden programmässigt använder du Azure Monitor API:er. Se till exempel [Hämta Azure Monitor-mått med .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-reliability"></a>Batch måtttillförlitlighet

Mått är avsedda att användas för trender och dataanalys. Måttleverans garanteras inte och är föremål för leverans utanför beställning, dataförlust och/eller dubbelarbete. Det rekommenderas inte att använda enskilda händelser för att avisera eller utlösa funktioner. Se avsnittet [Batch-måttaviseringar](#batch-metric-alerts) för mer information om hur du anger tröskelvärden för aviseringar.

Mått som avges under de senaste 3 minuterna kan fortfarande vara aggregerande. Under den här tidsperioden kan måttvärdena vara underrapporterade.

## <a name="batch-metric-alerts"></a>Batch måttaviseringar

Du kan också konfigurera *måttaviseringar* i nära realtid som utlöser när värdet för ett angivet mått överskrider ett tröskelvärde som du tilldelar. Aviseringen genererar ett [meddelande](../monitoring-and-diagnostics/insights-alerts-portal.md) som du väljer när aviseringen är "Aktiverad" (när tröskelvärdet överskrids och varningsvillkoret uppfylls) samt när det är "Löst" (när tröskelvärdet överskrids igen och villkoret inte längre uppfylls). Aviseringar baserat på enskilda datapunkter rekommenderas inte eftersom mått är föremål för leverans utanför beställning, dataförlust och/eller duplicering. Aviseringar bör använda tröskelvärden för att ta hänsyn till dessa inkonsekvenser.

Du kanske till exempel vill konfigurera en måttavisering när antalet kärnvärden med låg prioritet sjunker till en viss nivå, så att du kan justera sammansättningen av dina pooler. Vi rekommenderar att du ställer in en period på 10 eller fler minuter där aviseringar utlöses om det genomsnittliga kärnantalet med låg prioritet understiger tröskelvärdet för hela perioden. Det rekommenderas inte att varna på en 1-5 minuters period som mått kan fortfarande vara aggregera.

Så här konfigurerar du en måttavisering i portalen:

1. Klicka på **Alla tjänster** > **Batch-konton**och klicka sedan på namnet på ditt batchkonto.
2. Klicka på **Varningsregler** > **Lägg till måttavisering**under **Övervakning**.
3. Välj ett mått, ett varningsvillkor (till exempel när ett mått överskrider ett visst värde under en period) och ett eller flera meddelanden.

Du kan också konfigurera en varning i nära realtid med [REST API](https://docs.microsoft.com/rest/api/monitor/). Mer information finns i [Översikt över aviseringar](../azure-monitor/platform/alerts-overview.md). Om du vill inkludera jobb-, uppgifts- eller poolspecifik information i dina aviseringar läser du informationen om sökfrågor i [Svara på händelser med Azure Monitor-aviseringar](../azure-monitor/learn/tutorial-response.md)

## <a name="batch-diagnostics"></a>Batch-diagnostik

Diagnostikloggar innehåller information som avges av Azure-resurser som beskriver åtgärden för varje resurs. För Batch kan du samla in följande loggar:

* **Tjänstloggar** händelser som avges av Azure Batch-tjänsten under livstiden för en enskild batchresurs som en pool eller aktivitet. 

* **Måttloggar** på kontonivå. 

Inställningar för att aktivera insamling av diagnostikloggar är inte aktiverade som standard. Aktivera uttryckligen diagnostikinställningar för varje batchkonto som du vill övervaka.

### <a name="log-destinations"></a>Logga destinationer

Ett vanligt scenario är att välja ett Azure Storage-konto som loggmål. Om du vill lagra loggar i Azure Storage skapar du kontot innan du aktiverar insamling av loggar. Om du har kopplat ett lagringskonto till ditt Batch-konto kan du välja det kontot som loggmål. 

Andra valfria destinationer för diagnostikloggar:

* Strömma diagnostiklogghändelser för batch till en [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Event Hubs kan inta miljontals händelser per sekund, som du sedan kan omvandla och lagra med hjälp av alla analysleverantörer i realtid. 

* Skicka diagnostikloggar till [Azure Monitor-loggar](../log-analytics/log-analytics-overview.md), där du kan analysera dem eller exportera dem för analys i Power BI eller Excel.

> [!NOTE]
> Du kan ådra dig ytterligare kostnader för att lagra eller bearbeta diagnostikloggdata med Azure-tjänster. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Aktivera insamling av batchdiagnostiska loggar

1. Klicka på **Alla tjänster** > **Batch-konton**i portalen och klicka sedan på namnet på ditt batchkonto.
2. Klicka på **Diagnostikloggar** > **Aktivera diagnostik**under **Övervakning**.
3. I **Diagnostikinställningar**anger du ett namn för inställningen och väljer ett loggmål (befintliga lagringskonto-, eventnav- eller Azure Monitor-loggar). Välj antingen eller både **ServiceLog** och **AllMetrics**.

    När du väljer ett lagringskonto anger du eventuellt en bevarandeprincip. Om du inte anger ett antal dagar för kvarhållning sparas data under lagringskontots livslängd.

4. Klicka på **Spara**.

    ![Batch-diagnostik](media/batch-diagnostics/diagnostics-portal.png)

Andra alternativ för att aktivera loggsamling inkluderar: använd Azure Monitor i portalen för att konfigurera diagnostikinställningar, använda en [Resource Manager-mall](../azure-monitor/platform/diagnostic-settings-template.md)eller använda Azure PowerShell eller Azure CLI. se [Samla in och använda loggdata från dina Azure-resurser](../azure-monitor/platform/platform-logs-overview.md).


### <a name="access-diagnostics-logs-in-storage"></a>Komma åt diagnostikloggar i lagring

Om du arkiverar batchdiagnostikloggar i ett lagringskonto skapas en lagringsbehållare i lagringskontot så snart en relaterad händelse inträffar. Blobbar skapas enligt följande namngivningsmönster:

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
Varje `PT1H.json` blob-fil innehåller JSON-formaterade händelser som inträffade inom den timme som `h=12`anges i blob-URL:en (till exempel ). Under den aktuella timmen läggs händelser `PT1H.json` till i filen när de inträffar. Minutvärdet (`m=00`) `00`är alltid , eftersom diagnostiska logghändelser är uppdelade i enskilda blobbar per timme. (Alla tider är i UTC.)

Nedan följer ett `PoolResizeCompleteEvent` exempel på `PT1H.json` en post i en loggfil. Den innehåller information om det aktuella antalet dedikerade noder och noder med låg prioritet samt start- och sluttid för operationen:

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Mer information om schemat för diagnostikloggar i lagringskontot finns i [Arkivera Azure Diagnostic Logs](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-platform-logs-in-storage-account). Använd api:erna för lagrings-API:er för att komma åt loggarna i ditt lagringskonto programmässigt. 

### <a name="service-log-events"></a>Servicelogghändelser
Azure Batch Service Loggar, om de samlas in, innehåller händelser som avges av Azure Batch-tjänsten under livstiden för en enskild batchresurs som en pool eller aktivitet. Varje händelse som avges av Batch loggas i JSON-format. Det här är till exempel brödtexten i en **exempelpool skapa händelse:**

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

Batch-tjänsten avger för närvarande följande servicelogghändelser. Den här listan kanske inte är uttömmande, eftersom ytterligare händelser kan ha lagts till sedan den här artikeln senast uppdaterades.

| **Servicelogghändelser** |
| --- |
| [Skapa pool](batch-pool-create-event.md) |
| [Start av poolborttagning](batch-pool-delete-start-event.md) |
| [Borttagning av pool har slutförts](batch-pool-delete-complete-event.md) |
| [Starta om poolen](batch-pool-resize-start-event.md) |
| [Ändrar storlek på poolen färdigt](batch-pool-resize-complete-event.md) |
| [Start av uppgift](batch-task-start-event.md) |
| [Uppgiften har slutförts](batch-task-complete-event.md) |
| [Aktiviteten misslyckas](batch-task-fail-event.md) |



## <a name="next-steps"></a>Nästa steg

* Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.
* Läs mer om [övervakning av batchlösningar](monitoring-overview.md).
