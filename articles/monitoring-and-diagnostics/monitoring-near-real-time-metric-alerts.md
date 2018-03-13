---
title: "Nära realtid mått aviseringar i Azure-Monitor | Microsoft Docs"
description: "Lär dig hur du använder nära mått aviseringar i realtid för att övervaka Azure-resurs med en frekvens som är så liten som 1 minut."
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: snmuvva, vinagara
ms.custom: 
ms.openlocfilehash: 88995b1f3350fe485e28efccc93779ae0a42eb97
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>Nära realtid mått aviseringar (förhandsgranskning)
Azure-Monitor stöder en ny aviseringstyp kallas nära realtid mått aviseringar (förhandsversion). Den här funktionen är för närvarande i förhandsversion.

Nära realtid mått aviseringar skiljer sig från vanlig mått aviseringar på flera sätt:

- **Förbättrad latens**: nära realtid mått aviseringar kan övervaka ändringar i måttvärden med en frekvens som är så liten som en minut.
- **Mer kontroll över mått villkor**: du kan definiera bättre Varningsregler i nära realtid mått aviseringar. Aviseringarna stöd för övervakning maximala, minsta, genomsnittlig och totala värden för mått.
- **Mått från loggar**: från populära loggdata som kommer till [logganalys](../log-analytics/log-analytics-overview.md), mått kan extraheras till Azure-Monitor och få information om nästan i realtid
- **Kombinerade övervakning av flera mått**: nära realtid mått aviseringar kan du övervaka flera (för närvarande högst två mått) med en enskild regel. En varning aktiveras om båda mått bryta mot sina respektive tröskelvärden för den angivna tidsperioden.
- **Modulära meddelandesystem**: nära realtid mått aviseringar använder [åtgärdsgrupper](monitoring-action-groups.md). Du kan använda åtgärdsgrupper skapa modulära åtgärder. Du kan återanvända åtgärdsgrupper för flera Varningsregler.

> [!NOTE]
> Nära realtid mått är för närvarande i förhandsversion. Och mått för loggar funktioner finns i *begränsad* förhandsversion. Funktioner och användarupplevelse kan ändras.
>

## <a name="metrics-and-dimensions-supported"></a>Mått och dimensioner som stöds
Nära realtid mått stöder aviseringar aviseringar för mått som använder dimensioner. Du kan använda dimensioner för att filtrera dina mått för rätt nivå. Alla stöds mått tillsammans med tillämpliga dimensioner kan utforskade och visualiseras från [Azure-Monitor - Metrics Explorer (förhandsgranskning)](monitoring-metric-charts.md).

Här är en fullständig lista över Azure övervakaren baserat mått källor som stöds för nära realtid mått aviseringar:

|Mått Namnuppgifter  |Dimensioner som stöds  |
|---------|---------|
|Microsoft.ApiManagement/service     | Ja        |
|Microsoft.Automation/automationAccounts     |     Gäller inte    |
|Microsoft.Automation/automationAccounts     |   Gäller inte      |
|Microsoft.Cache/Redis     |    Gäller inte     |
|Microsoft.Compute/virtualMachines     |    Gäller inte     |
|Microsoft.Compute/virtualMachineScaleSets     |   Gäller inte      |
|Microsoft.DataFactory/factories     |   Gäller inte      |
|Microsoft.DBforMySQL/servers     |   Gäller inte      |
|Microsoft.DBforPostgreSQL/servers     |    Gäller inte     |
|Microsoft.EventHub/namespaces     |   Gäller inte      |
|Microsoft.Logic/workflows     |     Gäller inte    |
|Microsoft.Network/applicationGateways     |    Gäller inte     |
|Microsoft.Network/publicipaddresses     |  Gäller inte       |
|Microsoft.Search/searchServices     |   Gäller inte      |
|Microsoft.ServiceBus/namespaces     |  Gäller inte       |
|Microsoft.Storage/storageAccounts     |    Ja     |
|Microsoft.Storage/storageAccounts/services     |     Ja    |
|Microsoft.StreamAnalytics/streamingjobs     |  Gäller inte       |
|Microsoft.CognitiveServices/accounts     |    Gäller inte     |


Mått från loggar, stöder för närvarande följande populära OMS-loggar:
- [Prestandaräknare](../log-analytics/log-analytics-data-sources-performance-counters.md) för Windows och Linux-datorer
- Heartbeat-poster för datorer
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
> Specifikt mått och/eller dimension visas bara om det finns data för den valda perioden

## <a name="create-a-near-real-time-metric-alert"></a>Skapa en nära realtid mått
För närvarande kan du skapa nära realtid mått aviseringar endast i Azure-portalen. Stöd för att konfigurera nästan realtid mått aviseringar med hjälp av PowerShell, Azure-kommandoradsgränssnittet (Azure CLI) och Azure övervakaren REST API: er kommer snart.

Upplevelse för att skapa en nära realtid mått avisering har flyttats till den nya **aviseringar (förhandsgranskning)** sidan. Även om de aktuella aviseringarna sidan visar **lägga till nära realtid mått avisering**, du omdirigeras till den **aviseringar (förhandsgranskning)** sidan.

Information om hur du skapar en nära realtid mått avisering finns [skapa en aviseringsregel i Azure portal](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="manage-near-real-time-metric-alerts"></a>Hantera nära realtid mått aviseringar
När du har skapat en nära realtid mått avisering kan du hantera aviseringen med hjälp av stegen som beskrivs i [Hantera aviseringar i Azure portal](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

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

* Mer information om den nya [aviseringar (förhandsgranskning)-upplevelse](monitoring-overview-unified-alerts.md).
* Lär dig mer om [Logga varningar i Azure varningar (förhandsgranskning)](monitor-alerts-unified-log.md).
* Lär dig mer om [aviseringar i Azure](monitoring-overview-alerts.md).
