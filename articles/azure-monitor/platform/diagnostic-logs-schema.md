---
title: Tjänster och scheman som stöds av Azure-resurs loggar
description: Förstå de tjänster och händelse scheman som stöds för Azure Diagnostic-loggar.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
author: rboucher
ms.author: robb
ms.openlocfilehash: 22521a3619482361c8f556b05436bb3b78c7dc9b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932331"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Tjänster, scheman och kategorier som stöds för Azure-resurs loggar

> [!NOTE]
> Resurs loggar kallades tidigare för diagnostikloggar.

[Azure Monitor resurs loggar](../../azure-monitor/platform/resource-logs-overview.md) genereras av Azure-tjänster som beskriver driften av dessa tjänster eller resurser. Alla resurs loggar som är tillgängliga via Azure Monitor dela ett gemensamt schema på högsta nivå, med flexibilitet för varje tjänst för att generera unika egenskaper för sina egna händelser.

En kombination av resurs typen (tillgänglig i egenskapen `resourceId`) och `category` unikt identifierar ett schema. Den här artikeln beskriver schemat på högsta nivån för resurs loggar och länkar till scheman för varje tjänst.

## <a name="top-level-resource-logs-schema"></a>Schema för resurs loggar på högsta nivån

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

## <a name="service-specific-schemas-for-resource-logs"></a>Tjänstspecifika scheman för resurs loggar
Schemat för resurs diagnostiska loggar varierar beroende på resurs-och logg kategori. I den här listan visas alla tjänster som gör tillgängliga resurs loggar och länkar till tjänsten och det projektspecifika schemat där det är tillgängligt.

