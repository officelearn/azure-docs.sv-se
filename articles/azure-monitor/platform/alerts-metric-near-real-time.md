---
title: Resurser som stöds för måttaviseringar i Azure Monitor
description: Referens på stödmått och loggar för måttaviseringar i Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 3/5/2020
ms.subservice: alerts
ms.openlocfilehash: 62c1e29b668c3ef58b0e26e2749c55b913279425
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545682"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Resurser som stöds för måttaviseringar i Azure Monitor

Azure Monitor stöder nu en [ny måttaviseringstyp](../../azure-monitor/platform/alerts-overview.md) som har betydande fördelar jämfört med äldre [klassiska måttaviseringar](../../azure-monitor/platform/alerts-classic.overview.md). Mått är tillgängliga för [stor lista över Azure-tjänster](../../azure-monitor/platform/metrics-supported.md). De nyare aviseringarna stöder en (växande) delmängd av resurstyperna. I den här artikeln visas den delmängden.

Du kan också använda nyare måttaviseringar på populära loggdata som lagras i en Log Analytics-arbetsyta som extraherats som mått. Mer information finns [i Måttaviseringar för loggar](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Support för Portal, PowerShell, CLI, REST
För närvarande kan du skapa nyare måttaviseringar endast i Azure-portalen, [REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/)eller [Resource Manager-mallar](../../azure-monitor/platform/alerts-metric-create-templates.md). Stöd för att konfigurera nyare aviseringar med PowerShell och Azure CLI version 2.0 och högre kommer snart.

## <a name="metrics-and-dimensions-supported"></a>Mått och dimensioner som stöds
Nyare måttaviseringar stöder aviseringar för mått som använder dimensioner. Du kan använda dimensioner för att filtrera måttet till rätt nivå. Alla mått som stöds tillsammans med tillämpliga dimensioner kan utforskas och visualiseras från [Azure Monitor - Metrics Explorer](../../azure-monitor/platform/metrics-charts.md).

Här är den fullständiga listan över Azure monitor-måttkällor som stöds av de nyare aviseringarna:

|Resurstyp  |Dimensioner som stöds |Aviseringar för flera resurser| Tillgängliga mätvärden|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | Ja| Inga | [API Management](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.AppPlattform/Fjäder |Inga| Ja|
|Microsoft.Automation/automationKonton | Ja| Inga | [Automation-konton](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchKonton | Ej tillämpligt| Inga | [Batch-konton](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis|Ja| Inga |[Azure Cache for Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic Microsoft.ClassicStorage/storageAccounts/mmxclassic Microsoft.ClassicStorage/storageAccounts/mmxclassic Microsoft.|Inga|Ja|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/blobServices Microsoft.ClassicStorage/storageAccounts/mmxclassic/blobServices Microsoft.ClassicStorage/storageAccounts/mmxclassic/blobServices Microsoft.|Inga|Ja|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/fileServices Microsoft.ClassicStorage/storageAccounts/mmxclassic/fileServices Microsoft.ClassicStorage/storageAccounts/mmxclassic/fileServices Microsoft.|Inga|Ja|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/queueServices Microsoft.ClassicStorage/storageAccounts/mmxclassic/queueServices Microsoft.ClassicStorage/storageAccounts/mmxclassic/queueServices Microsoft.|Inga|Ja|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/tableServices Microsoft.ClassicStorage/storageAccounts/mmxclassic/tableServices Microsoft.ClassicStorage/storageAccounts/mmxclassic/tableServices Microsoft.|Inga|Ja| |
|Microsoft.CognitiveServices/konton| Ej tillämpligt | Inga | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |Ja | Ja | [Virtuella datorer](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |Ej tillämpligt | Ja |[Skalningsuppsättningar för virtuella datorer](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Ja| Inga | [Containergrupper](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Ja | Inga | [Hanterade kluster](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Ja | Ja | |
|Microsoft.DataFactory/datafakta| Ja| Inga | [Data Fabriker V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/fabriker |Ja | Inga |[Data Fabriker V2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DataShare/konton |Inga| Ja|
|Microsoft.DBforMySQL/servrar |Ej tillämpligt| Inga |[DB för MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servrar |Ej tillämpligt | Inga | [DB för PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | Ej tillämpligt | Inga |[IoT Hub-mått](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Devices/provisioningServices Microsoft.Devices/provisioningServices Microsoft.Devices/provisioningServices Microsoft.| Ja | Inga |[DPS-mått](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Microsoft.EventGrid/domäner|Inga|Ja| |
|Ämnen för Microsoft.EventGrid/Event |Ja | Inga |[Avsnitt om händelserutnät](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft.EventHub/kluster |Ja| Inga |[Kluster för eventhubbar](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/namnområden |Ja| Inga |[Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/valv| Inga |Inga |[Valv](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/arbetsflöden |Ej tillämpligt | Inga |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/arbetsytor|Ja| Inga | [Maskininlärning](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.NetApp/netAppAccounts/capacityPools |Ja| Inga | [Azure NetApp kapacitet pooler](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volymer |Ja| Inga | [Azure NetApp-volymer](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways|Ej tillämpligt| Inga |  |
|Microsoft.Network/dnsZones | Ej tillämpligt| Inga | [DNS-zoner](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | Ej tillämpligt | Inga |[ExpressRoute-kretsar](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (endast för standardskronor)| Ja| Inga | [Belastningsutjämnare](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/natGateways|Inga|Ja|
|Microsoft.Network/privateEndpoints|Inga|Ja|
|Microsoft.Network/privateLinkServices|Inga|Ja|
|Microsoft.Network/publicipaddresses |Ej tillämpligt | Inga |[Offentliga IP-adresser](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Ja | Inga | [Traffic Manager-profiler](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/arbetsytor| Ja | Inga | [Log Analytics-arbetsytor](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Relay/namnområden | Ja | Inga | [Reläer](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Microsoft.Peering/peeringServices|Inga|Ja|
|Microsoft.PowerBIDedicated/capacity | Ej tillämpligt | Inga | [Kapaciteter](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |Ej tillämpligt|Inga | [Söktjänster](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namnområden |Ja| Inga |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servrar/elasticPools |    Inga | Ja |
|Microsoft.Sql/servrar/databaser    | Inga | Ja |
|Microsoft.Storage/storageAccounts |Ja | Inga | [Lagringskonton](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageRedovisning/-tjänster | Ja| Inga | [Blob Services,](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices) [Filtjänster,](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices) [Kötjänster](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) och [Tabelltjänster](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |Ej tillämpligt| Inga | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.Microsoft.VMWareCloudSimple/virtualMachines |Ja|Inga |[Virtuella CloudSimple-datorer](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/hostingMiljöer/multiRolePools | Ja | Inga | [Flerrollspooler för apptjänstmiljö](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingMiljöer/workerPools | Ja | Inga | [Arbetspooler för apptjänstmiljö](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | Ja | Inga | [Planer för apptjänst](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Webbplatser | Ja | Inga | [App Services](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites)|
|Microsoft.webbplatser/platser | Ja | Inga | [Platser för apptjänst](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>Schema för nyttolast

> [!NOTE]
> Du kan också använda det [gemensamma varningsschemat](https://aka.ms/commonAlertSchemaDocs), som ger fördelen av att ha en enda utökningsbar och enhetlig varningsnyttolast över alla varningstjänster i Azure Monitor, för dina webhook-integreringar. [Läs mer om de vanliga definitionerna för varningsscheman.](https://aka.ms/commonAlertSchemaDefinitions)


POST-åtgärden innehåller följande JSON-nyttolast och schema för alla nära nyare måttaviseringar när en lämpligt konfigurerad [åtgärdsgrupp](../../azure-monitor/platform/action-groups.md) används:

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

* Läs mer om den nya [aviseringarupplevelsen](../../azure-monitor/platform/alerts-overview.md).
* Läs mer om [loggaviseringar i Azure](../../azure-monitor/platform/alerts-unified-log.md).
* Läs mer om [aviseringar i Azure](../../azure-monitor/platform/alerts-overview.md).
