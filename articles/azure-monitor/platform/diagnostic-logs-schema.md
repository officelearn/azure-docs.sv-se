---
title: Tjänster och scheman som stöds av Azure-diagnostikloggar
description: Förstå de tjänster och händelse scheman som stöds för Azure Diagnostic-loggar.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 10/11/2018
ms.openlocfilehash: f2d71972fa8acc930800a70193f688246ee7415c
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555574"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Tjänster, scheman och kategorier som stöds för Azure Diagnostic-loggar

[Azure Monitor diagnostikloggar](../../azure-monitor/platform/resource-logs-overview.md) loggar ut av Azure-tjänster som beskriver driften av dessa tjänster eller resurser. Alla diagnostikloggar som är tillgängliga via Azure Monitor dela ett gemensamt schema på högsta nivå, med flexibilitet för varje tjänst för att generera unika egenskaper för sina egna händelser.

En kombination av resurs typen (tillgänglig i egenskapen `resourceId`) och `category` unikt identifierar ett schema. Den här artikeln beskriver schemat på högsta nivån för diagnostikloggar och länkar till scheman för varje tjänst.

## <a name="top-level-diagnostic-logs-schema"></a>Schema för diagnostiska loggar på högsta nivån

| Namn | Obligatorisk/valfri | Beskrivning |
|---|---|---|
| time | Krävs | Tids stämplingen (UTC) för händelsen. |
| resourceId | Krävs | Resurs-ID för den resurs som har orsakat händelsen. För klient tjänster är detta av formatet/Tenants/Tenant-ID/providers/Provider-Name. |
| TenantId | Krävs för klient loggar | Klient-ID för den Active Directory klient som den här händelsen är kopplad till. Den här egenskapen används bara för loggar på klient nivå, den visas inte i loggar på resurs nivå. |
| operationName | Krävs | Namnet på åtgärden som representeras av den här händelsen. Om händelsen representerar en RBAC-åtgärd är detta namnet på RBAC-åtgärden (t. ex. Microsoft. Storage/storageAccounts/blobServices/blobbar/Read). Vanligt vis modelleras i form av en Resource Manager-åtgärd, även om de inte är faktiska dokumenterade Resource Manager-åtgärder (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Valfritt | Den API-version som är kopplad till åtgärden, om operationName utfördes med hjälp av ett API (t. ex. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Om det inte finns något API som motsvarar den här åtgärden representerar-versionen den åtgärd som är associerad med åtgärden i framtiden. |
| category | Krävs | Händelsens logg kategori. Kategori är den granularitet som du kan använda för att aktivera eller inaktivera loggar för en viss resurs. Egenskaperna som visas i en händelses egenskaps-BLOB är desamma inom en viss logg kategori och resurs typ. Typiska logg kategorier är "granskning", "körning" och "begäran". |
| resultType | Valfritt | Händelsens status. Vanliga värden är startad, pågår, lyckades, misslyckades, aktivt och löst. |
| resultSignature | Valfritt | Händelsens under status. Om den här åtgärden motsvarar ett REST API-anrop är detta HTTP-statuskod för motsvarande REST-anrop. |
| resultDescription | Valfritt | Den statiska text beskrivningen för den här åtgärden, t. ex. "Hämta lagrings fil". |
| durationMs | Valfritt | Åtgärdens varaktighet i millisekunder. |
| callerIpAddress | Valfritt | IP-adressen för anroparen, om åtgärden motsvarar ett API-anrop som kommer från en entitet med en offentligt tillgänglig IP-adress. |
| correlationId | Valfritt | Ett GUID som används för att gruppera samman en uppsättning relaterade händelser. Normalt, om två händelser har samma operationName men två olika status värden (t. ex. "Startade" och "lyckades") delar samma korrelations-ID. Detta kan även representera andra relationer mellan händelser. |
| identitet | Valfritt | En JSON-blob som beskriver identiteten för den användare eller det program som utförde åtgärden. Detta inkluderar vanligt vis auktorisering och anspråk/JWT-token från Active Directory. |
| Nivå | Valfritt | Händelsens allvarlighets grad. Måste vara en av information, varning, fel eller kritisk. |
| location | Valfritt | Den region i resursen som avger händelsen, t. ex. "USA, östra" eller "Frankrike, södra" |
| properties | Valfritt | Eventuella utökade egenskaper som är relaterade till den här specifika kategorin av händelser. Alla anpassade/unika egenskaper måste placeras i det här "del B" av schemat. |

## <a name="service-specific-schemas-for-resource-diagnostic-logs"></a>Tjänstspecifika scheman för resurs diagnostiska loggar
Schemat för resurs diagnostiska loggar varierar beroende på resurs-och logg kategori. I den här listan visas alla tjänster som gör tillgängliga diagnostikloggar och länkar till tjänsten och det projektspecifika schemat där det är tillgängligt.

| Tjänst | Schema & dokument |
| --- | --- |
| Azure Active Directory | [Översikt](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), schema för [Gransknings logg](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) och [inloggnings tillägg](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [API Management diagnostikloggar](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Application Gateways |[Diagnostikloggning för Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automatisering |[Log Analytics för Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch diagnostisk loggning](../../batch/batch-diagnostics.md) |
| Azure-databas för MySQL | [Azure Database for MySQL diagnostikloggar](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure-databas för PostgreSQL | [Azure Database for PostgreSQL diagnostikloggar](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Datautforskaren i Azure | [Azure Datautforskaren diagnostikloggar](../../data-explorer/using-diagnostic-logs.md) |
| Kognitiva tjänster | [Diagnostisk loggning för Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Content Delivery Network | [Azure-diagnostikloggar för CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB loggning](../../cosmos-db/logging.md) |
| Data Factory | [Övervaka data fabriker med hjälp av Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Åtkomst till diagnostikloggar för Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Datasjölagring |[Åtkomst till diagnostikloggar för Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Händelsehubbar |[Azure Event Hubs diagnostikloggar](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | Schemat är inte tillgängligt. |
| Azure Firewall | Schemat är inte tillgängligt. |
| IoT Hub | [IoT Hub åtgärder](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure Key Vault-loggning](../../key-vault/key-vault-logging.md) |
| Kubernetes Service |[Azure Kubernetes-loggning](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Logganalys för Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logikappar |[Anpassat Logic Apps B2B-spårningsschema](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Nätverkssäkerhetsgrupper |[Log Analytics för nätverkssäkerhetsgrupper (NSG)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS Protection | [Hantera Azure DDoS Protection standard](../../virtual-network/manage-ddos-protection.md) |
| Dedikerad Power BI | [Diagnostisk loggning för Power BI Embedded i Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Data modell för Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Sök |[Aktivera och använda Sök Trafikanalys](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus diagnostikloggar](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database diagnostisk loggning](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Jobb diagnostiska loggar](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager logg schema](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuella nätverk | Schemat är inte tillgängligt. |
| Virtual Network-gatewayer | Schemat är inte tillgängligt. |

## <a name="supported-log-categories-per-resource-type"></a>Logg kategorier som stöds per resurs typ
|Resurstyp|Kategori|Kategori visnings namn|
|---|---|---|
|Microsoft. AnalysisServices/servers|Motorn|Motorn|
|Microsoft. AnalysisServices/servers|Tjänst|Tjänst|
|Microsoft.ApiManagement/service|GatewayLogs|Loggar som rör API Management-Gateway|
|Microsoft. Automation/automationAccounts|JobLogs|Jobb loggar|
|Microsoft. Automation/automationAccounts|JobStreams|Jobb strömmar|
|Microsoft. Automation/automationAccounts|DscNodeStatus|DSC-nods status|
|Microsoft. batch/batchAccounts|ServiceLog|Tjänst loggar|
|Microsoft. CDN/profiler/slut punkter|CoreAnalytics|Hämtar Mät värdena för slut punkten, t. ex. bandbredd, utgående data osv.|
|Microsoft. ClassicNetwork/networksecuritygroups|Regel flödes händelse för nätverks säkerhets grupp|Regel flödes händelse för nätverks säkerhets grupp|
|Microsoft. CognitiveServices/konton|Granska|Granskningsloggar|
|Microsoft. CognitiveServices/konton|RequestResponse|Förfrågningar och svars loggar|
|Microsoft. container service/managedClusters|kube-apiserver|Kubernetes-API-Server|
|Microsoft. container service/managedClusters|Kube-Controller-Manager|Kubernetes Controller Manager|
|Microsoft. container service/managedClusters|kluster – autoskalning|Kubernetes-kluster autoskalning|
|Microsoft. container service/managedClusters|Kube – Scheduler|Kubernetes Scheduler|
|Microsoft. container service/managedClusters|skyddade|Autentisering-webhook|
|Microsoft. CustomerInsights/hubbar|AuditEvents|AuditEvents|
|Microsoft. DataFactory/fabriker|ActivityRuns|Pipeline-aktivitet kör logg|
|Microsoft. DataFactory/fabriker|PipelineRuns|Pipeline kör logg|
|Microsoft. DataFactory/fabriker|TriggerRuns|Utlös körnings logg|
|Microsoft. DataLakeAnalytics/konton|Granska|Granskningsloggar|
|Microsoft. DataLakeAnalytics/konton|Begäranden|Begär ande loggar|
|Microsoft. DataLakeStore/konton|Granska|Granskningsloggar|
|Microsoft. DataLakeStore/konton|Begäranden|Begär ande loggar|
|Microsoft. DBforMySQL/servers|MySqlSlowLogs|MySQL server-loggar|
|Microsoft. DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL Server-loggar|
|Microsoft. Devices/IotHubs|Anslutningar|Anslutningar|
|Microsoft. Devices/IotHubs|DeviceTelemetry|Enhetstelemetri|
|Microsoft. Devices/IotHubs|C2DCommands|C2D-kommandon|
|Microsoft. Devices/IotHubs|DeviceIdentityOperations|Enhets identitets åtgärder|
|Microsoft. Devices/IotHubs|FileUploadOperations|Fil överförings åtgärder|
|Microsoft. Devices/IotHubs|Vägar|Vägar|
|Microsoft. Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft. Devices/IotHubs|C2DTwinOperations|C2D dubbla åtgärder|
|Microsoft. Devices/IotHubs|TwinQueries|Dubbla frågor|
|Microsoft. Devices/IotHubs|JobsOperations|Jobb åtgärder|
|Microsoft. Devices/IotHubs|DirectMethods|Direkta metoder|
|Microsoft. Devices/IotHubs|E2EDiagnostics|E2E-diagnostik (för hands version)|
|Microsoft. Devices/IotHubs|Konfigurationer|Konfigurationer|
|Microsoft. Devices/provisioningServices|DeviceOperations|Enhets åtgärder|
|Microsoft. Devices/provisioningServices|ServiceOperations|Tjänst åtgärder|
|Microsoft. DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft. DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft. DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft. EventHub/Namespaces|ArchiveLogs|Arkiv loggar|
|Microsoft. EventHub/Namespaces|OperationalLogs|Drift loggar|
|Microsoft. EventHub/Namespaces|AutoScaleLogs|Automatisk skalnings loggar|
|Microsoft. Insights/AutoscaleSettings|AutoscaleEvaluations|Autoskala-utvärderingar|
|Microsoft. Insights/AutoscaleSettings|AutoscaleScaleActions|Åtgärder för autoskalning av skala|
|Microsoft. IoTSpaces/Graph|Spårning|Spårning|
|Microsoft. IoTSpaces/Graph|Verksamhetsrelaterade|Verksamhetsrelaterade|
|Microsoft. IoTSpaces/Graph|Granska|Granska|
|Microsoft. IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft. IoTSpaces/Graph|Ingångshändelser|Ingångshändelser|
|Microsoft. IoTSpaces/Graph|Utgående|Utgående|
|Microsoft. nyckel valv/-valv|AuditEvent|Granskningsloggar|
|Microsoft. Logic/arbets flöden|WorkflowRuntime|Diagnostiska händelser för arbets flödes körning|
|Microsoft. Logic/integrationAccounts|IntegrationAccountTrackingEvents|Spårnings händelser för integrations konto|
|Microsoft. Network/networksecuritygroups|NetworkSecurityGroupEvent|Händelse för nätverks säkerhets grupp|
|Microsoft. Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Regel räknare för nätverks säkerhets grupp|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Load Balancer aviserings händelser|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Load Balancer avsökningens hälso status|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS skydds meddelanden|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Flödes loggar för DDoSa beslut om minskning|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Rapporter för DDoS-åtgärder|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Aviseringar för VM-skydd|
|Microsoft. Network/applicationGateways|ApplicationGatewayAccessLog|Application Gateway åtkomst logg|
|Microsoft. Network/applicationGateways|ApplicationGatewayPerformanceLog|Application Gateway prestanda logg|
|Microsoft. Network/applicationGateways|ApplicationGatewayFirewallLog|Application Gateway brand Väggs logg|
|Microsoft. Network/securegateways|AzureFirewallApplicationRule|Regel för Azure brand Väggs program|
|Microsoft. Network/securegateways|AzureFirewallNetworkRule|Nätverks regel för Azure Firewall|
|Microsoft. Network/azurefirewalls|AzureFirewallApplicationRule|Regel för Azure brand Väggs program|
|Microsoft. Network/azurefirewalls|AzureFirewallNetworkRule|Nätverks regel för Azure Firewall|
|Microsoft. Network/virtualNetworkGateways|GatewayDiagnosticLog|Gateway-diagnostikloggar|
|Microsoft. Network/virtualNetworkGateways|TunnelDiagnosticLog|Tunnel diagnostikloggar|
|Microsoft. Network/virtualNetworkGateways|RouteDiagnosticLog|Vidarebefordra diagnostikloggar|
|Microsoft. Network/virtualNetworkGateways|IKEDiagnosticLog|IKE-diagnostikloggar|
|Microsoft. Network/virtualNetworkGateways|P2SDiagnosticLog|P2S diagnostikloggar|
|Microsoft. Network/trafficManagerProfiles|ProbeHealthStatusEvents|Traffic Manager händelse av hälso resultat för avsökning|
|Microsoft. Network/expressRouteCircuits|PeeringRouteLog|Tabell loggar för peering-routning|
|Microsoft. Network/frontdoors|FrontdoorAccessLog|Ytterdörr åtkomst logg|
|Microsoft. Network/frontdoors|FrontdoorWebApplicationFirewallLog|Ytterdörr webb program brand Väggs logg|
|Microsoft. PowerBIDedicated/kapacitet|Motorn|Motorn|
|Microsoft. RecoveryServices/valv|AzureBackupReport|Azure Backup rapporterings data|
|Microsoft. RecoveryServices/valv|AzureSiteRecoveryJobs|Azure Site Recovery jobb|
|Microsoft. RecoveryServices/valv|AzureSiteRecoveryEvents|Azure Site Recovery händelser|
|Microsoft. RecoveryServices/valv|AzureSiteRecoveryReplicatedItems|Azure Site Recovery replikerade objekt|
|Microsoft. RecoveryServices/valv|AzureSiteRecoveryReplicationStats|Statistik för Azure Site Recovery-replikering|
|Microsoft. RecoveryServices/valv|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery återställnings punkter|
|Microsoft. RecoveryServices/valv|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery överförings hastighet för replikeringsdata|
|Microsoft. RecoveryServices/valv|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery skyddad disk data omsättning|
|Microsoft. search/searchServices|OperationLogs|Åtgärds loggar|
|Microsoft. Service Bus/namnrymder|OperationalLogs|Drift loggar|
|Microsoft. SQL/Servers/databaser|SQLInsights|SQL Insights|
|Microsoft. SQL/Servers/databaser|AutomaticTuning|Automatisk inställning|
|Microsoft. SQL/Servers/databaser|QueryStoreRuntimeStatistics|Körnings statistik för Query Store|
|Microsoft. SQL/Servers/databaser|QueryStoreWaitStatistics|Väntande statistik för Query Store|
|Microsoft. SQL/Servers/databaser|Fel|Fel|
|Microsoft. SQL/Servers/databaser|DatabaseWaitStatistics|Väntande statistik över databasen|
|Microsoft. SQL/Servers/databaser|Timeouter|Timeouter|
|Microsoft. SQL/Servers/databaser|Delar|Delar|
|Microsoft. SQL/Servers/databaser|Låsningar|Låsningar|
|Microsoft. SQL/Servers/databaser|Granska|Granskningsloggar|
|Microsoft. SQL/Servers/databaser|SQLSecurityAuditEvents|Säkerhets gransknings händelse i SQL|
|Microsoft. SQL/Servers/databaser|DmsWorkers|DMS-arbetare|
|Microsoft. SQL/Servers/databaser|ExecRequests|Exec-begäranden|
|Microsoft. SQL/Servers/databaser|RequestSteps|Förfrågnings steg|
|Microsoft. SQL/Servers/databaser|SqlRequests|SQL-begäranden|
|Microsoft. SQL/Servers/databaser|Väntar|Väntar|
|Microsoft. SQL/managedInstances|ResourceUsageStats|Statistik för resursanvändning|
|Microsoft. SQL/managedInstances|SQLSecurityAuditEvents|Säkerhets gransknings händelse i SQL|
|Microsoft. SQL/managedInstances/-databaser|SQLInsights|SQL Insights|
|Microsoft. SQL/managedInstances/-databaser|QueryStoreRuntimeStatistics|Körnings statistik för Query Store|
|Microsoft. SQL/managedInstances/-databaser|QueryStoreWaitStatistics|Väntande statistik för Query Store|
|Microsoft. SQL/managedInstances/-databaser|Fel|Fel|
|Microsoft. StreamAnalytics/streamingjobs|Körning|Körning|
|Microsoft. StreamAnalytics/streamingjobs|Redigering|Redigering|
|Microsoft. Web/Sites|FunctionExecutionLogs|Funktions körnings loggar|
|Microsoft. Web/Sites/lotss|FunctionExecutionLogs|Funktions körnings loggar|

## <a name="next-steps"></a>Nästa steg

* [Läs mer om diagnostikloggar](../../azure-monitor/platform/resource-logs-overview.md)
* [Strömma resurs diagnostiska loggar till **Event Hubs**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Ändra inställningarna för resurs diagnostik med Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analysera loggar från Azure Storage med Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