| Tjänst | Schema & dokument |
| --- | --- |
| Azure Active Directory | [Översikt](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), schema för [Gransknings logg](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) och [inloggnings tillägg](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [API Management resurs loggar](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Application Gateways |[Loggning för Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automatisering |[Log Analytics för Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch loggning](../../batch/batch-diagnostics.md) |
| Azure-databas för MySQL | [Azure Database for MySQL diagnostikloggar](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure-databas för PostgreSQL | [Azure Database for PostgreSQL loggar](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Datautforskaren i Azure | [Azure Datautforskaren-loggar](../../data-explorer/using-diagnostic-logs.md) |
| Kognitiva tjänster | [Loggning för Azure-Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Content Delivery Network | [Azure-loggar för CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB loggning](../../cosmos-db/logging.md) |
| Data Factory | [Övervaka data fabriker med hjälp av Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Åtkomst till loggar för Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Datasjölagring |[Åtkomst till loggar för Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Händelsehubbar |[Azure Event Hubs-loggar](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | Schemat är inte tillgängligt. |
| Azure Firewall | Schemat är inte tillgängligt. |
| IoT Hub | [IoT Hub åtgärder](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure Key Vault-loggning](../../key-vault/key-vault-logging.md) |
| Kubernetes Service |[Azure Kubernetes-loggning](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Logganalys för Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logikappar |[Anpassat Logic Apps B2B-spårningsschema](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Nätverkssäkerhetsgrupper |[Log Analytics för nätverkssäkerhetsgrupper (NSG)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS Protection | [Hantera Azure DDoS Protection standard](../../virtual-network/manage-ddos-protection.md) |
| Dedikerad Power BI | [Loggning för Power BI Embedded i Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Data modell för Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Sök |[Aktivera och använda Sök Trafikanalys](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus loggar](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database loggning](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Jobb loggar](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager logg schema](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuella nätverk | Schemat är inte tillgängligt. |
| Virtual Network-gatewayer | Schemat är inte tillgängligt. |

## <a name="supported-log-categories-per-resource-type"></a>Logg kategorier som stöds per resurs typ
|Resurstyp|Kategori|Kategori visnings namn|
|---|---|---|
|Microsoft. AAD/domainServices|SystemSecurity|SystemSecurity|
|Microsoft. AAD/domainServices|AccountManagement|AccountManagement|
|Microsoft. AAD/domainServices|LogonLogoff|LogonLogoff|
|Microsoft. AAD/domainServices|ObjectAccess|ObjectAccess|
|Microsoft. AAD/domainServices|PolicyChange|PolicyChange|
|Microsoft. AAD/domainServices|PrivilegeUse|PrivilegeUse|
|Microsoft. AAD/domainServices|DetailTracking|DetailTracking|
|Microsoft. AAD/domainServices|DirectoryServiceAccess|DirectoryServiceAccess|
|Microsoft. AAD/domainServices|AccountLogon|AccountLogon|
|Microsoft. aadiam/klient organisationer|Inloggning|Inloggning|
|Microsoft. AnalysisServices/servers|Motorn|Motorn|
|Microsoft. AnalysisServices/servers|Tjänst|Tjänst|
|Microsoft.ApiManagement/service|GatewayLogs|Loggar som rör API Management-Gateway|
|Microsoft. AppPlatform/våren|ApplicationConsole|Program konsol|
|Microsoft. Automation/automationAccounts|JobLogs|Jobb loggar|
|Microsoft. Automation/automationAccounts|JobStreams|Jobb strömmar|
|Microsoft. Automation/automationAccounts|DscNodeStatus|DSC-nods status|
|Microsoft. batch/batchAccounts|ServiceLog|Tjänst loggar|
|Microsoft. BatchAI/arbets ytor|BaiClusterEvent|BaiClusterEvent|
|Microsoft. BatchAI/arbets ytor|BaiClusterNodeEvent|BaiClusterNodeEvent|
|Microsoft. BatchAI/arbets ytor|BaiJobEvent|BaiJobEvent|
|Microsoft. blockchain/blockchainMembers|BlockchainApplication|Blockchain-program|
|Microsoft. blockchain/blockchainMembers|Proxy|Proxy|
|Microsoft. CDN/profiler/slut punkter|CoreAnalytics|Hämtar Mät värdena för slut punkten, t. ex. bandbredd, utgående data osv.|
|Microsoft. ClassicNetwork/networksecuritygroups|Regel flödes händelse för nätverks säkerhets grupp|Regel flödes händelse för nätverks säkerhets grupp|
|Microsoft. CognitiveServices/konton|Granska|Granskningsloggar|
|Microsoft. CognitiveServices/konton|RequestResponse|Förfrågningar och svars loggar|
|Microsoft. ContainerRegistry/register|ContainerRegistryRepositoryEvents|RepositoryEvent-loggar|
|Microsoft. ContainerRegistry/register|ContainerRegistryLoginEvents|Inloggnings händelser|
|Microsoft. container service/managedClusters|kube-apiserver|Kubernetes-API-Server|
|Microsoft. container service/managedClusters|Kube-Controller-Manager|Kubernetes Controller Manager|
|Microsoft. container service/managedClusters|Kube – Scheduler|Kubernetes Scheduler|
|Microsoft. container service/managedClusters|Kube – granskning|Kubernetes granskning|
|Microsoft. container service/managedClusters|kluster – autoskalning|Kubernetes-kluster autoskalning|
|Microsoft. Databricks/arbets ytor|dBFS|Databricks-filsystem|
|Microsoft. Databricks/arbets ytor|kluster|Databricks-kluster|
|Microsoft. Databricks/arbets ytor|konton|Databricks-konton|
|Microsoft. Databricks/arbets ytor|Utskrifts|Databricks-jobb|
|Microsoft. Databricks/arbets ytor|1150|Databricks-anteckningsbok|
|Microsoft. Databricks/arbets ytor|SSH|Databricks SSH|
|Microsoft. Databricks/arbets ytor|Platsen|Databricks-arbetsyta|
|Microsoft. Databricks/arbets ytor|hemligheter|Databricks hemligheter|
|Microsoft. Databricks/arbets ytor|sqlPermissions|Databricks SQLPermissions|
|Microsoft. Databricks/arbets ytor|instancePools|Instanspooler|
|Microsoft. DataCatalog/datacatalogs|ScanStatusLogEvent|ScanStatus|
|Microsoft. DataFactory/fabriker|ActivityRuns|Pipeline-aktivitet kör logg|
|Microsoft. DataFactory/fabriker|PipelineRuns|Pipeline kör logg|
|Microsoft. DataFactory/fabriker|TriggerRuns|Utlös körnings logg|
|Microsoft. DataLakeAnalytics/konton|Granska|Granskningsloggar|
|Microsoft. DataLakeAnalytics/konton|Begäranden|Begär ande loggar|
|Microsoft. DataLakeStore/konton|Granska|Granskningsloggar|
|Microsoft. DataLakeStore/konton|Begäranden|Begär ande loggar|
|Microsoft. DataShare/konton|Resurser|Resurser|
|Microsoft. DataShare/konton|ShareSubscriptions|Dela prenumerationer|
|Microsoft. DataShare/konton|SentShareSnapshots|Ögonblicks bilder av skickade resurser|
|Microsoft. DataShare/konton|ReceivedShareSnapshots|Mottagna resurs ögonblicks bilder|
|Microsoft. DBforMySQL/servers|MySqlSlowLogs|MySQL server-loggar|
|Microsoft. DBforMySQL/servers|MySqlAuditLogs|MySQL gransknings loggar|
|Microsoft. DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL Server-loggar|
|Microsoft. DBforPostgreSQL/servers|QueryStoreRuntimeStatistics|PostgreSQL för fråge lagrings körning|
|Microsoft. DBforPostgreSQL/servers|QueryStoreWaitStatistics|Väntande statistik för PostgreSQL Query Store|
|Microsoft. DBforPostgreSQL/serversv2|PostgreSQLLogs|PostgreSQL Server-loggar|
|Microsoft. DBforPostgreSQL/serversv2|QueryStoreRuntimeStatistics|PostgreSQL för fråge lagrings körning|
|Microsoft. DBforPostgreSQL/serversv2|QueryStoreWaitStatistics|Väntande statistik för PostgreSQL Query Store|
|Microsoft. DesktopVirtualization/arbets ytor|Checkpoint|Checkpoint|
|Microsoft. DesktopVirtualization/arbets ytor|Fel|Fel|
|Microsoft. DesktopVirtualization/arbets ytor|Förvaltning|Förvaltning|
|Microsoft. DesktopVirtualization/arbets ytor|Feed|Feed|
|Microsoft. DesktopVirtualization/applicationGroups|Checkpoint|Checkpoint|
|Microsoft. DesktopVirtualization/applicationGroups|Fel|Fel|
|Microsoft. DesktopVirtualization/applicationGroups|Förvaltning|Förvaltning|
|Microsoft. DesktopVirtualization/hostPools|Checkpoint|Checkpoint|
|Microsoft. DesktopVirtualization/hostPools|Fel|Fel|
|Microsoft. DesktopVirtualization/hostPools|Förvaltning|Förvaltning|
|Microsoft. DesktopVirtualization/hostPools|Anslutning|Anslutning|
|Microsoft. DesktopVirtualization/hostPools|HostRegistration|HostRegistration|
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
|Microsoft. Devices/IotHubs|DistributedTracing|Distribuerad spårning (för hands version)|
|Microsoft. Devices/IotHubs|Konfigurationer|Konfigurationer|
|Microsoft. Devices/IotHubs|DeviceStreams|Enhets strömmar (förhands granskning)|
|Microsoft. Devices/provisioningServices|DeviceOperations|Enhets åtgärder|
|Microsoft. Devices/provisioningServices|ServiceOperations|Tjänst åtgärder|
|Microsoft. DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft. DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft. DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft. DocumentDB/databaseAccounts|PartitionKeyStatistics|PartitionKeyStatistics|
|Microsoft. DocumentDB/databaseAccounts|ControlPlaneRequests|ControlPlaneRequests|
|Microsoft. EnterpriseKnowledgeGraph/Services|AuditEvent|AuditEvent-logg|
|Microsoft. EnterpriseKnowledgeGraph/Services|DataIssue|DataIssue-logg|
|Microsoft. EnterpriseKnowledgeGraph/Services|Begäranden|Konfigurations logg|
|Microsoft. EventHub/Namespaces|ArchiveLogs|Arkiv loggar|
|Microsoft. EventHub/Namespaces|OperationalLogs|Drift loggar|
|Microsoft. EventHub/Namespaces|AutoScaleLogs|Automatisk skalnings loggar|
|Microsoft. EventHub/Namespaces|KafkaCoordinatorLogs|Kafka koordinator loggar|
|Microsoft. EventHub/Namespaces|KafkaUserErrorLogs|Kafka användar fel loggar|
|Microsoft. EventHub/Namespaces|EventHubVNetConnectionEvent|Anslutnings loggar för VNet/IP-filtrering|
|Microsoft. EventHub/Namespaces|CustomerManagedKeyUserLogs|Kund hanterade nyckel loggar|
|Microsoft. HealthcareApis/Services|AuditLogs|Granskningsloggar|
|Microsoft. Insights/AutoscaleSettings|AutoscaleEvaluations|Autoskala-utvärderingar|
|Microsoft. Insights/AutoscaleSettings|AutoscaleScaleActions|Åtgärder för autoskalning av skala|
|Microsoft. IoTSpaces/Graph|Spårning|Spårning|
|Microsoft. IoTSpaces/Graph|Verksamhetsrelaterade|Verksamhetsrelaterade|
|Microsoft. IoTSpaces/Graph|Granska|Granska|
|Microsoft. IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft. IoTSpaces/Graph|Ingångshändelser|Ingångshändelser|
|Microsoft. IoTSpaces/Graph|Utgående|Utgående|
|Microsoft. nyckel valv/-valv|AuditEvent|Granskningsloggar|
|Microsoft. Kusto/kluster|SucceededIngestion|Framgångs åtgärder|
|Microsoft. Kusto/kluster|FailedIngestion|Misslyckade inmatnings åtgärder|
|Microsoft. Logic/arbets flöden|WorkflowRuntime|Diagnostiska händelser för arbets flödes körning|
|Microsoft. Logic/integrationAccounts|IntegrationAccountTrackingEvents|Spårnings händelser för integrations konto|
|Microsoft. MachineLearningServices/arbets ytor|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Microsoft. MachineLearningServices/arbets ytor|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|Microsoft. MachineLearningServices/arbets ytor|AmlComputeJobEvent|AmlComputeJobEvent|
|Microsoft. Media/Media Services|KeyDeliveryRequests|Begär Anden om nyckel leverans|
|Microsoft. Network/networksecuritygroups|NetworkSecurityGroupEvent|Händelse för nätverks säkerhets grupp|
|Microsoft. Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Regel räknare för nätverks säkerhets grupp|
|Microsoft. Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Regel flödes händelse för nätverks säkerhets grupp|
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
|Microsoft. Network/vpnGateways|GatewayDiagnosticLog|Gateway-diagnostikloggar|
|Microsoft. Network/vpnGateways|TunnelDiagnosticLog|Tunnel diagnostikloggar|
|Microsoft. Network/vpnGateways|RouteDiagnosticLog|Vidarebefordra diagnostikloggar|
|Microsoft. Network/vpnGateways|IKEDiagnosticLog|IKE-diagnostikloggar|
|Microsoft. Network/frontdoors|FrontdoorAccessLog|Ytterdörr åtkomst logg|
|Microsoft. Network/frontdoors|FrontdoorWebApplicationFirewallLog|Ytterdörr webb program brand Väggs logg|
|Microsoft. Network/p2sVpnGateways|GatewayDiagnosticLog|Gateway-diagnostikloggar|
|Microsoft. Network/p2sVpnGateways|IKEDiagnosticLog|IKE-diagnostikloggar|
|Microsoft. Network/p2sVpnGateways|P2SDiagnosticLog|P2S diagnostikloggar|
|Microsoft. Network/bastionHosts|BastionAuditLogs|Gransknings loggar för skydds|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Load Balancer aviserings händelser|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Load Balancer avsökningens hälso status|
|Microsoft. PowerBIDedicated/kapacitet|Motorn|Motorn|
|Microsoft. RecoveryServices/valv|AzureBackupReport|Azure Backup rapporterings data|
|Microsoft. RecoveryServices/valv|CoreAzureBackup|Kärn Azure Backup data|
|Microsoft. RecoveryServices/valv|AddonAzureBackupJobs|Jobb data för addon Azure Backup|
|Microsoft. RecoveryServices/valv|AddonAzureBackupAlerts|Aviserings data för addon Azure Backup|
|Microsoft. RecoveryServices/valv|AddonAzureBackupPolicy|Princip data för addon Azure Backup|
|Microsoft. RecoveryServices/valv|AddonAzureBackupStorage|Tillägg för Azure Backup lagrings data|
|Microsoft. RecoveryServices/valv|AddonAzureBackupProtectedInstance|Addon Azure Backup skyddade instans data|
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
|Microsoft. Storage/storageAccounts/tableServices|StorageRead|StorageRead|
|Microsoft. Storage/storageAccounts/tableServices|StorageWrite|StorageWrite|
|Microsoft. Storage/storageAccounts/tableServices|StorageDelete|StorageDelete|
|Microsoft. Storage/storageAccounts/blobServices|StorageRead|StorageRead|
|Microsoft. Storage/storageAccounts/blobServices|StorageWrite|StorageWrite|
|Microsoft. Storage/storageAccounts/blobServices|StorageDelete|StorageDelete|
|Microsoft. Storage/storageAccounts/fileServices|StorageRead|StorageRead|
|Microsoft. Storage/storageAccounts/fileServices|StorageWrite|StorageWrite|
|Microsoft. Storage/storageAccounts/fileServices|StorageDelete|StorageDelete|
|Microsoft. Storage/storageAccounts/queueServices|StorageRead|StorageRead|
|Microsoft. Storage/storageAccounts/queueServices|StorageWrite|StorageWrite|
|Microsoft. Storage/storageAccounts/queueServices|StorageDelete|StorageDelete|
|Microsoft. StreamAnalytics/streamingjobs|Körning|Körning|
|Microsoft. StreamAnalytics/streamingjobs|Redigering|Redigering|
|Microsoft. Web/hostingenvironments|AppServiceEnvironmentPlatformLogs|App Service-miljön plattforms loggar|
|Microsoft. Web/Sites|FunctionAppLogs|Funktions program loggar|
|Microsoft. Web/Sites|AppServiceHTTPLogs|HTTP-loggar|
|Microsoft. Web/Sites|AppServiceConsoleLogs|App Service konsol loggar|
|Microsoft. Web/Sites|AppServiceAppLogs|App Service program loggar|
|Microsoft. Web/Sites|AppServiceFileAuditLogs|Gransknings loggar för ändring av webbplats innehåll|
|Microsoft. Web/Sites|AppServiceAuditLogs|Åtkomst gransknings loggar|
|Microsoft. Web/Sites/lotss|FunctionAppLogs|Funktions program loggar|
|Microsoft. Web/Sites/lotss|AppServiceHTTPLogs|HTTP-loggar|
|Microsoft. Web/Sites/lotss|AppServiceConsoleLogs|Konsol loggar|
|Microsoft. Web/Sites/lotss|AppServiceAppLogs|Programloggar|
|Microsoft. Web/Sites/lotss|AppServiceFileAuditLogs|Gransknings loggar för ändring av webbplats innehåll|
|Microsoft. Web/Sites/lotss|AppServiceAuditLogs|Åtkomst gransknings loggar|

## <a name="next-steps"></a>Nästa steg

* [Läs mer om resurs loggar](../../azure-monitor/platform/resource-logs-overview.md)
* [Strömma resurs resurs loggar till **Event Hubs**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Ändra diagnostikinställningar för resurs loggen med hjälp av Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analysera loggar från Azure Storage med Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
