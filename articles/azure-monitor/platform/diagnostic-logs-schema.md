---
title: Tjänster och scheman som stöds av Azure-diagnostikloggar
description: Förstå de tjänster och händelse scheman som stöds för Azure Diagnostic-loggar.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/11/2018
ms.author: robb
ms.subservice: logs
ms.openlocfilehash: fdcfcbaf99d48a345d2be4da297be1c9139da15c
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71308118"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Tjänster, scheman och kategorier som stöds för Azure Diagnostic-loggar

[Azure Monitor diagnostikloggar](../../azure-monitor/platform/resource-logs-overview.md) loggar ut av Azure-tjänster som beskriver driften av dessa tjänster eller resurser. Alla diagnostikloggar som är tillgängliga via Azure Monitor dela ett gemensamt schema på högsta nivå, med flexibilitet för varje tjänst för att generera unika egenskaper för sina egna händelser.

En kombination av resurs typen (tillgänglig i `resourceId` egenskapen) `category` och unikt identifiera ett schema. Den här artikeln beskriver schemat på högsta nivån för diagnostikloggar och länkar till scheman för varje tjänst.

## <a name="top-level-diagnostic-logs-schema"></a>Schema för diagnostiska loggar på högsta nivån

| Name | Obligatorisk/valfri | Beskrivning |
|---|---|---|
| time | Obligatorisk | Tids stämplingen (UTC) för händelsen. |
| resourceId | Obligatorisk | Resurs-ID för den resurs som har orsakat händelsen. För klient tjänster är detta av formatet/Tenants/Tenant-ID/providers/Provider-Name. |
| tenantId | Krävs för klient loggar | Klient-ID för den Active Directory klient som den här händelsen är kopplad till. Den här egenskapen används bara för loggar på klient nivå, den visas inte i loggar på resurs nivå. |
| operationName | Obligatorisk | Namnet på åtgärden som representeras av den här händelsen. Om händelsen representerar en RBAC-åtgärd är detta namnet på RBAC-åtgärden (t. ex. Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Vanligt vis modelleras i form av en Resource Manager-åtgärd, även om de inte är faktiska dokumenterade Resource Manager`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`-åtgärder () |
| operationVersion | Valfritt | Den API-version som är kopplad till åtgärden, om operationName utfördes med hjälp av ett API (t. ex. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Om det inte finns något API som motsvarar den här åtgärden representerar-versionen den åtgärd som är associerad med åtgärden i framtiden. |
| category | Obligatorisk | Händelsens logg kategori. Kategori är den granularitet som du kan använda för att aktivera eller inaktivera loggar för en viss resurs. Egenskaperna som visas i en händelses egenskaps-BLOB är desamma inom en viss logg kategori och resurs typ. Typiska logg kategorier är "granskning", "körning" och "begäran". |
| resultType | Valfritt | Händelsens status. Vanliga värden är startad, pågår, lyckades, misslyckades, aktivt och löst. |
| resultSignature | Valfritt | Händelsens under status. Om den här åtgärden motsvarar ett REST API-anrop är detta HTTP-statuskod för motsvarande REST-anrop. |
| resultDescription | Valfritt | Den statiska text beskrivningen för den här åtgärden, t. ex. "Hämta lagrings fil". |
| durationMs | Valfritt | Åtgärdens varaktighet i millisekunder. |
| callerIpAddress | Valfritt | IP-adressen för anroparen, om åtgärden motsvarar ett API-anrop som kommer från en entitet med en offentligt tillgänglig IP-adress. |
| correlationId | Valfritt | Ett GUID som används för att gruppera samman en uppsättning relaterade händelser. Normalt, om två händelser har samma operationName men två olika status värden (t. ex. "Startade" och "lyckades") delar samma korrelations-ID. Detta kan även representera andra relationer mellan händelser. |
| identity | Valfritt | En JSON-blob som beskriver identiteten för den användare eller det program som utförde åtgärden. Detta inkluderar vanligt vis auktorisering och anspråk/JWT-token från Active Directory. |
| Nivå | Valfritt | Händelsens allvarlighets grad. Måste vara en av information, varning, fel eller kritisk. |
| location | Valfritt | Den region i resursen som avger händelsen, t. ex. "USA, östra" eller "Frankrike, södra" |
| properties | Valfritt | Eventuella utökade egenskaper som är relaterade till den här specifika kategorin av händelser. Alla anpassade/unika egenskaper måste placeras i det här "del B" av schemat. |

## <a name="service-specific-schemas-for-resource-diagnostic-logs"></a>Tjänstspecifika scheman för resurs diagnostiska loggar
Schemat för resurs diagnostiska loggar varierar beroende på resurs-och logg kategori. I den här listan visas alla tjänster som gör tillgängliga diagnostikloggar och länkar till tjänsten och det projektspecifika schemat där det är tillgängligt.

| Tjänsten | Schema & dokument |
| --- | --- |
| Azure Active Directory | [Översikt](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), schema för [Gransknings logg](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) och [inloggnings tillägg](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [API Management diagnostikloggar](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Programgateways |[Diagnostikloggning för Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics för Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch diagnostisk loggning](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Azure Database for MySQL diagnostikloggar](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL diagnostikloggar](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Cognitive Services | [Diagnostisk loggning för Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Content Delivery Network | [Azure-diagnostikloggar för CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB loggning](../../cosmos-db/logging.md) |
| Data Factory | [Övervaka data fabriker med hjälp av Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Åtkomst till diagnostikloggar för Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Åtkomst till diagnostikloggar för Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Azure Event Hubs diagnostikloggar](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | Schemat är inte tillgängligt. |
| Azure Firewall | Schemat är inte tillgängligt. |
| IoT-hubb | [IoT Hub åtgärder](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure Key Vault-loggning](../../key-vault/key-vault-logging.md) |
| Kubernetes Service |[Azure Kubernetes-loggning](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Logganalys för Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Anpassat Logic Apps B2B-spårningsschema](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Nätverkssäkerhetsgrupper |[Log Analytics för nätverkssäkerhetsgrupper (NSG)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS Protection | [Hantera Azure DDoS Protection standard](../../virtual-network/manage-ddos-protection.md) |
| Dedikerad Power BI | [Diagnostisk loggning för Power BI Embedded i Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Data modell för Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Search |[Aktivera och använda Sök Trafikanalys](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus diagnostikloggar](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database diagnostisk loggning](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Jobb diagnostiska loggar](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager logg schema](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuella nätverk | Schemat är inte tillgängligt. |
| Virtuella nätverksgatewayer | Schemat är inte tillgängligt. |

## <a name="supported-log-categories-per-resource-type"></a>Logg kategorier som stöds per resurs typ
|Resurstyp|Category|Kategori visnings namn|
|---|---|---|
|Microsoft.AnalysisServices/servers|Motorn|Motorn|
|Microsoft.AnalysisServices/servers|Tjänsten|Tjänsten|
|Microsoft.ApiManagement/service|GatewayLogs|Loggar som rör API Management-Gateway|
|Microsoft. Automation/automationAccounts|JobLogs|Jobb loggar|
|Microsoft. Automation/automationAccounts|JobStreams|Jobb strömmar|
|Microsoft. Automation/automationAccounts|DscNodeStatus|DSC-nods status|
|Microsoft.Batch/batchAccounts|ServiceLog|Tjänst loggar|
|Microsoft. CDN/profiler/slut punkter|CoreAnalytics|Hämtar Mät värdena för slut punkten, t. ex. bandbredd, utgående data osv.|
|Microsoft.ClassicNetwork/networksecuritygroups|Regel flödes händelse för nätverks säkerhets grupp|Regel flödes händelse för nätverks säkerhets grupp|
|Microsoft.CognitiveServices/accounts|Granska|Granskningsloggar|
|Microsoft.CognitiveServices/accounts|RequestResponse|Förfrågningar och svars loggar|
|Microsoft. container service/managedClusters|kube-apiserver|Kubernetes API Server|
|Microsoft. container service/managedClusters|kube-controller-manager|Kubernetes Controller Manager|
|Microsoft. container service/managedClusters|cluster-autoscaler|Kubernetes Cluster Autoscaler|
|Microsoft. container service/managedClusters|kube-scheduler|Kubernetes Scheduler|
|Microsoft. container service/managedClusters|skyddade|Autentiseringswebbhook|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft. DataFactory/fabriker|ActivityRuns|Pipeline-aktivitet kör logg|
|Microsoft. DataFactory/fabriker|PipelineRuns|Pipeline kör logg|
|Microsoft. DataFactory/fabriker|TriggerRuns|Utlös körnings logg|
|Microsoft.DataLakeAnalytics/accounts|Granska|Granskningsloggar|
|Microsoft.DataLakeAnalytics/accounts|Begäranden|Begär ande loggar|
|Microsoft.DataLakeStore/accounts|Granska|Granskningsloggar|
|Microsoft.DataLakeStore/accounts|Begäranden|Begär ande loggar|
|Microsoft. DBforMySQL/servers|MySqlSlowLogs|MySQL server-loggar|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL Server-loggar|
|Microsoft.Devices/IotHubs|Anslutningar|Anslutningar|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Enhetstelemetri|
|Microsoft.Devices/IotHubs|C2DCommands|C2D-kommandon|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Enhets identitets åtgärder|
|Microsoft.Devices/IotHubs|FileUploadOperations|Fil överförings åtgärder|
|Microsoft.Devices/IotHubs|Vägar|Vägar|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|C2D dubbla åtgärder|
|Microsoft.Devices/IotHubs|twinQueries|Dubbla frågor|
|Microsoft.Devices/IotHubs|JobsOperations|Jobb åtgärder|
|Microsoft.Devices/IotHubs|DirectMethods|Direkta metoder|
|Microsoft.Devices/IotHubs|E2EDiagnostics|E2E-diagnostik (för hands version)|
|Microsoft.Devices/IotHubs|Konfigurationer|Konfigurationer|
|Microsoft.Devices/provisioningServices|DeviceOperations|Enhetsåtgärder|
|Microsoft.Devices/provisioningServices|ServiceOperations|Tjänst åtgärder|
|Microsoft. DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft. DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft. DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.EventHub/namespaces|ArchiveLogs|Arkiv loggar|
|Microsoft.EventHub/namespaces|OperationalLogs|Drift loggar|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Automatisk skalnings loggar|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|Utvärdering av autoskalning|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|Skalningsåtgärder för autoskalning|
|Microsoft.IoTSpaces/Graph|Spårning|Spårning|
|Microsoft.IoTSpaces/Graph|I drift|I drift|
|Microsoft.IoTSpaces/Graph|Granska|Granska|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|Ingress|Ingress|
|Microsoft.IoTSpaces/Graph|Egress|Egress|
|Microsoft.KeyVault/vaults|AuditEvent|Granskningsloggar|
|Microsoft.Logic/workflows|WorkflowRuntime|Diagnostiska händelser för arbets flödes körning|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Spårnings händelser för integrations konto|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Händelse för nätverks säkerhets grupp|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Regel räknare för nätverks säkerhets grupp|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Load Balancer aviserings händelser|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Load Balancer avsökningens hälso status|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS skydds meddelanden|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Flödes loggar för DDoSa beslut om minskning|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Rapporter för DDoS-åtgärder|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Aviseringar för VM-skydd|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Application Gateway åtkomst logg|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Application Gateway prestanda logg|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Application Gateway brand Väggs logg|
|Microsoft. Network/securegateways|AzureFirewallApplicationRule|Regel för Azure brand Väggs program|
|Microsoft. Network/securegateways|AzureFirewallNetworkRule|Nätverks regel för Azure Firewall|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Regel för Azure brand Väggs program|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Nätverks regel för Azure Firewall|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Gateway-diagnostikloggar|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Tunnel diagnostikloggar|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Vidarebefordra diagnostikloggar|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|IKE-diagnostikloggar|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|P2S diagnostikloggar|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Traffic Manager händelse av hälso resultat för avsökning|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Tabell loggar för peering-routning|
|Microsoft. Network/frontdoors|FrontdoorAccessLog|Ytterdörr åtkomst logg|
|Microsoft. Network/frontdoors|FrontdoorWebApplicationFirewallLog|Ytterdörr webb program brand Väggs logg|
|Microsoft. PowerBIDedicated/kapacitet|Motorn|Motorn|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Rapporteringsdata för Azure Backup|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery-jobb|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery-händelser|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Replikerade objekt i Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Azure Site Recovery-replikeringsstatistik|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery-återställningspunkter|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery överförings hastighet för replikeringsdata|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery skyddad disk data omsättning|
|Microsoft.Search/searchServices|OperationLogs|Åtgärds loggar|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Drift loggar|
|Microsoft.Sql/servers/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/servers/databases|AutomaticTuning|Automatisk justering|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Körnings statistik för Query Store|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Väntande statistik för Query Store|
|Microsoft.Sql/servers/databases|Fel|Fel|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Väntande statistik över databasen|
|Microsoft.Sql/servers/databases|Timeouter|Timeouter|
|Microsoft.Sql/servers/databases|Delar|Delar|
|Microsoft.Sql/servers/databases|Dödlägen|Dödlägen|
|Microsoft.Sql/servers/databases|Granska|Granskningsloggar|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Säkerhets gransknings händelse i SQL|
|Microsoft.Sql/servers/databases|DmsWorkers|DMS-arbetare|
|Microsoft.Sql/servers/databases|ExecRequests|Exec-begäranden|
|Microsoft.Sql/servers/databases|RequestSteps|Förfrågnings steg|
|Microsoft.Sql/servers/databases|SqlRequests|SQL-begäranden|
|Microsoft.Sql/servers/databases|Väntar|Väntar|
|Microsoft.Sql/managedInstances|ResourceUsageStats|Statistik för resursanvändning|
|Microsoft.Sql/managedInstances|SQLSecurityAuditEvents|Säkerhets gransknings händelse i SQL|
|Microsoft. SQL/managedInstances/-databaser|SQLInsights|SQL Insights|
|Microsoft. SQL/managedInstances/-databaser|QueryStoreRuntimeStatistics|Körnings statistik för Query Store|
|Microsoft. SQL/managedInstances/-databaser|QueryStoreWaitStatistics|Väntande statistik för Query Store|
|Microsoft. SQL/managedInstances/-databaser|Fel|Fel|
|Microsoft.StreamAnalytics/streamingjobs|Körning|Körning|
|Microsoft.StreamAnalytics/streamingjobs|Redigering|Redigering|
|microsoft.web/sites|FunctionExecutionLogs|Funktions körnings loggar|
|microsoft.web/sites/slots|FunctionExecutionLogs|Funktions körnings loggar|

## <a name="next-steps"></a>Nästa steg

* [Läs mer om diagnostikloggar](../../azure-monitor/platform/resource-logs-overview.md)
* [Stream resursdiagnostikloggar till **Event Hubs**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Ändra resursdiagnostikinställningar med hjälp av Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analysera loggar från Azure storage med Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
