---
title: Mått, aviseringar och diagnostikloggar för Azure Batch | Microsoft Docs
description: Registrera och analysera diagnostiklogg för resurser som pooler och uppgifter i Azure Batch-kontot.
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
ms.openlocfilehash: 54034b9a851fc6f06f97be9cfd5f261465bad455
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248270"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batch-mått, aviseringar och loggar för diagnostisk utvärdering och övervakning

Den här artikeln förklarar hur du övervakar ett Batch-konto med hjälp av funktionerna i [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Azure Monitor samlar in [mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md) och [diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) för resurser i Batch-kontot. Samla in och använda dessa data i en mängd olika sätt att övervaka ditt Batch-konto och diagnostisera problem. Du kan också konfigurera [måttaviseringar](../monitoring-and-diagnostics/monitoring-overview-alerts.md#alerts-on-azure-monitor-data) så att du att få meddelanden när ett mått överskrider ett angivet värde. 

## <a name="batch-metrics"></a>Batch-mått

Mått är Azure telemetridata (kallas även prestandaräknare) skickas från dina Azure-resurser som förbrukas av Azure Monitor-tjänsten. Inkludera exempel mått i ett Batch-konto: Pool skapa händelser, med låg prioritet Nodantal och uppgiften klar händelser. 

Se den [lista över mått som stöds Batch](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftbatchbatchaccounts).

Mått är:

* Aktiverat som standard i varje Batch-konto utan ytterligare konfiguration
* Genereras varje minut
* Beständiga inte automatiskt, men har en rullande 30-dagars historik. Du kan även spara aktivitetsmått som en del av [diagnostikloggning](#work-with-diagnostic-logs).

### <a name="view-metrics"></a>Visa mått

Visa mått för ditt Batch-konto i Azure-portalen. Den **översikt** sidan för kontot som standard visar viktiga nod, core och statistik för uppgiften. 

Visa alla mått för Batch-konto: 

1. I portalen klickar du på **alla tjänster** > **Batch-konton**, och klicka sedan på namnet på ditt Batch-konto.
2. Under **övervakning**, klickar du på **mått**.
3. Välj en eller flera av mått. Om du vill välja ytterligare Resursmått med hjälp av den **prenumerationer**, **resursgrupp**, **resurstyp**, och **Resource** listrutor.

    ![Batch-mått](media/batch-diagnostics/metrics-portal.png)

Hämta mätvärden via programmering genom att använda API: er för Azure Monitor. Se exempelvis [hämta Azure Monitor-mått med .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-alerts"></a>Måttaviseringar för batch

Alternativt kan du konfigurera nära realtid *måttaviseringar* som utlöser när värdet för ett visst mått överskrider ett tröskelvärde som du tilldelar. Aviseringen genererar en [meddelande](../monitoring-and-diagnostics/insights-alerts-portal.md) du väljer när aviseringen är ”aktiverad” (när tröskelvärdet skärs och aviseringstillståndet uppfylls), samt när det är ”löst” (när tröskelvärdet skärs igen och villkoret är ingen längre uppfyllt). 

Du kan till exempel vill konfigurera en metrisk varning när med låg prioritet core beräkningen faller på en viss nivå så att du kan justera sammansättning av dina pooler.

Konfigurera en metrisk varning i portalen:

1. Klicka på **Alla tjänster** > **Batch-konton** och sedan på namnet på Batch-kontot.
2. Under **övervakning**, klickar du på **Aviseringsregler** > **Lägg till metrisk varning**.
3. Välj ett mått, en varningsvillkor (till exempel när ett mått överskrider ett visst värde under en period) och en eller flera meddelanden.

Du kan också konfigurera en nästan i realtid aviseringen med hjälp av den [REST API](). Mer information finns i [använda nyare måttaviseringar för Azure-tjänster i Azure-portalen](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md)
## <a name="batch-diagnostics"></a>Batch-diagnostik

Diagnostikloggar innehåller information som genereras av Azure-resurser som beskrivs åtgärden för varje resurs. För Batch, kan du samla in följande loggar:

* **Tjänstloggar** händelser som genereras av Azure Batch-tjänsten under livslängden för en enskild Batch-resurs som en pool eller uppgift. 

* **Mått** loggar på kontonivå. 

Inställningar för att aktivera insamling av diagnostiska loggar är inte aktiverade som standard. Uttryckligen aktivera diagnostikinställningar för varje Batch-konto som du vill övervaka.

### <a name="log-destinations"></a>Log mål

Ett vanligt scenario är att välja ett Azure Storage-konto som destination log. För att lagra loggar i Azure Storage, skapa kontot innan du aktiverar insamling av loggar. Om du har associerat ett storage-konto med ditt Batch-konto, kan du välja det kontot som log-mål. 

Andra valfritt mål för diagnostikloggar:

* Stream Batch diagnostiklogg händelser till en [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Event Hubs kan mata in miljontals händelser per sekund, vilket du kan omvandla och lagra med hjälp av valfri leverantör av realtidsanalys. 

* Skicka diagnostikloggar till [Azure Log Analytics](../log-analytics/log-analytics-overview.md), där du kan analysera dem i Operations Management Suite (OMS)-portalen eller exportera dem för analys i Power BI eller Excel.

> [!NOTE]
> Du kan medföra ytterligare kostnader för att lagra eller bearbeta diagnostiklogg data med Azure-tjänster. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Aktivera insamling av Batch-diagnostikloggar

1. I portalen klickar du på **alla tjänster** > **Batch-konton**, och klicka sedan på namnet på ditt Batch-konto.
2. Under **övervakning**, klickar du på **diagnostikloggar** > **slå på diagnostik**.
3. I **diagnostikinställningar**, ange ett namn för inställningen och väljer en loggmålet (befintliga Storage-konto, Event Hub eller Log Analytics). Välj ett eller båda **ServiceLog** och **AllMetrics**.

    När du väljer ett lagringskonto om du vill ange en bevarandeprincip. Om du inte anger ett antal dagar för kvarhållning, bevaras under din arbetsgrens livstid storage-konto.

4. Klicka på **Spara**.

    ![Batch-diagnostik](media/batch-diagnostics/diagnostics-portal.png)

Andra alternativ för att aktivera insamling av supportloggar omfattar: använda Azure Monitor i portalen för att konfigurera diagnostikinställningar ska du använda en [Resource Manager-mall](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md), eller Använd Azure PowerShell eller Azure CLI. Se [samla in och använda loggdata från resurserna i Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs).


### <a name="access-diagnostics-logs-in-storage"></a>Diagnostisk loggar i storage

Om du arkiverar Batch-diagnostikloggar i ett lagringskonto skapas en lagringsbehållare i lagringskontot när en relaterade händelse inträffar. BLOB-objekt skapas enligt följande namngivningsmönstret:

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
Varje PT1H.json-blob-fil innehåller JSON-formaterade händelser som inträffade inom den angivna timmen i blob-URL (till exempel h = 12). Under den aktuella timmen läggs händelser till i filen PT1H.json allt eftersom de inträffar. Minutvärdet (m = 00) är alltid 00, eftersom diagnostiska logghändelser delas upp i enskilda blobar per timme. (Hela tiden är i UTC.)


Mer information om schemat för diagnostikloggar i storage-konto finns i [Arkivera Azure-diagnostikloggar](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

Använda Storage-API: er för att komma åt loggarna i ditt storage-konto via programmering. 

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
