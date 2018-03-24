---
title: Nära realtid mått aviseringar i Azure-Monitor | Microsoft Docs
description: Lär dig hur du använder nära mått aviseringar i realtid för att övervaka Azure-resurs med en frekvens som är så liten som 1 minut.
author: snehithm
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: snmuvva, vinagara
ms.custom: ''
ms.openlocfilehash: 15b9b0b69f3805b3e3af1d3973fd3a77bea62ab9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="use-the-newer-metric-alerts-for-azure-services-in-azure-portal"></a>Använd de nyare mått aviseringarna för Azure-tjänster i Azure-portalen
Azure-Monitor stöder en ny aviseringstyp kallas nära realtid mått aviseringar. 

Nära realtid mått aviseringar som skiljer sig från [klassiska mått aviseringar](insights-alerts-portal.md) på flera sätt:

- **Förbättrad latens**: nära realtid mått aviseringar kan köras så ofta som var en minut. Äldre mått aviseringar körs alltid en frekvens på 5 minuter.
- **Stöd för flerdimensionella mått**: du kan meddela om dimensionell mått så att du kan övervaka en intressant segmentet i måttet.
- **Mer kontroll över mått villkor**: du kan definiera bättre Varningsregler i nära realtid mått aviseringar. Aviseringarna stöd för övervakning maximala, minsta, genomsnittlig och totala värden för mått.
- **Kombinerade övervakning av flera mått**: nära realtid mått aviseringar kan du övervaka flera (för närvarande högst två mått) med en enskild regel. En varning aktiveras om båda mått bryta mot sina respektive tröskelvärden för den angivna tidsperioden.
- **Modulära meddelandesystem**: nära realtid mått aviseringar använder [åtgärdsgrupper](monitoring-action-groups.md). Du kan använda åtgärdsgrupper skapa modulära åtgärder. Du kan återanvända åtgärdsgrupper för flera Varningsregler.
- **Mått från loggar**: från populära loggdata som kommer till [logganalys](../log-analytics/log-analytics-overview.md), mått kan extraheras till Azure-Monitor och få information om nästan i realtid.


## <a name="metrics-and-dimensions-supported"></a>Mått och dimensioner som stöds
Nära realtid mått stöder aviseringar aviseringar för mått som använder dimensioner. Du kan använda dimensioner för att filtrera dina mått för rätt nivå. Alla stöds mått tillsammans med tillämpliga dimensioner kan utforskade och visualiseras från [Azure-Monitor - Metrics Explorer (förhandsgranskning)](monitoring-metric-charts.md).

Här är en fullständig lista över Azure övervakaren baserat mått källor som stöds för nära realtid mått aviseringar:

