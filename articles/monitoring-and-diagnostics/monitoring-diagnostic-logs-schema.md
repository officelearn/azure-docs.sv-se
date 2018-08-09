---
title: Azure diagnostikloggar tjänster och scheman som stöds
description: Förstå stöds tjänster och händelsen schemat för diagnostikloggar för Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 7/18/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: a075b60c525fc3883f4464f19a8964fb64ce15a0
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627720"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Tjänster som stöds, scheman och kategorier för Azure-diagnostikloggar

[Azure Monitor-diagnostikloggar](monitoring-overview-of-diagnostic-logs.md) är loggar som genereras av Azure-tjänster som beskriver driften av dessa tjänster eller resurser. Alla diagnostikloggar som är tillgängliga i Azure Monitor delar ett gemensamt översta schema med flexibilitet för varje tjänst att generera unika egenskaper för sina egna händelser.

En kombination av resurstypen (tillgänglig i den `resourceId` egenskapen) och `category` unikt identifiera ett schema. Den här artikeln beskriver översta schemat för diagnostikloggar och länkar till scheman för varje tjänst.

## <a name="top-level-diagnostic-logs-schema"></a>Översta diagnostikloggar schema

| namn | Krävs/valfritt | Beskrivning |
|---|---|---|
| tid | Obligatorisk | Tidsstämpel (UTC) för händelsen. |
| resourceId | Obligatorisk | Resurs-ID för den resurs som genereras av händelsen. För klienttjänster är det av formuläret /tenants/tenant-id/providers/provider-name. |
| TenantId | Krävs för klient loggar | Klient-ID för Active Directory-klient som den här händelsen är kopplad till. Den här egenskapen används endast för på klientnivå loggar, visas inte i resursnivå loggar. |
| operationName | Obligatorisk | Namnet på åtgärden som representeras av den här händelsen. Om händelsen representerar en RBAC-åtgärd, är detta Åtgärdsnamnet RBAC (t.ex.) Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Vanligtvis modellerats i form av en Resource Manager-åtgärd, även om de inte är faktiska dokumenterade Resource Manager-åtgärder (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Valfritt | Api-versionen som är associerade med åtgärden om operationName har utförts med hjälp av ett API (t.ex.) http://myservice.windowsazure.net/object?api-version=2016-06-01). Om det finns inga API som motsvarar den här åtgärden, representerar versionen version av åtgärden om egenskaper som är associerade med åtgärden ändras i framtiden. |
| category | Obligatorisk | Loggkategori för händelsen. Kategorin är precisionen som du kan aktivera eller inaktivera loggar på en viss resurs. Egenskaperna som visas i blobben som egenskaper för en händelse är samma inom en viss kategori och resurs loggtyp. Vanliga loggkategorier är ”granskning” ”drift” ”körning” och ”-begäran”. |
| resultType | Valfritt | Status för händelsen. Vanliga värden är startad, pågår, slutfört, misslyckades, aktiv och löst. |
| resultSignature | Valfritt | Sub-status för händelsen. Om den här åtgärden motsvarar ett REST API-anrop, är HTTP-statuskod för motsvarande REST-anropet. |
| resultDescription | Valfritt | Statisk textbeskrivningen av den här åtgärden, t.ex. ”Hämta lagringsfilen”. |
| durationMs | Valfritt | Varaktigheten i millisekunder. |
| callerIpAddress | Valfritt | Anroparen IP-adressen, om åtgärden motsvarar ett API-anrop som skulle kommer från en entitet med offentligt tillgängliga IP-adress. |
| correlationId | Valfritt | Ett GUID som används för att gruppera en uppsättning relaterade händelser. Normalt om två händelser har samma operationName men två olika statusar (t.ex.) ”Igång” och ”lyckades”), de delar samma Korrelations-ID. Detta kan också representera andra relationer mellan händelser. |
| identity | Valfritt | En JSON-blob som beskriver identiteten för användaren eller programmet som utförde åtgärden. Detta brukar vara auktorisering och anspråk / JWT-token från active directory. |
| Nivå | Valfritt | Allvarlighetsgrad för händelsen. Måste vara något av information, varning, fel eller kritiskt. |
| plats | Valfritt | Regionen för den resurs som sänder händelsen, t.ex. ”Östra USA” eller ”Frankrike, södra” |
| properties | Valfritt | Alla utökade egenskaper relaterade till den här viss kategori med händelser. Alla anpassade/unika egenskaper måste placeras i den här ”del B” av schemat. |

