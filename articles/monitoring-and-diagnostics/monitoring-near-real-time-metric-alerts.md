---
title: De resurser som stöds för måttaviseringar i Azure Monitor
description: Referens för support mått och måttaviseringar i Azure Monitor-loggar
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: 102f087f31aa304aaaee49e0e39ff8c6874127b4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966808"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>De resurser som stöds för måttaviseringar i Azure Monitor

Azure Monitor nu stöder en [nya måttaviseringstypen](monitoring-overview-unified-alerts.md) som har betydande fördelar över den äldre [klassiska måttaviseringar](insights-alerts-portal.md). Mått är tillgängliga för [lång lista med Azure-tjänster](monitoring-supported-metrics.md). En (växande) delmängd av resurstyperna som har stöd för nyare aviseringar. Den här artikeln innehåller dessa användare.

Du kan också använda nyare måttaviseringar på den populära Log Analytics loggar extraherade som mått som en del av mått från loggar 
- [Prestandaräknare](../log-analytics/log-analytics-data-sources-performance-counters.md) för Windows och Linux-datorer
- [Pulsslagsposter för Agenthälsa](../operations-management-suite/oms-solution-agenthealth.md)
- [Hantering av uppdateringar](../operations-management-suite/oms-solution-update-management.md) poster
- [Händelsedata](../log-analytics/log-analytics-data-sources-windows-events.md) loggar
 
> [!NOTE]
> Specifika mått och/eller dimension endast visas om det finns data för den valda perioden. De här måtten är tillgängliga för kunder med Azure Log Analytics-arbetsytor i östra USA, västra centrala USA och Västeuropa. Mått från Log Analytics är för närvarande i offentlig förhandsversion och kan komma att ändras.

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, CLI, REST-stöd
För närvarande kan du kan skapa nyare måttaviseringar endast i Azure-portalen [REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/createorupdate) eller [Resource Manager-mallar](monitoring-create-metric-alerts-with-templates.md). Stöd för att konfigurera nyare aviseringar med hjälp av PowerShell och Azure CLI version 2.0 och senare kommer snart.

## <a name="metrics-and-dimensions-supported"></a>Mått och dimensioner som stöds
Nyare måttaviseringar stöd för aviseringar för mått med dimensioner. Du kan använda dimensioner för att filtrera dina mått till rätt nivå. Alla mått som stöds för tillsammans med tillämpliga dimensioner kan utforskas och visualiseras från [Azure Monitor - Metrics Explorer (förhandsversion)](monitoring-metric-charts.md).

Här är en fullständig lista över Azure monitor mått källor som stöds av de nyare aviseringarna:

|Resurstyp  |Dimensioner som stöds  | Tillgängliga mått|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Ja        | [API Management](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Ja   | [Automation-konton](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | Gäller inte| [Batch-konton](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    Gäller inte     |[Redis Cache](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.CognitiveServices/accounts     |    Gäller inte     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines     |    Gäller inte     | [Virtual Machines](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   Gäller inte      |[VM-skalningsuppsättningar](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Ja| [Behållargrupper](monitoring-supported-metrics.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Ja | [Hanterade kluster](monitoring-supported-metrics.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataFactory/datafactories| Ja| [Datafabriker V1](monitoring-supported-metrics.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   Ja     |[Datafabriker V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   Gäller inte      |[Databas för MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    Gäller inte     | [Databas för PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Ja      |[Event Hubs](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Nej | [Valv](monitoring-supported-metrics.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     Gäller inte    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    Gäller inte     | [Programgatewayer](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/expressRouteCircuits | Gäller inte |  [Express Route-kretsar](monitoring-supported-metrics.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/dnsZones | Gäller inte| [DNS-zoner](monitoring-supported-metrics.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers (endast för Standard-SKU: er)| Ja| [Belastningsutjämnare](monitoring-supported-metrics.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  Gäller inte       |[Offentlig IP-adress Addreses](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | Gäller inte | [Kapaciteter](monitoring-supported-metrics.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Network/trafficManagerProfiles | Ja | [Traffic Manager-profiler](monitoring-supported-metrics.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.Search/searchServices     |   Gäller inte      |[Söktjänster](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Ja       |[Service Bus](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Ja     | [Lagringskonton](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Ja    | [BLOB-tjänster](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [Filtjänster](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [kö tjänster](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) och [tabellen tjänster](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  Gäller inte       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
| Microsoft.Web/serverfarms | Ja | [App Service-planer](monitoring-supported-metrics.md#microsoftwebserverfarms)  |
| Microsoft.Web/sites | Ja | [App Services](monitoring-supported-metrics.md#microsoftwebsites-excluding-functions) och [funktioner](monitoring-supported-metrics.md#microsoftwebsites-functions)|
| Microsoft.Web/sites/slots | Ja | [App Service-fack](monitoring-supported-metrics.md#microsoftwebsitesslots)|
|Microsoft.OperationalInsights/workspaces| Ja|[Log Analytics-arbetsytor](monitoring-supported-metrics.md#microsoftoperationalinsightsworkspaces)|



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
