---
title: "Azure diagnostikloggar tjänster som stöds och scheman | Microsoft Docs"
description: "Förstå stöds tjänster och händelsen schemat för Azure diagnostikloggar."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: johnkem
ms.openlocfilehash: 1a58db2d424e4280fd56be972d48df89648e8c13
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Tjänster som stöds, scheman och kategorier för diagnostikloggar i Azure

[Azure-resurs diagnostikloggar](monitoring-overview-of-diagnostic-logs.md) är loggar från Azure-resurser som beskriver användningen av den här resursen. Dessa loggar är resurstypen specifika. I den här artikeln beskriver vi en uppsättning tjänster som stöds och Händelseschema för händelser som sänds av varje tjänst. Den här artikeln innehåller också en fullständig lista över tillgängliga kategorier per resurstypen.

## <a name="supported-services-and-schemas-for-resource-diagnostic-logs"></a>Tjänster och scheman som stöds för resursen diagnostikloggar
Schemat för resursen diagnostikloggar varierar beroende på kategorin resurs och loggfiler.   

| Tjänst | Schemat & Docs |
| --- | --- |
| Analysis Services | Schemat är inte tillgänglig. |
| API Management | [API Management diagnostikloggar](../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Application Gateways |[Diagnostikloggning för Programgateway](../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Logganalys för Azure Automation](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch-diagnostikloggning](../batch/batch-diagnostics.md) |
| Kunden insikter | Schemat är inte tillgänglig. |
| Content Delivery Network | Schemat är inte tillgänglig. |
| CosmosDB | [Azure Cosmos DB-loggning](../cosmos-db/logging.md) |
| Data Lake Analytics |[Åtkomst till diagnostikloggar för Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Åtkomst till diagnostikloggarna för Azure Data Lake Store](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Händelsehubbar |[Azure Event Hubs diagnostiska loggar](../event-hubs/event-hubs-diagnostic-logs.md) |
| IoT Hub | [Åtgärder för IoT-hubb](../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure Key Vault-loggning](../key-vault/key-vault-logging.md) |
| Belastningsutjämnare |[Logganalys för Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Anpassat Logic Apps B2B-spårningsschema](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Nätverkssäkerhetsgrupper |[Log Analytics för nätverkssäkerhetsgrupper (NSG)](../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS-skydd | Schemat är inte tillgänglig. |
| Recovery Services | [Datamodell för Azure-säkerhetskopiering](../backup/backup-azure-reports-data-model.md)|
| Söka |[Att aktivera och använda Sök trafik Analytics](../search/search-traffic-analytics.md) |
| Serverhantering | Schemat är inte tillgänglig. |
| Service Bus |[Azure Service Bus-diagnostikloggar](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database-diagnostikloggning](../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Jobbet diagnostikloggar](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Virtuella nätverk | Schemat är inte tillgänglig. |

## <a name="supported-log-categories-per-resource-type"></a>Stöd för kategorier i loggen resurstyp
|Resurstyp|Kategori|Kategori visningsnamn|
|---|---|---|
|Microsoft.aadiam/tenants|Inloggning|Inloggning|
|Microsoft.AnalysisServices/servers|Motorn|Motorn|
|Microsoft.AnalysisServices/servers|Tjänst|Tjänst|
|Microsoft.ApiManagement/service|GatewayLogs|Relaterade till ApiManagement Gateway|
|Microsoft.Automation/automationAccounts|JobLogs|Jobbloggar|
|Microsoft.Automation/automationAccounts|JobStreams|Dataströmmar för jobbet|
|Microsoft.Automation/automationAccounts|DscNodeStatus|DSC-noden Status|
|Microsoft.Batch/batchAccounts|ServiceLog|Tjänsten loggar|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Hämtar mätvärden för slutpunkten, t.ex. bandbredd, utgång osv.|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|Aktivitetskörningen|Pipelinen körs aktivitetsloggen|
|Microsoft.DataFactory/factories|PipelineRuns|Pipeline körs logg|
|Microsoft.DataFactory/factories|TriggerRuns|Trigger kör logg|
|Microsoft.DataLakeAnalytics/accounts|Granska|Granskningsloggar|
|Microsoft.DataLakeAnalytics/accounts|Begäranden|Begäran loggar|
|Microsoft.DataLakeStore/accounts|Granska|Granskningsloggar|
|Microsoft.DataLakeStore/accounts|Begäranden|Begäran loggar|
|Microsoft.Devices/IotHubs|Anslutningar|Anslutningar|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Enhetstelemetrin|
|Microsoft.Devices/IotHubs|C2DCommands|C2D kommandon|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Enhetens identitet åtgärder|
|Microsoft.Devices/IotHubs|FileUploadOperations|Överför filåtgärder|
|Microsoft.Devices/IotHubs|Vägar|Vägar|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|C2D dubbla åtgärder|
|Microsoft.Devices/IotHubs|TwinQueries|Dubbla frågor|
|Microsoft.Devices/IotHubs|JobsOperations|Åtgärder för jobb|
|Microsoft.Devices/IotHubs|DirectMethods|Direkt-metoder|
|Microsoft.Devices/provisioningServices|DeviceOperations|Åtgärder för enhet|
|Microsoft.Devices/provisioningServices|ServiceOperations|Tjänståtgärder|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.EventHub/namespaces|ArchiveLogs|Arkivera loggar|
|Microsoft.EventHub/namespaces|OperationalLogs|Operativa loggar|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Automatisk skalning loggar|
|Microsoft.KeyVault/vaults|AuditEvent|Granskningsloggar|
|Microsoft.Logic/workflows|WorkflowRuntime|Diagnostikhändelser för arbetsflödeskörning|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Integration konto spåra händelser|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Nätverkssäkerhetsgrupphändelse|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Regelräknare för Nätverkssäkerhetsgrupp|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Regeln flödet Nätverkssäkerhetsgrupphändelse|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Läsa in belastningsutjämning avisering händelser|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Läsa in hälsostatus för belastningsutjämnaren avsökning|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS-skydd-meddelanden|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|VM protection-aviseringar|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Gateway tillgång för programloggen|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Programloggen Gateway prestanda|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Programloggen Gateway-brandväggen|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Gateway diagnostikloggar|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Tunnel diagnostikloggar|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Vidarebefordra diagnostikloggar|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Traffic Manager avsökningen Hälsohändelse resultat|
|Microsoft.Network/expressRouteCircuits|GWMCountersTable|Tabell med GWM räknare|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Azure-säkerhetskopiering rapportdata|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery-jobb|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery-händelser|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Azure Site Recovery replikerade objekt|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Azure Site Recovery replikering statistik|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Återställningspunkter för Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Åtgärdsloggar|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Operativa loggar|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Query Store Körningsstatistik|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Query Store vänta statistik|
|Microsoft.Sql/servers/databases|Fel|Fel|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Databasen vänta statistik|
|Microsoft.Sql/servers/databases|Tidsgränser|Tidsgränser|
|Microsoft.Sql/servers/databases|Block|Block|
|Microsoft.Sql/servers/databases|SQLInsights|SQL-insikter|
|Microsoft.StreamAnalytics/streamingjobs|Körning|Körning|
|Microsoft.StreamAnalytics/streamingjobs|Redigering|Redigering|

## <a name="next-steps"></a>Nästa steg

* [Mer information om diagnostikloggar](monitoring-overview-of-diagnostic-logs.md)
* [Strömma resurs diagnostiska loggar till **Händelsehubbar**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Ändra resurs diagnostiska inställningar med hjälp av REST API för Azure-Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analysera loggar från Azure storage med logganalys](../log-analytics/log-analytics-azure-storage.md)
