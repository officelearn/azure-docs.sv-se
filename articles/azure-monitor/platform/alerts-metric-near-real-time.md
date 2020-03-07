---
title: Resurser som stöds för mått varningar i Azure Monitor
description: Referens för support mått och loggar för mått varningar i Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 3/5/2020
ms.subservice: alerts
ms.openlocfilehash: e4dfc33756a287b6d2f6e41edded3332ca85241d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363454"
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
|Microsoft.ApiManagement/service | Ja| Nej | [API Management](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft. AppPlatform/våren |Nej| Ja|
|Microsoft. Automation/automationAccounts | Ja| Nej | [Automation-konton](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | Ej tillämpligt| Nej | [Batch-konton](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis|Ja| Nej |[Azure Cache for Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic|Nej|Ja|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/blobServices|Nej|Ja|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/fileServices|Nej|Ja|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/queueServices|Nej|Ja|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/tableServices|Nej|Ja| |
|Microsoft.CognitiveServices/accounts| Ej tillämpligt | Nej | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |Ja | Ja | [Virtual Machines](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |Ej tillämpligt | Ja |[Skaluppsättningar för virtuella datorer](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft. ContainerInstance/containerGroups | Ja| Nej | [Behållar grupper](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft. container service/managedClusters | Ja | Nej | [Hanterade kluster](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft. DataBoxEdge/dataBoxEdgeDevices | Ja | Ja | |
|Microsoft. DataFactory/datafactories| Ja| Nej | [Data fabriker v1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft. DataFactory/fabriker |Ja | Nej |[Data fabriker v2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft. DataShare/konton |Nej| Ja|
|Microsoft. DBforMySQL/servers |Ej tillämpligt| Nej |[DB för MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers |Ej tillämpligt | Nej | [DATABAS för PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | Ej tillämpligt | Nej |[IoT Hub mått](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Devices/provisioningServices| Ja | Nej |[DPS-mått](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Microsoft. EventGrid/Domains|Nej|Ja| |
|Microsoft. EventGrid/ämnen |Ja | Nej |[Event Grid ämnen](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft.EventHub/clusters |Ja| Nej |[Event Hubs kluster](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/namespaces |Ja| Nej |[Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Nej |Nej |[Valv](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows |Ej tillämpligt | Nej |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces|Ja| Nej | [Machine Learning](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.NetApp/netAppAccounts/capacityPools |Ja| Nej | [Pool för Azure NetApp-kapacitet](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft. NetApp/netAppAccounts/capacityPools/Volumes |Ja| Nej | [Azure NetApp-volymer](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways|Ej tillämpligt| Nej |  |
|Microsoft. Network/dnsZones | Ej tillämpligt| Nej | [DNS-zoner](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | Ej tillämpligt | Nej |[ExpressRoute-kretsar](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft. Network/belastningsutjämnare (endast för standard-SKU: er)| Ja| Nej | [Belastnings utjämning](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways|Nej|Ja|
|Microsoft. Network/privateEndpoints|Nej|Ja|
|Microsoft. Network/privateLinkServices|Nej|Ja|
|Microsoft. Network/publicipaddresses |Ej tillämpligt | Nej |[offentliga IP-adresser](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Ja | Nej | [Traffic Manager profiler](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft. OperationalInsights/arbets ytor| Ja | Nej | [Log Analytics arbets ytor](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Relay/namespaces | Ja | Nej | [Reläer](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Microsoft. peering/peeringServices|Nej|Ja|
|Microsoft. PowerBIDedicated/kapacitet | Ej tillämpligt | Nej | [Kapaciteter](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |Ej tillämpligt|Nej | [Sök tjänster](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces |Ja| Nej |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servers/elasticPools |   Nej | Ja |
|Microsoft.Sql/servers/databases    | Nej | Ja |
|Microsoft.Storage/storageAccounts |Ja | Nej | [Lagringskonton](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | Ja| Nej | [BLOB Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [fil tjänster](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [Queue Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) och [tabell tjänster](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |Ej tillämpligt| Nej | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft. Microsoft. VMWareCloudSimple/virtualMachines |Ja|Nej |[CloudSimple Virtual Machines](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/hostingEnvironments/multiRolePools | Ja | Nej | [App Service-miljön pooler med flera roller](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | Ja | Nej | [App Service-miljön Worker-pooler](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | Ja | Nej | [App Services planer](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Ja | Nej | [App Services](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) och [funktioner](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | Ja | Nej | [App Service platser](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

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