|Resurstyp  |Dimensioner som stöds  | Tillgängliga mått|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Ja        | [API Management](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Ja   | [Automation-konton](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | Gäller inte| [Batch-konton](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    Gäller inte     |[Redis Cache](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    Gäller inte     | [Virtual Machines](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   Gäller inte      |[Skaluppsättningar för den virtuella datorn](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.DataFactory/factories     |   Ja     |[Data fabriker V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   Gäller inte      |[DB för MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    Gäller inte     | [DB för PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Ja      |[Event Hubs](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.Logic/workflows     |     Gäller inte    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    Gäller inte     | [Programgatewayer](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/publicipaddresses     |  Gäller inte       |[Offentliga IP-Addreses](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Search/searchServices     |   Gäller inte      |[Search-tjänster](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Ja       |[Service Bus](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Ja     | [Lagringskonton](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Ja    | [BLOB-tjänster](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [Filtjänster](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [kö Services](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) och [tabell tjänster](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  Gäller inte       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    Gäller inte     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces (förhandsgranskning) | Ja|[Log Analytics arbetsytor](#support-for-oms-logs-as-metrics-for-alerting)|


## <a name="create-a-newer-metric-alert"></a>Skapa en nyare mått
För närvarande kan du skapa nyare mått aviseringar endast i Azure portal eller REST API. Stöd för att konfigurera nästan realtid mått aviseringar med hjälp av PowerShell, Azure-kommandoradsgränssnittet (Azure CLI) kommer snart.

Information om hur du skapar en avisering om nyare mått i Azure-portalen finns [skapa en aviseringsregel i Azure portal](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="manage-newer-metric-alerts"></a>Hantera aviseringar för nyare mått
När du har skapat en nära realtid mått avisering kan du hantera aviseringen med hjälp av stegen som beskrivs i [Hantera aviseringar i Azure portal](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="support-for-oms-logs-as-metrics-for-alerting"></a>Stöd för OMS-loggar som mätvärden för aviseringar

Du kan också använda nära realtid mått aviseringar på populära OMS-loggarna extraherat som mått som en del av mätvärden från förhandsversionen av loggar.  
- [Prestandaräknare](../log-analytics/log-analytics-data-sources-performance-counters.md) för Windows och Linux-datorer
- [Heartbeat-poster för Agenthälsa](../operations-management-suite/oms-solution-agenthealth.md)
- [Uppdateringshantering](../operations-management-suite/oms-solution-update-management.md) poster

Här är en fullständig lista över OMS loggbaserade mått källor som stöds för nära realtid mått aviseringar:

Mått Namnuppgifter  |Dimensioner som stöds  | Typ av logg  |
|---------|---------|---------|
|Average_Avg. Disk sek/läsning     |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
| Average_Avg. Disk sek/skrivning     |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
| Average_Current Disk Queue Length   |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
| Average_Disk Reads/sec    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
| Average_Disk Transfers/sec    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
|   Average_ % ledigt utrymme    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
| Average_Available MBytes     |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
| Average_ % allokerade byte som används    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
| Average_Bytes/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
|  Average_Bytes Sent/sec    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
|  Average_Bytes totalt/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
|  Average_ % processortid    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
|   Average_Processor Queue Length    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
|   Average_ % Ledigai   |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ % ledigt utrymme   |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ används i procent  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Använt utrymme i procent för Average_   |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Disk lästa byte/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Disk Reads/sec |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Disk Transfers/sec |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Disk skrivna byte/s   |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Disk Diskskrivningar/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Free megabyte |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Logical Disk Bytes/sec |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ tillgängligt minne i procent |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Tillgängligt växlingsutrymme i Average_ % |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Använt minne i procent för Average_  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ % använt växlingsutrymme  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Available minne i megabyte    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Available megabyte växlingsutrymme  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Page Reads/sec |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Page Writes/sec    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Pages/sec  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Used megabyte växlingsutrymme |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Used minne i megabyte |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Total byte som överförs    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Total mottagna byte   |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Total byte    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Total antal skickade paket  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Total paket som tagits emot |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Total Rx fel    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Total Tx-fel    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Total kollisioner   |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Avg. Disk sek/läsning |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Avg. Disk sek/disköverföring |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Avg. Disk sek/skrivning    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Physical Disk byte/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Privilegierad tid i Average_Pct    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Användartid i Average_Pct  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Used minne Kbyte |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Virtual delat minne  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ % DPC-tid |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ inaktivitetstid i procent    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ avbrottstid i procent   |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ %-i/o-väntetid |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ Nice Time    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Privilegierad tid i procent för Average_  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ % processortid   |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ Användartid i procent    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Free fysiskt minne   |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Free utrymme i växlingsfiler |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Free virtuellt minne    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Processes  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Size lagrad i växlingsfiler    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Uptime |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Users  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Pulsslag  |     Yes - Computer, OSType, Version & SourceComputerId    |   Heartbeat-poster |
|    Uppdatering |     Ja - dator, produkt, klassificering, UpdateState valfria & godkända    |   Uppdateringshantering |

> [!NOTE]
> Specifikt mått och/eller dimension visas bara om det finns data för den valda perioden. De här måtten är tillgängliga för kunder med arbetsytor i östra USA, västra centrala USA och Västeuropa som har valts i förhandsgranskningen. Om du vill ska vara en del av den här förhandsgranskningen kan logga med [undersökningen](https://aka.ms/MetricLogPreview).


## <a name="payload-schema"></a>Nyttolasten i schemat

POST-åtgärden innehåller följande JSON-nyttolast och schemat för alla nära realtid mått aviseringar när korrekt konfigurerad [grupp](monitoring-action-groups.md) används:

```json
{"schemaId":"AzureMonitorMetricAlert","data":
    {
    "version": "2.0",
    "status": "Activated",
    "context": {
    "timestamp": "2018-02-28T10:44:10.1714014Z",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
    "name": "StorageCheck",
    "description": "",
    "conditionType": "SingleResourceMultipleMetricCriteria",
    "condition": {
      "windowSize": "PT5M",
      "allOf": [
        {
          "metricName": "Transactions",
          "dimensions": [
            {
              "name": "AccountResourceId",
              "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
            },
            {
              "name": "GeoType",
              "value": "Primary"
            }
          ],
          "operator": "GreaterThan",
          "threshold": "0",
          "timeAggregation": "PT5M",
          "metricValue": 1.0
        },
      ]
    },
    "subscriptionId": "00000000-0000-0000-0000-000000000000",
    "resourceGroupName": "Contoso",
    "resourceName": "diag500",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
    "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
  },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

* Mer information om den nya [aviseringar upplevelse](monitoring-overview-unified-alerts.md).
* Lär dig mer om [Logga varningar i Azure](monitor-alerts-unified-log.md).
* Lär dig mer om [aviseringar i Azure](monitoring-overview-alerts.md).
