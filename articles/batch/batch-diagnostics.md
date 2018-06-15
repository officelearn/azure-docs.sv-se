---
title: Mått, aviseringar och diagnostikloggar för Azure Batch | Microsoft Docs
description: Registrera och analysera diagnostiska logghändelser för Azure Batch-kontot resurser som pooler och uppgifter.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 04/05/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: e64d272695c4e47c972df040d1c1c2a63bf3dddd
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31788202"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batch mått, aviseringar och loggar för diagnostik utvärdering och övervakning

Den här artikeln förklarar hur du övervakar ett Batch-konto med hjälp av funktioner i [Azure-Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Azure övervakaren samlar in [mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md) och [diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) för resurser i Batch-kontot. Samla in och använda dessa data i en mängd olika sätt att övervaka Batch-kontot och diagnostisera problem. Du kan också konfigurera [mått aviseringar](../monitoring-and-diagnostics/monitoring-overview-alerts.md#alerts-on-azure-monitor-data) så att du får meddelanden när ett mått når ett angivet värde. 

## <a name="batch-metrics"></a>Batch-mått

Mått är Azure telemetridata (kallas även prestandaräknare) från Azure-resurser som används av Azure-Monitor-tjänsten. Exempel exempel mått i ett Batch-konto är: poolen skapa händelser, antalet noder för låg prioritet och aktivitet slutförts händelser. 

Finns det [lista över stöds Batch mått](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftbatchbatchaccounts).

Mått är:

* Aktiverat som standard i varje Batch-kontot utan ytterligare konfiguration
* Genereras varje 1 minut
* Beständig inte automatiskt, men har en 30-dagars löpande historik. Du kan spara aktivitetsmått som en del av [diagnostikloggning](#work-with-diagnostic-logs).

### <a name="view-metrics"></a>Visa mått

Visa måtten för Batch-kontot i Azure-portalen. Den **översikt** sidan för kontot som standard visar viktiga nod, kärnor och uppgiften mått. 

Visa alla mätvärden för Batch-kontot: 

1. I portalen klickar du på **alla tjänster** > **Batch-konton**, och klicka sedan på namnet på Batch-kontot.
2. Under **övervakning**, klickar du på **mått**.
3. Välj en eller flera av måtten. Om du vill välja ytterligare mått med hjälp av den **prenumerationer**, **resursgruppen**, **resurstypen**, och **resurs** nedrullningsbara listorna.

    ![Batch-mått](media/batch-diagnostics/metrics-portal.png)

Hämta mätvärden via programmering genom att använda API: er för Azure-Monitor. Se exempelvis [hämta Azure-Monitor mått med .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-alerts"></a>Batch mått aviseringar

Alternativt kan du konfigurera nästan realtid *mått aviseringar* som utlöses när värdet för ett visst mått överskrider ett tröskelvärde som du tilldelar. Aviseringen genererar en [meddelande](../monitoring-and-diagnostics/insights-alerts-portal.md) du väljer när aviseringen är ”aktiverad” (när tröskelvärdet skärs och avisering villkoret är uppfyllt) samt när ”löses” (när tröskelvärdet skärs igen och villkoret är ingen längre uppfyllt). 

Du kanske vill konfigurera en avisering om mått när med låg prioritet core beräkningen sjunker till en viss nivå så att du kan justera sammansättningen av din pooler.

Konfigurera en avisering om mått i portalen:

1. Klicka på **Alla tjänster** > **Batch-konton** och sedan på namnet på Batch-kontot.
2. Under **övervakning**, klickar du på **Varna regler** > **Lägg till mått avisering**.
3. Markera ett mått, en varning villkor (till exempel när en måttet överskrider ett visst värde under en period) och ett eller flera meddelanden.

Du kan också konfigurera en nära realtid avisering med hjälp av den [REST API](). Mer information finns i [använder nyare mått aviseringar för Azure-tjänster i Azure-portalen](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md)
## <a name="batch-diagnostics"></a>Diagnostik för batch

Diagnostikloggar innehåller information som sänds av Azure-resurser som beskrivs åtgärden för varje resurs. För Batch, kan du samla in följande loggar:

* **Tjänstloggar** händelser som sänds av Azure Batch-tjänsten under livslängden för en enskild Batch-resurs som en pool eller aktiviteten. 

* **Mått** loggar på kontonivå. 

Inställningar för att aktivera insamling av diagnostikloggar är inte aktiverade som standard. Uttryckligen aktivera diagnostikinställningar för varje Batch-kontot som du vill övervaka.

### <a name="log-destinations"></a>Loggen mål

Ett vanligt scenario är att välja ett Azure Storage-konto som mål för loggen. Skapa kontot innan du aktiverar insamling av loggar för att lagra loggar i Azure Storage. Om du har associerat ett lagringskonto med Batch-kontot du kontot som mål för loggen. 

Andra valfria mål för diagnostikloggar:

* Strömma Batch diagnostiska logga händelser till en [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Händelsehubbar kan mata in miljontals händelser per sekund, vilket du kan sedan omvandla och lagra med hjälp av en leverantör av realtidsanalys. 

* Skicka diagnostikloggar till [Azure logganalys](../log-analytics/log-analytics-overview.md), där du kan analysera dem i portalen Operations Management Suite (OMS) eller exportera dem för analys i Power BI eller Excel.

> [!NOTE]
> Du kan innebära ytterligare kostnader för att lagra eller bearbeta diagnostiska loggdata med Azure-tjänster. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Aktivera insamling av Batch diagnostikloggar

1. I portalen klickar du på **alla tjänster** > **Batch-konton**, och klicka sedan på namnet på Batch-kontot.
2. Under **övervakning**, klickar du på **diagnostikloggar** > **aktivera diagnostiken**.
3. I **diagnostikinställningar**, ange ett namn för inställningen och välj en loggmålet (befintliga lagring konto, Event Hub eller logganalys). Välj ett eller båda **ServiceLog** och **AllMetrics**.

    När du väljer ett lagringskonto, om du vill ange en bevarandeprincip. Om du inte anger ett antal dagar för kvarhållning bevaras data under storage-konto.

4. Klicka på **Spara**.

    ![Diagnostik för batch](media/batch-diagnostics/diagnostics-portal.png)

Andra alternativ för att aktivera Logginsamling inkludera: använda Azure Övervakare i portalen för att konfigurera diagnostikinställningar använder en [Resource Manager-mall](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md), eller använda Azure PowerShell eller Azure CLI. Se [samla in och använda loggdata från resurserna i Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs).


### <a name="access-diagnostics-logs-in-storage"></a>Diagnostisk loggar in lagring

Om du arkiverar Batch diagnostikloggar i ett lagringskonto skapas en lagringsbehållare i lagringskontot som en relaterad händelse inträffar. Blobbar skapas enligt följande namngivningsmönstret:

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
Exempel:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
Varje PT1H.json blob-fil innehåller JSON-formaterad händelser som inträffade inom en timme som anges i blob-URL (till exempel h = 12). Under den aktuella timmen läggs händelser till i filen PT1H.json allt eftersom de inträffar. Minuten (m = 00) är alltid 00, eftersom diagnostiska logghändelser delas upp i enskilda blobbar per timme. (Hela tiden är i UTC.)


Mer information om schemat diagnostikloggar i storage-konto finns [Arkiv Azure diagnostikloggar](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

Använd API: erna för lagring för att komma åt loggarna i ditt lagringskonto via programmering. 

### <a name="service-log-events"></a>Tjänsten logghändelser
Azure Batch tjänstloggar om som samlas in, innehålla händelser som sänds av Azure Batch-tjänsten under livslängden för en enskild Batch-resurs som en pool eller aktiviteten. Varje händelse som orsakat av Batch loggas i JSON-format. Detta är till exempel brödtexten i ett exempel på en **pool Skapa händelse**:

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

Batch-tjänsten skickar för närvarande följande händelser i loggen för tjänsten. Den här listan får inte vara fullständig, eftersom ytterligare händelser kan ha lagts eftersom den här artikeln senast uppdaterades.

| **Tjänsten logghändelser** |
| --- |
| [Skapa poolen](batch-pool-create-event.md) |
| [Poolen delete start](batch-pool-delete-start-event.md) |
| [Poolen ta bort klar](batch-pool-delete-complete-event.md) |
| [Poolen storleksändring start](batch-pool-resize-start-event.md) |
| [Poolen storleksändring slutförd](batch-pool-resize-complete-event.md) |
| [Uppgiften start](batch-task-start-event.md) |
| [Uppgift slutförd](batch-task-complete-event.md) |
| [Aktiviteten misslyckas](batch-task-fail-event.md) |



## <a name="next-steps"></a>Nästa steg

* Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.
* Lär dig mer om [övervakningslösningar Batch](monitoring-overview.md).
