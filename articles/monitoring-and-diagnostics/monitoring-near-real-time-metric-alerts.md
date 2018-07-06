---
title: Stöds resurser för nyare måttaviseringar i Azure Monitor
description: Referens support mått och loggar för nyare Azure aviseringar i nära realtid mått.
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: 01c0b5897ab47a2a5091646aed1977779cf0234c
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868039"
---
# <a name="supported-metrics-and-creation-methods-for-new-metric-alerts"></a>Metoder som stöds mått och skapa nya mått aviseringar
Azure Monitor nu stöder en [nya måttaviseringstypen](monitoring-overview-unified-alerts.md) som har betydande fördelar över den äldre [klassiska måttaviseringar](insights-alerts-portal.md). Stöd för äldre aviseringarna en [lång lista med mått](monitoring-supported-metrics.md). En (växande) delmängd av större listan har stöd för nyare aviseringar. Den här artikeln innehåller dessa användare. 

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, CLI, REST-stöd
För närvarande kan du kan skapa nyare måttaviseringar endast i Azure-portalen [REST API](https://docs.microsoft.com/en-us/rest/api/monitor/metricalerts/createorupdate) eller [Resource Manager-mallar](monitoring-create-metric-alerts-with-templates.md). Stöd för att konfigurera nyare aviseringar med PowerShell och kommandoradsgränssnittet (Azure CLI 2.0) kommer snart.

## <a name="metrics-and-dimensions-supported"></a>Mått och dimensioner som stöds
Nyare måttaviseringar stöd för aviseringar för mått med dimensioner. Du kan använda dimensioner för att filtrera dina mått till rätt nivå. Alla mått som stöds för tillsammans med tillämpliga dimensioner kan utforskas och visualiseras från [Azure Monitor - Metrics Explorer (förhandsversion)](monitoring-metric-charts.md).

Här är en fullständig lista över Azure monitor mått källor som stöds av de nyare aviseringarna:

|Resurstyp  |Dimensioner som stöds  | Tillgängliga mått|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Ja        | [API Management](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Ja   | [Automation-konton](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | Gäller inte| [Batch-konton](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    Gäller inte     |[Redis Cache](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    Gäller inte     | [Virtual Machines](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   Gäller inte      |[VM-skalningsuppsättningar](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Ja| [Behållargrupper](monitoring-supported-metrics.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.DataFactory/datafactories| Ja| [Datafabriker V1](monitoring-supported-metrics.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   Ja     |[Datafabriker V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   Gäller inte      |[Databas för MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    Gäller inte     | [Databas för PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Ja      |[Event Hubs](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Nej | [Valv](monitoring-supported-metrics.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     Gäller inte    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    Gäller inte     | [Programgatewayer](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/dnsZones | Gäller inte| [DNS-zoner](monitoring-supported-metrics.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers (endast för Standard-SKU: er)| Ja| [Belastningsutjämnare](monitoring-supported-metrics.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  Gäller inte       |[Offentlig IP-adress Addreses](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | Gäller inte | [Kapaciteter](monitoring-supported-metrics.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices     |   Gäller inte      |[Söktjänster](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Ja       |[Service Bus](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Ja     | [Lagringskonton](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Ja    | [BLOB-tjänster](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [Filtjänster](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [kö tjänster](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) och [tabellen tjänster](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  Gäller inte       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    Gäller inte     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces (förhandsversion) | Ja|[Log Analytics-arbetsytor](#log-analytics-logs-as-metrics-for-alerting)|


## <a name="log-analytics-logs-as-metrics-for-alerting"></a>Log Analytics-loggar som mått för aviseringar 

Du kan också använda nyare måttaviseringar på den populära Log Analytics loggar extraherade som mått som en del av mått från aktivitetsloggar i förhandsutgåvan.  
- [Prestandaräknare](../log-analytics/log-analytics-data-sources-performance-counters.md) för Windows och Linux-datorer
- [Pulsslagsposter för Agenthälsa](../operations-management-suite/oms-solution-agenthealth.md)
- [Hantering av uppdateringar](../operations-management-suite/oms-solution-update-management.md) poster
 
> [!NOTE]
> Specifika mått och/eller dimension endast visas om det finns data för den valda perioden. De här måtten är tillgängliga för kunder med arbetsytor i östra USA, västra centrala USA och Europa, västra som har valts i förhandsversionen. Om du vill ska vara en del av den här förhandsversionen kan registrera dig med [undersökningen](https://aka.ms/MetricLogPreview).

Följande lista med Log Analytics loggbaserade mått källor stöds:

Tjänstmåttets namn/uppgifter  |Dimensioner som stöds  | Typ av logg  |
|---------|---------|---------|
|Average_Avg. S/diskläsning     |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Windows-prestandaräknare      |
| Average_Avg. S/diskskrivning     |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Windows-prestandaräknare      |
| Average_Current diskkölängd   |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Windows-prestandaräknare      |
| Average_Disk Diskläsningar/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Windows-prestandaräknare      |
| Average_Disk disköverföringar/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Windows-prestandaräknare      |
|   Average_ % ledigt utrymme    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Windows-prestandaräknare      |
| Average_Available megabyte     |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Windows-prestandaräknare      |
| Average_ % allokerade byte som används    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Windows-prestandaräknare      |
| Average_Bytes per sekund    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Windows-prestandaräknare      |
|  Average_Bytes/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Windows-prestandaräknare      |
|  Average_Bytes totalt/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Windows-prestandaräknare      |
|  Average_ % processortid    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Windows-prestandaräknare      |
|   Average_Processor Kölängd    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Windows-prestandaräknare      |
|   Average_ % Ledigai   |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_ % ledigt utrymme   |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_ % Användai-noder  |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Använt utrymme i procent för Average_   |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Disk lästa byte/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Disk Diskläsningar/sek |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Disk disköverföringar/sek |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Disk skrivna byte/sek   |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Disk Diskskrivningar/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Free megabyte |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Logical Disk byte/sek |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_ tillgängligt minne i procent |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Tillgängligt växlingsutrymme i Average_ % |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Använt minne i procent för Average_  |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_ % använt växlingsutrymme  |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Available minne i megabyte    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Available megabyte växlingsutrymme  |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Page Diskläsningar/sek |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Page Diskskrivningar/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Pages/sek  |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Used megabyte växlingsutrymme i procent |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Used minne i megabyte |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Överförda Average_Total byte    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Total byte som tagits emot   |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Total byte    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Total antal skickade paket  |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Total paket som tas emot |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Total Rx fel    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Total Tx-fel    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Total kollisioner   |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Avg. S/diskläsning |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Avg. S/disköverföring |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Avg. S/diskskrivning    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Physical Disk byte/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Privilegierad tid i Average_Pct    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Pct användartid  |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Used minne kB |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Virtual delat minne  |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_ % DPC-tid |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_ inaktivitetstid i procent    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_ avbrottstid i procent   |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_ % / o-väntetid |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_ Nice Time    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Privilegierad tid i procent för Average_  |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_ % processortid   |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_ Användartid i procent    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Free fysiskt minne   |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Free utrymme i växlingsfiler |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Free virtuellt minne    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Processes  |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Size lagrad i växlingsfiler    |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Uptime |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Average_Users  |     Ja - dator, objektnamn, InstanceName, räknarsökväg och SourceSystem    |   Prestandaräknare för Linux      |
|    Pulsslag  |     Ja - dator, OSType, Version och SourceComputerId    |   Pulsslagsposter |
|    Uppdatering |     Ja - dator, produkt, klassificering, UpdateState valfritt & godkända    |   Uppdateringshantering |



## <a name="payload-schema"></a>Nyttolast-schema

POST-åtgärd innehåller följande JSON-nyttolast och schemat för alla nära nyare måttaviseringar när en korrekt konfigurerad [åtgärdsgrupp](monitoring-action-groups.md) används:

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
* Lär dig mer om [loggaviseringar i Azure](monitor-alerts-unified-log.md).
* Lär dig mer om [aviseringar i Azure](monitoring-overview-alerts.md).