## <a name="service-specific-schemas-for-resource-diagnostic-logs"></a>Tjänstspecifika scheman för diagnostikloggar för resursen
Schemat för resursdiagnostikloggar varierar beroende på resursen och log kategori. I listan visas alla tjänster som gör tillgängliga diagnostikloggar och länkar till tjänsten och kategorispecifik schema där det är tillgängligt.

| Tjänst | Schemat och dokument |
| --- | --- |
| Azure Active Directory | [Översikt över](../active-directory/reports-monitoring/overview-activity-logs-in-azure-monitor.md), [Granska loggen schemat](../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) och [logga moduler schema](../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [API Management-diagnostikloggar](../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Programgateways |[Diagnostikloggning för Application Gateway](../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Logganalys för Azure Automation](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch Diagnostisk loggning](../batch/batch-diagnostics.md) |
| Content Delivery Network | [Azure diagnostikloggar för CDN](../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB-loggning](../cosmos-db/logging.md) |
| Data Factory | [Övervaka Datafabriker med hjälp av Azure Monitor](../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Åtkomst till diagnostikloggar för Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Åtkomst till diagnostikloggar för Azure Data Lake Store](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Databas för PostgreSQL |  Schemat är inte tillgänglig. |
| Event Hubs |[Azure Event Hubs-diagnostikloggar](../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Schemat är inte tillgänglig. |
| IoT-hubb | [IoT Hub-åtgärder](../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure Key Vault-loggning](../key-vault/key-vault-logging.md) |
| Load Balancer |
  [Logganalys för Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Anpassat Logic Apps B2B-spårningsschema](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Nätverkssäkerhetsgrupper |[Log Analytics för nätverkssäkerhetsgrupper (NSG)](../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS Protection | [Hantera Azure DDoS Protection Standard](../virtual-network/manage-ddos-protection.md) |
| Power BI Dedicated | Schemat är inte tillgänglig. |
| Recovery Services | [Datamodell för Azure Backup](../backup/backup-azure-reports-data-model.md)|
| Sökning |[Att aktivera och använda Search Traffic Analytics](../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus-diagnostikloggar](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database Diagnostisk loggning](../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Jobbet diagnostikloggar](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | Schemat är inte tillgänglig. |
| Virtuella nätverk | Schemat är inte tillgänglig. |
| Virtuella nätverksgatewayer | Schemat är inte tillgänglig. |

## <a name="supported-log-categories-per-resource-type"></a>Loggkategorier per resurstyp som stöds
|Resurstyp|Kategori|Visningsnamn för kategori|
|---|---|---|
|Microsoft.AnalysisServices/servers|Motorn|Motorn|
|Microsoft.AnalysisServices/servers|Tjänst|Tjänst|
|Microsoft.ApiManagement/service|GatewayLogs|Relaterade till ApiManagement-Gateway|
|Microsoft.Automation/automationAccounts|JobLogs|Jobbloggar|
|Microsoft.Automation/automationAccounts|JobStreams|Jobbströmmar|
|Microsoft.Automation/automationAccounts|DscNodeStatus|DSC-nodstatus|
|Microsoft.Batch/batchAccounts|ServiceLog|Tjänstloggar|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Hämtar måtten för slutpunkten, t.ex. bandbredd, egress osv.|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|ActivityRuns|Pipeline-körningar aktivitetsloggen|
|Microsoft.DataFactory/factories|PipelineRuns|Pipelinekörningar log|
|Microsoft.DataFactory/factories|TriggerRuns|Utlösaren kör log|
|Microsoft.DataLakeAnalytics/accounts|Granska|Granskningsloggar|
|Microsoft.DataLakeAnalytics/accounts|Ansökningar|Begära loggar|
|Microsoft.DataLakeStore/accounts|Granska|Granskningsloggar|
|Microsoft.DataLakeStore/accounts|Ansökningar|Begära loggar|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL-Server-loggar|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLBackupEvents|PostgreSQL-Säkerhetskopieringshändelser|
|Microsoft.Devices/IotHubs|Anslutningar|Anslutningar|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Enhetstelemetri|
|Microsoft.Devices/IotHubs|C2DCommands|C2D-kommandon|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Identitet åtgärder|
|Microsoft.Devices/IotHubs|FileUploadOperations|Filöverföringsåtgärder|
|Microsoft.Devices/IotHubs|Vägar|Vägar|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|C2D Twin åtgärder|
|Microsoft.Devices/IotHubs|TwinQueries|Twin frågor|
|Microsoft.Devices/IotHubs|JobsOperations|Jobbåtgärder|
|Microsoft.Devices/IotHubs|DirectMethods|Direkta metoder|
|Microsoft.Devices/IotHubs|E2EDiagnostics|E2E diagnostik (förhandsversion)|
|Microsoft.Devices /|DeviceOperations|Åtgärder|
|Microsoft.Devices /|ServiceOperations|Tjänståtgärder|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.EventHub/namespaces|ArchiveLogs|Arkivera loggar|
|Microsoft.EventHub/namespaces|OperationalLogs|Driftloggar|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Automatisk skalning loggar|
|Microsoft.KeyVault/vaults|AuditEvent|Granskningsloggar|
|Microsoft.Logic/workflows|WorkflowRuntime|Diagnostikhändelser för arbetsflödeskörning|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Spårningshändelser för integrationskonto|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Nätverkssäkerhetsgrupp händelse|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Nätverkssäkerhetsgrupp kopplad till regeln räknare|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Läsa in belastningsutjämnare Aviseringshändelser|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Hälsostatusen avsökning för belastningsutjämnaren|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS protection-aviseringar|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|VM-protection-aviseringar|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Application Gateway åtkomst-loggar|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Application Gateway-loggar för prestanda|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Application Gateway-brandväggen loggar|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Gatewayens diagnostikloggar|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Tunnel diagnostikloggar|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Dirigera diagnostikloggar|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|IKE-diagnostikloggar|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|P2S-diagnostikloggar|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Traffic Manager avsökningen Hälsotillståndshändelse resultat|
|Microsoft.Network/expressRouteCircuits|GWMCountersTable|Tabell med GWM räknare|
|Microsoft.PowerBIDedicated/capacities|Motorn|Motorn|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Azure Backup rapportdata|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery-jobb|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery-händelser|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Azure Site Recovery replikerade objekt|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Azure Site Recovery-Replikeringsstatistik|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery-återställningspunkter|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Azure Site dataöverföringshastighet Recovery replikering|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Skyddade Azure Site Recovery Dataomsättningen för disken|
|Microsoft.Search/searchServices|OperationLogs|Åtgärdsloggar|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Driftloggar|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Query Store-Körningsstatistik|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Query Store vänta statistik|
|Microsoft.Sql/servers/databases|Fel|Fel|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Vänta databasstatistik|
|Microsoft.Sql/servers/databases|Timeouter|Timeouter|
|Microsoft.Sql/servers/databases|block|block|
|Microsoft.Sql/servers/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/servers/databases|Granska|Granskningsloggar|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Granskningshändelse för SQL-säkerhet|
|Microsoft.StreamAnalytics/streamingjobs|Körning|Körning|
|Microsoft.StreamAnalytics/streamingjobs|Redigering|Redigering|

## <a name="next-steps"></a>Nästa steg

* [Mer information om diagnostikloggar](monitoring-overview-of-diagnostic-logs.md)
* [Stream resursdiagnostikloggar till **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Ändra resursdiagnostikinställningar med hjälp av Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analysera loggar från Azure storage med Log Analytics](../log-analytics/log-analytics-azure-storage.md)
