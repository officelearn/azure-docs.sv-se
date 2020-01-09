---
title: Resurser som stöds för mått varningar i Azure Monitor
description: Referens för support mått och loggar för mått varningar i Azure Monitor
author: harelbr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: harelbr
ms.subservice: alerts
ms.openlocfilehash: 14dc7b33a82b490f005d9684e4c9cb76bd947a7c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75364500"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Resurser som stöds för mått varningar i Azure Monitor

Azure Monitor har nu stöd för en [ny mått varnings typ](../../azure-monitor/platform/alerts-overview.md) som har betydande fördelar jämfört med äldre [klassiska mått varningar](../../azure-monitor/platform/alerts-classic.overview.md). Mått är tillgängliga för [stor lista över Azure-tjänster](../../azure-monitor/platform/metrics-supported.md). De nya aviseringarna stöder en (växande) del av resurs typerna. Den här artikeln visar en delmängd.

Du kan också använda nya mått varningar på populära loggdata som lagras i en Log Analytics arbets yta som har extraherats som mått. Om du vill ha mer information kan du Visa [mått aviseringar för loggar](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, CLI, REST-support
För närvarande kan du bara skapa nya mått varningar i Azure Portal-, [REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/)-eller [Resource Manager-mallar](../../azure-monitor/platform/alerts-metric-create-templates.md). Stöd för att konfigurera nya aviseringar med PowerShell och Azure CLI version 2,0 och senare kommer snart.

## <a name="metrics-and-dimensions-supported"></a>Mått och dimensioner som stöds
Nya mått aviseringar stöder aviseringar för mått som använder dimensioner. Du kan använda dimensioner för att filtrera måttet till rätt nivå. Alla mått som stöds tillsammans med tillämpliga dimensioner kan utforskas och visualiseras från [Azure Monitor-Metrics Explorer](../../azure-monitor/platform/metrics-charts.md).

Här är en fullständig lista över Azure Monitors mått källor som stöds av de nya aviseringarna:

|Resurstyp  |Dimensioner som stöds |Aviseringar med flera resurser| Tillgängliga mått|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | Ja| Inga | [API Management](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft. AppPlatform/våren |Inga| Ja|
|Microsoft. Automation/automationAccounts | Ja| Inga | [Automation-konton](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | Gäller inte| Inga | [Batch-konton](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis|Ja| Inga |[Azure Cache for Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic|Inga|Ja|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/blobServices|Inga|Ja|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/fileServices|Inga|Ja|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/queueServices|Inga|Ja|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/tableServices|Inga|Ja| |
|Microsoft.CognitiveServices/accounts| Gäller inte | Inga | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |Ja | Ja | [Virtuella datorer](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |Gäller inte | Ja |[Skaluppsättningar för virtuella datorer](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft. ContainerInstance/containerGroups | Ja| Inga | [Behållar grupper](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft. container service/managedClusters | Ja | Inga | [Hanterade kluster](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft. DataBoxEdge/dataBoxEdgeDevices | Ja | Ja | |
|Microsoft. DataFactory/datafactories| Ja| Inga | [Data fabriker v1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft. DataFactory/fabriker |Ja | Inga |[Data fabriker v2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft. DataShare/konton |Inga| Ja|
|Microsoft. DBforMySQL/servers |Gäller inte| Inga |[DB för MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers |Gäller inte | Inga | [DATABAS för PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | Gäller inte | Inga |[IoT Hub mått](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Devices/provisioningServices| Ja | Inga |[DPS-mått](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Microsoft. EventGrid/Domains|Inga|Ja| |
|Microsoft. EventGrid/ämnen |Ja | Inga |[Event Grid ämnen](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft.EventHub/clusters |Ja| Inga |[Event Hubs kluster](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/namespaces |Ja| Inga |[Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Inga |Inga |[Valv](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows |Gäller inte | Inga |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces|Ja| Inga | [Machine Learning](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.Network/applicationGateways|Gäller inte| Inga |  |
|Microsoft. Network/dnsZones | Gäller inte| Inga | [DNS-zoner](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | Gäller inte | Inga |[ExpressRoute-kretsar](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft. Network/belastningsutjämnare (endast för standard-SKU: er)| Ja| Inga | [Belastnings utjämning](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways|Inga|Ja|
|Microsoft. Network/privateEndpoints|Inga|Ja|
|Microsoft. Network/privateLinkServices|Inga|Ja|
|Microsoft. Network/publicipaddresses |Gäller inte | Inga |[offentliga IP-adresser](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Ja | Inga | [Traffic Manager profiler](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft. OperationalInsights/arbets ytor| Ja | Inga | [Log Analytics arbets ytor](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Relay/namespaces | Ja | Inga | [Reläer](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Microsoft. peering/peeringServices|Inga|Ja|
|Microsoft. PowerBIDedicated/kapacitet | Gäller inte | Inga | [Kapaciteter](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |Gäller inte|Inga | [Sök tjänster](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces |Ja| Inga |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servers/elasticPools |   Inga | Ja |
|Microsoft.Sql/servers/databases    | Inga | Ja |
|Microsoft.Storage/storageAccounts |Ja | Inga | [Lagringskonton](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | Ja| Inga | [BLOB Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [fil tjänster](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [Queue Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) och [tabell tjänster](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |Gäller inte| Inga | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft. Microsoft. VMWareCloudSimple/virtualMachines |Ja|Inga |[CloudSimple Virtual Machines](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/serverfarms | Ja | Inga | [App Services planer](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Ja | Inga | [App Services](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) och [funktioner](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | Ja | Inga | [App Service platser](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>Nytto Last schema

> [!NOTE]
> Du kan också använda det [vanliga aviserings schemat](https://aka.ms/commonAlertSchemaDocs), vilket ger fördelarna med att ha en enda utöknings bar och enhetlig aviserings nytto Last i alla aviserings tjänster i Azure Monitor, för dina webhook-integrationer. [Lär dig mer om vanliga aviserings schema definitioner.](https://aka.ms/commonAlertSchemaDefinitions)


POST-åtgärden innehåller följande JSON-nyttolast och schema för alla nära nyare mått-varningar när en lämplig konfigurerad [Åtgärds grupp](../../azure-monitor/platform/action-groups.md) används:

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
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
            "metricValue": 1
          }
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

* Lär dig mer om den nya [aviserings upplevelsen](../../azure-monitor/platform/alerts-overview.md).
* Lär dig mer om [logg aviseringar i Azure](../../azure-monitor/platform/alerts-unified-log.md).
* Lär dig mer om [aviseringar i Azure](../../azure-monitor/platform/alerts-overview.md).
